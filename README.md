# GenieSEO — Claude Code plugin

Connect Google Search Console and GA4 to Claude Code through the hosted
GenieSEO connector, plus 9 skills for SEO analysis.

This repository is the Claude Code plugin package only. The MCP server
itself, and the rest of the GenieSEO product, live in
[nrvmodi/google-search-console-mcp](https://github.com/nrvmodi/google-search-console-mcp).

## Install

Once published to the community marketplace:

```
/plugin marketplace add anthropics/claude-plugins-community
/plugin install genieseo@claude-community
```

To try it before publishing, clone this repository and run:

```
claude --plugin-dir .
```

## Connect your account

This plugin's MCP server (`genieseo`) points at the hosted
`https://seo.geniedial.in/mcp` endpoint. On first use, run `/mcp` inside
Claude Code and follow the OAuth sign-in prompt to authorize your Google
Search Console and GA4 access (read-only scopes). No local Python or
Google Cloud OAuth client setup is required — that's only needed if you
want to self-host the MCP server locally instead (see the
[main repository README](https://github.com/nrvmodi/google-search-console-mcp#readme)).

## Skills

**Basic**
- `seo-weekly-report` — full weekly performance report
- `cannibalization-check` — queries where multiple pages compete for the
  same rankings
- `content-opportunities` — high-impression / low-CTR / position 11–20
  quick wins
- `indexing-audit` — indexing status audit across top pages

**Advanced**
- `keyword-gap-finder` — position 11–50 opportunities grouped by landing
  page, with content and internal-linking recommendations
- `revenue-attribution` — Search Console queries cross-referenced with GA4
  conversions and revenue
- `content-decay-detection` — pages with sustained click decline across
  three consecutive periods
- `structured-data-audit` — structured-data errors and FAQ/HowTo schema
  opportunities
- `international-traffic-audit` — country-level performance and
  localization/hreflang opportunities

Skills are namespaced as `/genieseo:<skill-name>` once installed.

## License

MIT
