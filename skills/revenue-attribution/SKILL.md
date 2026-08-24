---
name: revenue-attribution
description: Cross-reference Search Console queries with GA4 conversions and
  revenue to find which keywords actually drive business outcomes. Use when
  asked about SEO ROI, conversion value, or which queries convert.
---

# Revenue Attribution

Combine Search Console and GA4 to map organic queries to conversions and
revenue — the report that proves SEO ROI.

## Steps

1. Call `list_properties` to confirm the exact `site_url`.
2. Call `get_advanced_search_analytics` with `dimensions=page`,
   `sort_by=clicks`, `sort_direction=descending`, `row_limit=20`,
   `start_date` = 28 days ago, `end_date` = today, to get the top 20
   organic landing pages by clicks.
3. For each of those 20 pages, call `get_search_by_page_query` for the same
   date range to retrieve its top 3 queries (clicks, impressions, average
   position per query).
4. Call `list_ga4_properties` to identify the GA4 property that matches this
   Search Console site. If more than one property could match, list the
   candidates and ask the user to confirm before continuing.
5. Call `run_ga4_report` once for the same 28-day window with
   `dimensions=["pagePath", "sessionDefaultChannelGroup"]`,
   `metrics=["sessions", "conversions", "totalRevenue"]`, and `limit` set
   high enough to cover all pages (e.g. 1000). The tool has no server-side
   filter, so filter the RETURNED rows yourself: keep only rows where
   `sessionDefaultChannelGroup` equals "Organic Search", then aggregate by
   `pagePath` for each of the 20 landing pages. If the property doesn't
   support the `conversions` metric (some GA4 properties have migrated to
   `keyEvents` and no longer support the legacy `conversions` metric),
   retry the call with `metrics=["sessions", "keyEvents", "totalRevenue"]`
   and use `keyEvents` as the conversions figure instead. If revenue
   events aren't configured for the property, report `totalRevenue = 0`
   and note it explicitly rather than omitting the row.
6. Join the Search Console and GA4 data on page path.
7. Flag any row where Search Console clicks > 100 AND the GA4 conversion
   rate < 1% with the label `CONVERSION_OPPORTUNITY` — high-traffic,
   low-conversion pages are the highest-leverage rewrite candidates.

## Output format

A single Markdown table sorted by GA4 conversion value descending, with
columns: landing page, top query, SC clicks, SC avg position, GA4 sessions,
GA4 conversions, conversion rate %, GA4 revenue, flag. Follow the table with
a short list of every row flagged `CONVERSION_OPPORTUNITY`.
