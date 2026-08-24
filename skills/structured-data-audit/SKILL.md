---
name: structured-data-audit
description: Audit top pages for structured-data errors and flag FAQ/HowTo
  schema opportunities from query phrasing. Use when asked about schema
  markup, rich results, or structured data errors.
---

# Structured Data Audit

Find which high-traffic pages have structured-data errors, and which pages
could win FAQ or HowTo rich results but don't have that schema yet.

## Steps

1. Call `list_properties` to confirm the exact `site_url`.
2. Call `get_advanced_search_analytics` with `dimensions=page`,
   `sort_by=impressions`, `sort_direction=descending`, `row_limit=30`,
   `start_date` = 28 days ago, `end_date` = today, to get the top 30 pages
   by impressions. Record each page's impressions, clicks, and average
   position.
3. Call `batch_url_inspection` on those 30 URLs (up to 10 per call — run it
   three times to cover all 30). For each page, record:
   - Any structured-data errors or warnings
   - The structured-data types currently present (Article, FAQPage, HowTo,
     Product, etc.)
   - Mark the page `SCHEMA_ERROR` if any error was reported
4. For every page that does **not** currently have FAQPage schema, call
   `get_search_by_page_query` to get its top 5 queries for the same date
   range. Flag the page `FAQ_CANDIDATE` if any of those queries starts with
   Who, What, Why, When, How, Is, Are, or Can.
5. For every page that does **not** currently have HowTo schema, flag it
   `HOWTO_CANDIDATE` if any of its top 5 queries contains the phrase
   "how to".

## Output format

A Markdown table with columns: page URL, impressions, structured-data types
present, errors (yes/no), FAQ candidate (yes/no), HowTo candidate (yes/no).
Follow it with two subsections: one listing only the `SCHEMA_ERROR` pages
with their specific errors, and one listing the `FAQ_CANDIDATE` /
`HOWTO_CANDIDATE` pages with their triggering queries.
