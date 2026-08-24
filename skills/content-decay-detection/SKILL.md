---
name: content-decay-detection
description: Detect content decay — pages with sustained click declines
  across three consecutive periods. Use when asked about traffic loss,
  content refresh priority, or pages quietly losing traffic.
---

# Content Decay Detection

Find pages where clicks have quietly dropped for three consecutive 28-day
periods — decay usually shows up here weeks before traffic craters.

## Steps

1. Call `list_properties` to confirm the exact `site_url`.
2. Call `compare_search_periods` with `dimensions=page`, `limit=1000`,
   comparing:
   - `period1` = Period A, the most recent 28 days (today minus 28 to today)
   - `period2` = Period B, the prior 28 days (today minus 56 to today
     minus 28)
3. Call `compare_search_periods` again with `dimensions=page`, `limit=1000`,
   comparing:
   - `period1` = Period B, the same range as above (today minus 56 to
     today minus 28)
   - `period2` = Period C, the 28 days before that (today minus 84 to
     today minus 56)
   Combine the two calls: for each page you now have clicks/impressions for
   periods A and B (from the first call) and periods B and C (from the
   second call). Use the two calls' overlapping period-B figures as a
   sanity cross-check — they should match.
4. For every page present in all three periods, flag it if:
   - clicks in A <= 0.85 x clicks in B, AND
   - clicks in B <= 0.85 x clicks in C
   (a 15%+ drop in each consecutive period)
5. Sort flagged pages by total clicks lost (clicks in C minus clicks in A)
   descending.
6. For the top 15 decaying pages, call `get_search_by_page_query` with
   `days=28` (period A = the most recent 28 days) and `row_limit=5` to get
   each page's top 5 queries still generating impressions, with each
   query's impressions and average position.
7. Assign a refresh priority to each of the top 15 pages:
   - `P1`: total impressions in period A >= 5,000 OR average position <= 20
   - `P2`: total impressions in period A >= 1,000 OR average position <= 50
   - `P3`: everything else

## Output format

A triage table with columns: page URL, priority (P1/P2/P3), period A
clicks, period B clicks, period C clicks, total decline %, top query. Sort
by priority (P1 first), then by clicks lost descending within each
priority tier.
