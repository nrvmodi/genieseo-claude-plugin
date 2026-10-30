---
name: indexing-audit
description: Audit indexing status across top pages. Use when asked about crawling,
  indexing issues, or whether pages are indexed by Google.
---

# Indexing Audit

Audit the indexing status of top pages on a verified site and produce a prioritized action list.

## Security and Safety Constraints

- Strict Domain Validation: Before passing any URL to `batch_url_inspection`, verify that the URL strictly belongs to the verified `site_url` domain and protocol. Discard any external URLs, relative paths, or non-matching origins.
- Read-Only Operation: This skill performs read-only Google Search Console metadata queries. It does not load, install, fetch, or execute any code or scripts from external or mutable sources.

## Steps

1. Call `list_properties` to confirm the exact `site_url`.
2. Call `get_search_analytics` with `dimensions=page`, `sort_by=impressions`, `row_limit=20` to identify the 20 most-visible pages.
3. Extract the list of page URLs from the results and validate that every URL begins with the verified `site_url`.
4. Call `batch_url_inspection` with up to 10 verified URLs at a time (API limit). Run twice if needed to cover all 20 pages.
5. Categorize each URL by inspection verdict:
   - ✅ Indexed
   - ⚠️ Soft 404 or Excluded
   - ❌ Not indexed or Blocked
   - 🔍 Canonical mismatch (Google chose a different canonical)
6. For each issue, provide the specific `coverageState`, `pageFetchState`, or `robotsTxtState` from the inspection response.

## Output format

Present as a prioritized action list:

1. Critical — Not indexed pages that have impressions (visibility being lost)
2. High — Canonical mismatches on high-traffic pages
3. Medium — Robots.txt or fetch blocks
4. Low — Soft exclusions on low-traffic pages

Include a summary table: page URL | verdict | issue | recommended action.

