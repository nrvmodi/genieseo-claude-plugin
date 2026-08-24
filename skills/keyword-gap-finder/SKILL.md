---
name: keyword-gap-finder
description: Find keyword gap opportunities — queries ranking 11-50 with real
  search demand, grouped by landing page with content and internal-linking
  recommendations. Use when asked about keyword gaps, content planning, or
  page-2-5 rankings with topical authority.
---

# Keyword Gap Finder

Surface the best keyword gap opportunities: queries with real search demand
where the site ranks on page 2–5 but already has topical relevance.

## Steps

1. Call `list_properties` to confirm the exact `site_url`.
2. Call `get_advanced_search_analytics` with `dimensions=query,page`,
   `sort_by=impressions`, `sort_direction=descending`, `row_limit=1000`,
   `start_date` = 28 days ago, `end_date` = today.
3. Filter the results to rows where `position` is between **11 and 50** and
   `impressions` >= **500**.
4. Group the filtered rows by landing `page`. For each group, identify:
   - The cluster theme (3–5 words describing the shared topic across the
     group's queries)
   - The top 3 queries by impressions, with each query's impressions and
     average position
5. For each landing-page group, assign ONE content improvement
   recommendation based on the queries' phrasing:
   - `ADD_SECTION` — the page is missing a subtopic the queries imply
   - `REWRITE_INTRO` — the page covers the topic but the intro doesn't match
     search intent
   - `TARGET_FEATURED_SNIPPET` — query phrasing suggests a definition or
     step-list answer
   - `ADD_FAQ_SCHEMA` — multiple question-style queries in the group
6. Call `get_advanced_search_analytics` with `dimensions=query,page`,
   `sort_by=impressions`, `sort_direction=descending`, `row_limit=1000`,
   `start_date`/`end_date` for the same date range as step 2, and filter
   to rows where `position` <= 10. For each landing-page group from step 4,
   pick the highest-relevance page from this position <=10 set as the
   internal-linking source candidate.
7. Sort the final groups by total impressions descending.

## Output format

A summary table (landing page | cluster theme | total impressions | top
query | recommendation | link-source page), followed by one detailed
subsection per landing-page group listing all 3 top queries with their
impressions and average position.
