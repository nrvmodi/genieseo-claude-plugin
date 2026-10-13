---
name: seo-weekly-report
description: Generate a complete weekly SEO performance report for a site.
  Use when asked for a site summary, performance overview, or weekly report.
---

# SEO Weekly Report

Generate a full weekly SEO performance report for a Google Search Console property.

This skill is read-only: it only calls GenieSEO reporting tools and never
submits, deletes, or changes anything in the user's account.

## Steps

1. Call `list_properties` to confirm the exact `site_url` to use. If the user
   has more than one property and didn't name one, ask which to report on.
2. Call `get_performance_overview` with `days=28` to retrieve totals (clicks,
   impressions, CTR, position) and the daily trend.
3. Call `compare_search_periods` with `dimensions=query`, `limit=200`, and
   explicit dates (YYYY-MM-DD), putting the **older** window in period 1 and
   the **recent** window in period 2:
   - `period1_start` = today minus 56 days, `period1_end` = today minus 29 days
     (prior 28 days)
   - `period2_start` = today minus 28 days, `period2_end` = today minus 1 day
     (last 28 days)

   With this ordering the tool's `click_diff` (= p2 − p1) and `click_pct` are
   negative when clicks fell. Do not swap the periods — a reversed order makes
   growing queries look like declines.
4. Flag a query as a decline alert only if `p1_clicks` >= 5 AND
   `p2_clicks` < 0.8 × `p1_clicks` (a drop of more than 20%). Compute this from
   the raw `p1_clicks` / `p2_clicks` values. Ignore queries below the 5-click
   floor, where percentage swings are noise. If nothing qualifies, say so
   explicitly rather than inventing alerts.
5. Call `get_search_analytics` with `dimensions=query`, `days=28`, and
   `row_limit=10` to get the top 10 queries by clicks.
6. Summarize all results in a structured report with:
   - Overall performance snapshot (totals + period-over-period change, where
     change = recent minus prior)
   - Alerts: queries with >20% click decline (prior clicks → recent clicks, %)
   - Top 10 queries by clicks
   - One-sentence recommendation for each alert

## Output format

Present the report as a clear markdown document with headings, a summary
table, and an action list. State the exact date ranges compared.
