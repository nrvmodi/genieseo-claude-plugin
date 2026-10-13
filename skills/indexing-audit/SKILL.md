---
name: indexing-audit
description: Audit indexing status across top pages. Use when asked about crawling,
  indexing issues, or whether pages are indexed by Google.
---

# Indexing Audit

Audit the indexing status of the top pages on a site and produce a prioritized action list.

This skill is read-only: it only calls GenieSEO reporting and URL-inspection
tools and never submits, deletes, or changes anything in the user's account.

## Steps

1. Call `list_properties` to confirm the exact `site_url`.
2. Call `get_advanced_search_analytics` with `dimensions=page`,
   `sort_by=impressions`, `sort_direction=descending`, `row_limit=20`,
   `start_date` = 28 days ago, `end_date` = today, to get the 20 most-visible
   pages. (`get_search_analytics` has no sort option, so don't use it here.)
3. Extract the list of page URLs from the results. If there are fewer than 20,
   audit the ones returned.
4. Call `batch_url_inspection` with up to 10 URLs per call (one URL per line
   in `urls`). Run it twice if needed to cover all pages. Each result has
   `verdict`, `coverage_state`, and `last_crawled`.
5. For every URL whose `verdict` is not `PASS`, call `inspect_url_enhanced`
   to get the detail fields: `page_fetch_state`, `robots_txt_state`,
   `indexing_state`, `google_canonical`, and `user_canonical`.
6. Categorize each URL:
   - ✅ **Indexed** — `verdict` = `PASS`
   - 🔍 **Canonical mismatch** — `coverage_state` mentions Google choosing a
     different canonical, or `google_canonical` differs from `user_canonical`
   - ❌ **Not indexed / Blocked** — `robots_txt_state` = `DISALLOWED`,
     `indexing_state` is a noindex state, or `page_fetch_state` is an error
     (e.g. `SERVER_ERROR`, `NOT_FOUND`)
   - ⚠️ **Soft 404 / Excluded** — any other non-PASS `coverage_state`
7. For each issue, quote the exact field values returned by the tools. Never
   infer a status the tools didn't report.

## Output format

Present as a prioritized action list:

1. **Critical** — Not indexed pages that have impressions (visibility being lost)
2. **High** — Canonical mismatches on high-traffic pages
3. **Medium** — Robots.txt or fetch blocks
4. **Low** — Soft exclusions on low-traffic pages

Include a summary table: page URL | impressions | verdict | coverage state |
issue | recommended action. If every page passes, say so plainly.
