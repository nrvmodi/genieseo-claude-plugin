# GenieSEO — SEO & Analytics Plugin

GenieSEO connects Google Search Console and Google Analytics 4 to Cursor and Claude Code — turning your AI assistant into a full-time SEO analyst, keyword research tool, rank tracker, and technical SEO auditor. No dashboard logins, no CSV exports, no pivot tables.

Whether you need an SEO MCP, Google Search Console MCP, GA4 MCP, keyword research MCP, or rank tracking MCP, this single plugin provides all of it. Ask in plain English; answers come live from Google's official Search Console and Analytics Data APIs.

## Highlights

- **Keyword Research & Rank Tracking**: Analyze queries, impressions, clicks, CTR, and exact average Google position by date range, country, device, or page. Easily spot striking-distance keywords stuck on page 2.
- **Traffic Drop & Algorithm Audits**: Compare any two date ranges to pinpoint pages and keywords that lost rankings after a Google core update.
- **Page-Level Audits**: Discover every query driving traffic to a URL for targeted content refreshes and on-page optimization.
- **Google Indexing & Technical SEO**: Perform live URL inspection for index status, crawl status, canonical selection, and mobile usability across single URLs or bulk sets.
- **XML Sitemap Monitoring**: Track submitted versus indexed counts, catch processing errors, and trigger resubmissions.
- **GA4 Analytics**: Inspect real-time active users, acquisition channels, top landing pages, and conversion funnels directly alongside search performance.

Built for founders, content marketers, e-commerce store owners, agencies, and SEO specialists who want instant Google search data and actionable recommendations.

---

## Installation & Setup

### Cursor IDE
- **From Marketplace**: Search for `genieseo` under **Cursor Settings > Plugins**.
- **Local Testing**: Go to **Cursor Settings > Plugins > Add Local Plugin** and select this directory.

### Claude Code
- **From Marketplace**:
  ```bash
  /plugin marketplace add anthropics/claude-plugins-community
  /plugin install genieseo@claude-community
  ```
- **Local Testing**:
  ```bash
  claude --plugin-dir .
  ```

---

## Account Connection

This plugin's MCP server (`genieseo`) connects to the hosted `https://seo.geniedial.in/mcp` endpoint.

1. On first use, follow the OAuth authorization prompt to grant read-only access to your Google Search Console and GA4 accounts.
2. No local Python or Google Cloud OAuth client setup is required — self-hosting options are documented in the [main repository](https://github.com/nrvmodi/google-search-console-mcp#readme).

---

## Included Skills

### Basic Skills
- `seo-weekly-report` — Complete weekly performance report for a site.
- `cannibalization-check` — Query analysis for multiple pages competing for the same rankings.
- `content-opportunities` — High-impression, low-CTR quick win optimization targets.
- `indexing-audit` — Indexing status audit across top landing pages.

### Advanced Skills
- `keyword-gap-finder` — Position 11–50 opportunities grouped by landing page with content and internal-linking recommendations.
- `revenue-attribution` — Search Console queries cross-referenced with GA4 conversions and revenue.
- `content-decay-detection` — Pages experiencing sustained click decline across three consecutive periods.
- `structured-data-audit` — Audit for structured data errors and FAQ/HowTo schema opportunities.
- `international-traffic-audit` — Country-level performance breakdown and localization/hreflang opportunities.

---

## License

This project is licensed under the [MIT License](LICENSE).
