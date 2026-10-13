---
name: international-traffic-audit
description: Break down performance by country to find localization and
  hreflang opportunities, and un-localized queries in top international
  markets. Use when asked about international SEO, country traffic, or
  hreflang.
---

# International Traffic Audit

Break Search Console performance down by country to find where
localization or hreflang would help — and which international queries have
no localized page to catch them.

This skill is read-only: it only calls GenieSEO reporting tools and never
submits, deletes, or changes anything in the user's account.

## Steps

1. Call `list_properties` to confirm the exact `site_url`.
2. Call `get_advanced_search_analytics` with `dimensions=country`,
   `sort_by=impressions`, `sort_direction=descending`, `row_limit=20`,
   `start_date` = 28 days ago, `end_date` = today. Report the top 20
   countries by impressions with clicks, impressions, CTR, and average
   position per country. Compute the impressions-weighted site-wide average
   CTR across all returned countries.
3. Flag any country where impressions > 1,000 AND its CTR is more than 20%
   below the site-wide average CTR (e.g. if site-wide CTR is 3.0%, flag any
   country with CTR < 2.4%). Label each flagged country
   `LOCALIZATION_OPPORTUNITY`.
4. Identify the top 3 non-primary markets by impressions — exclude the
   single country with the highest clicks, since that's the primary market.
5. For each of those 3 countries, call `get_advanced_search_analytics` with
   `dimensions=query,page`, `filter_dimension=country`,
   `filter_operator=equals`, `filter_expression=<lowercase ISO-3166-1
   alpha-3 code for that country, e.g. "usa">`, `sort_by=impressions`,
   `sort_direction=descending`, `row_limit=10`, same date range, to get its
   top 10 queries by impressions along with the landing page for each row.
   Flag a query `NO_LOCAL_PAGE` if none of the returned pages for that
   query appear localized for that country (no country-code subdirectory,
   subdomain, or equivalent visible in the page URL).

## Output format

A Markdown table for step 2 (all 20 countries), a short list of countries
flagged `LOCALIZATION_OPPORTUNITY` from step 3, and one subsection per
non-primary market from step 5 with its top-10-query table and any
`NO_LOCAL_PAGE` flags.
