---
name: structured-data-audit
description: Audit top pages for structured-data errors and flag FAQ/HowTo
  schema opportunities from query phrasing. Use when asked about schema
  markup, rich results, or structured data errors.
---

# Structured Data Audit

Find which high-traffic pages have structured-data errors, and which pages
could win FAQ or HowTo rich results but don't have that schema yet.

This skill is read-only: it only calls GenieSEO reporting tools and never
submits, deletes, or changes anything in the user's account.

## Steps

1. Call `list_properties` to confirm the exact `site_url`.
2. Call `get_advanced_search_analytics` with `dimensions=page`,
   `sort_by=impressions`, `sort_direction=descending`, `row_limit=30`,
   `start_date` = 28 days ago, `end_date` = today, to get the top 30 pages
   by impressions. Record each page's impressions, clicks, and average
   position.
3. Call `batch_url_inspection` on those 30 URLs (up to 10 per call, one URL
   per line in `urls` — run it three times to cover all 30). Read each
   result's `rich_results` field — the rich-result types Google detected and
   any issues it reported for them. For each page, record:
   - The detected rich-result types (e.g. FAQ, HowTo, Breadcrumb, Product)
   - Any errors or warnings listed for them
   - Mark the page `SCHEMA_ERROR` if any error was reported

   A `rich_results` value of `None`/null means Google detected no eligible
   rich results — report it as "none detected", not as proof that the page
   has no markup at all.
4. For every page without a detected FAQ rich result, call
   `get_search_by_page_query` with `page_url` = the page, `days=28`, and
   `row_limit=5` to get its top 5 queries. Flag the page `FAQ_CANDIDATE` if
   any of those queries starts with Who, What, Why, When, How, Is, Are, or
   Can.
5. For every page without a detected HowTo rich result, flag it
   `HOWTO_CANDIDATE` if any of its top 5 queries contains the phrase
   "how to".

## Output format

A Markdown table with columns: page URL, impressions, detected rich-result
types, errors (yes/no), FAQ candidate (yes/no), HowTo candidate (yes/no).
Follow it with two subsections: one listing only the `SCHEMA_ERROR` pages
with their specific errors, and one listing the `FAQ_CANDIDATE` /
`HOWTO_CANDIDATE` pages with their triggering queries.
