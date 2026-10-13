---
name: revenue-attribution
description: Cross-reference Search Console queries with GA4 conversions and
  revenue to find which keywords actually drive business outcomes. Use when
  asked about SEO ROI, conversion value, or which queries convert.
---

# Revenue Attribution

Combine Search Console and GA4 to map organic queries to conversions and
revenue — the report that proves SEO ROI.

This skill is read-only: it only calls GenieSEO reporting tools and never
submits, deletes, or changes anything in the user's account.

## Steps

1. Call `list_properties` to confirm the exact `site_url`.
2. Call `get_advanced_search_analytics` with `dimensions=page`,
   `sort_by=clicks`, `sort_direction=descending`, `row_limit=20`,
   `start_date` = 28 days ago, `end_date` = today, to get the top 20
   organic landing pages by clicks.
3. For each of those 20 pages, call `get_search_by_page_query` with
   `page_url` = the page, `days=28`, and `row_limit=3` to retrieve its top 3
   queries (clicks, impressions, average position per query).
4. Call `list_ga4_properties` to identify the GA4 property that matches this
   Search Console site. If more than one property could match, list the
   candidates and ask the user to confirm before continuing.
5. Call `run_ga4_report` once with `property_id` = the confirmed GA4
   property's numeric ID, `start_date="28daysAgo"`, `end_date="today"`,
   `dimensions=["hostName", "pagePath"]`,
   `metrics=["sessions", "conversions", "totalRevenue"]`,
   `dimension_filter_field="sessionDefaultChannelGroup"`,
   `dimension_filter_operator="exact"`,
   `dimension_filter_value="Organic Search"`, and `limit=1000`. Match each
   of the 20 landing pages to the row with the same `hostName` and
   `pagePath` (ignore scheme and query string; a domain property can span
   several subdomains, so the host must match too). If the property doesn't
   support the `conversions` metric (some GA4 properties have migrated to
   `keyEvents` and no longer support the legacy `conversions` metric),
   retry the call with `metrics=["sessions", "keyEvents", "totalRevenue"]`
   and use `keyEvents` as the conversions figure instead. If revenue
   events aren't configured for the property, report `totalRevenue = 0`
   and note it explicitly rather than omitting the row.
6. Join the Search Console and GA4 data on host + page path.
7. Flag any row where Search Console clicks > 100 AND the GA4 conversion
   rate < 1% with the label `CONVERSION_OPPORTUNITY` — high-traffic,
   low-conversion pages are the highest-leverage rewrite candidates.

## Output format

A single Markdown table sorted by GA4 conversion value descending, with
columns: landing page, top query, SC clicks, SC avg position, GA4 sessions,
GA4 conversions, conversion rate %, GA4 revenue, flag. Follow the table with
a short list of every row flagged `CONVERSION_OPPORTUNITY`.
