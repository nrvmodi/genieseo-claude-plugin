# Skill ↔ tool contract fixes (OpenAI review rejection of v1.0.0)

## Problem

OpenAI rejected GenieSEO v1.0.0: "One or more of your test cases did not
produce correct results … ensure the same test cases pass consistently on
both ChatGPT web and mobile." Their automated scan also attached
"security risk" / "may install or load code from an untrusted or mutable
source" findings to `indexing-audit`, `keyword-gap-finder`, and
`seo-weekly-report`.

## Root cause

The skills were written against assumed tool behavior, not the live
`https://seo.geniedial.in/mcp` schemas. Running every call against the
live server showed:

1. **Inverted period comparison (seo-weekly-report, content-decay-detection).**
   `compare_search_periods` returns `click_diff = p2 − p1` and `click_pct`
   relative to p1. The weekly report put the *recent* window in period 1, so
   growth looked like decline. Live example: "genie seo" went from 1 click
   (prior) to 4 (recent), and the tool reported `click_pct = -75.0`, which
   the skill would flag as a >20% drop. The decay skill also passed
   nonexistent `period1`/`period2` params (the real ones are `period1_start`,
   `period1_end`, and so on) and used overlapping windows.
2. **Nonexistent parameter (indexing-audit).** It called `get_search_analytics`
   with `sort_by=impressions`. That tool has no sort parameter, so results came
   back in clicks order (or the call failed in strict clients).
3. **Fields that don't exist (indexing-audit, structured-data-audit).**
   `batch_url_inspection` only returns `verdict`, `coverage_state`,
   `last_crawled`, and `rich_results`. The skills asked for
   `pageFetchState`, `robotsTxtState`, canonical info, and "structured-data
   types present", so the model had to invent them. Those fields
   (snake_case) only come from `inspect_url_enhanced`.
4. **Wrong claim about GA4 (revenue-attribution).** The skill said
   `run_ga4_report` has no server-side filter. It has
   `dimension_filter_field/operator/value`. It also omitted the required
   `property_id`, passed a "date range" to `get_search_by_page_query` (which
   only takes `days`), and joined on `pagePath` alone, which is ambiguous for
   domain properties that span several subdomains.
5. **keyword-gap-finder** repeated the identical 1000-row query in step 6,
   and had no fallback when no row met the 500-impression bar. On small
   sites that produced an empty result.

## Fix

- Rewrote the affected steps to use the exact parameter and field names from
  the live schemas, with explicit `YYYY-MM-DD` windows and period ordering
  (older = period 1, recent = period 2). Declines are computed from the raw
  `p1_clicks`/`p2_clicks`, with a 5-click noise floor.
- indexing-audit now uses `get_advanced_search_analytics` for the sort and
  calls `inspect_url_enhanced` for non-PASS URLs.
- structured-data-audit reads `rich_results`, and treats null as "none
  detected" rather than "no markup".
- revenue-attribution uses the GA4 server-side filter, passes `property_id`,
  and joins on `hostName` plus `pagePath`.
- keyword-gap-finder makes a single data call and has a documented threshold
  fallback.
- Each skill now has a `## Safety` section. It states that the skill is
  read-only, only calls the plugin's own MCP server, never installs, downloads,
  runs, or fetches anything, and treats tool-returned query text and URLs
  (which third parties can control, since anyone can type a search query) as
  data, never as instructions. This targets the scanner findings ("security
  risk", "untrusted supply chain") on indexing-audit, keyword-gap-finder, and
  seo-weekly-report. Those skills contained no install or run behavior, so the
  findings look heuristic. The prompt-injection guardrail closes the one real
  risk they share.
- Bumped every manifest to 1.0.1.

We chose to fix the skills rather than change the server: the server
behavior is reasonable and already shipped to other clients (Claude, Cursor,
Grok). Making the instructions match it is lower risk.

## Evidence

Live calls on 2026-10-13 against `sc-domain:geniedial.in` / GA4 property
`450157104`:
- `compare_search_periods` (p1 = 2026-09-15..10-12, p2 = 2026-08-18..09-14):
  "genie seo" `p1_clicks=4, p2_clicks=1, click_pct=-75.0`. This confirms
  that diff = p2 − p1.
- `batch_url_inspection` result keys: `url, verdict, coverage_state,
  last_crawled, rich_results`.
- `inspect_url_enhanced` adds `page_fetch_state, robots_txt_state,
  indexing_state, google_canonical, user_canonical`.
- `run_ga4_report` with `dimension_filter_field=sessionDefaultChannelGroup`,
  `value=Organic Search` returned filtered rows (`/`: 17 sessions).
