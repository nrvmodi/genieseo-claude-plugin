# GenieSEO — Claude community marketplace submission notes

Reference copy for filling out Anthropic's plugin submission form
(`platform.claude.com/plugins/submit`, or the claude.ai admin directory
form for a Team/Enterprise org). This file is not read by Claude Code
itself.

## Listing copy

**Name**: GenieSEO

**Short description**: Turn Google Search Console and GA4 data into
actionable SEO insights, from quick reports to advanced cross-referencing.

**Long description**: Connect your Google account to analyze search
performance, uncover query and page opportunities, diagnose indexing
issues, inspect URLs and sitemaps, compare date ranges, detect content
decay, audit structured data, and cross-reference Search Console with GA4
conversions and revenue. Nine skills cover both quick weekly reporting and
advanced quarterly analysis.

**Category**: Productivity

**Repository**: https://github.com/nrvmodi/genieseo-claude-plugin

**Version**: 1.0.0 · **Author**: Peakenza Software Services · **License**:
MIT · **Homepage**: https://seo.geniedial.in (these are already set in
`.claude-plugin/plugin.json` — surfaced here since the submission form will
ask for them separately).

**Icon/artwork**: `assets/logo.png` — upload this as the listing icon.

## Starter prompts

1. Give me my weekly SEO performance report.
2. Find queries where two of my pages are competing for the same rankings.
3. Find keyword gap opportunities where I rank 11–50 but have topical
   authority.
4. Which of my top pages are quietly losing traffic month over month?
5. Which Search Console queries actually drive GA4 conversions and revenue?

## Test cases

### Positive

1. **List properties** — after `/mcp` OAuth sign-in, ask to list Search
   Console properties; returns only properties available to the signed-in
   account.
2. **Basic skill** — run `/genieseo:seo-weekly-report`; returns a structured
   report with totals, period-over-period comparison, and top queries.
3. **Advanced skill** — run `/genieseo:revenue-attribution`; if a matching
   GA4 property can't be determined automatically, the skill asks for
   confirmation rather than guessing.
4. **Sitemap workflow** — list sitemaps for a property; no write operation
   (submit/delete) occurs without the user explicitly asking for it.

### Negative

1. **Unauthenticated request** — before completing `/mcp` OAuth, a data
   request prompts for authentication rather than failing silently or
   returning stale data.
2. **Unauthorized property** — requesting a property the signed-in account
   can't access returns an access-denied message, not another user's data.
3. **Destructive request without confirmation** — asking to delete a
   sitemap requires explicit confirmation before the tool is invoked.

## Pre-submission checklist

- [ ] `claude plugin validate . --strict` passes with no warnings.
- [ ] `claude --plugin-dir .` loads all 9 skills under the `genieseo`
  namespace.
- [ ] `/mcp` shows the `genieseo` server and completes OAuth sign-in against
  a real Google account.
- [ ] Production server exposes accurate MCP tool annotations
  (`readOnlyHint`, `destructiveHint`) — verified separately for the
  ChatGPT/Codex submission in the main repo
  ([nrvmodi/google-search-console-mcp](https://github.com/nrvmodi/google-search-console-mcp),
  `docs/MCP-REGISTRY-SUBMISSION.md`); re-check there if annotations change.
- [ ] Release notes drafted for version 1.0.0.
