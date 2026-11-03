# Directory policy fixes: MCP server type and privacy policy URL

## Problem
Version `ec03c1b` (v1.0.0) passed the security scan but was blocked by Claude
directory policy (Sep 30):
- `MCP_SERVER_INVALID_DROPPED` / `REMOTE_MCP_TYPE_MISSING` on `mcp.json` ·
  `mcpServers.genieseo` — remote server has a `url` but no `type`.
- `PRIVACY_URL_MISSING` — plugin uses a remote MCP server but links no privacy
  policy.

## Root cause
`ec03c1b` switched `.claude-plugin/plugin.json` from `./.mcp.json` (which has
`"type": "http"`) to a new root `mcp.json` shared with the Cursor/Grok
manifests, and that file omitted `type`. Claude Code drops a remote server
without `type`, so the plugin would have shipped with no connector at all.
No `privacyPolicyUrl` was ever set.

## Fix
- Added `"type": "http"` to `mcp.json`. Kept the shared file rather than
  pointing Claude back at `.mcp.json`, so all three manifests stay on one
  server definition.
- Added `"privacyPolicyUrl": "https://seo.geniedial.in/privacy"` to
  `.claude-plugin/plugin.json` only (the directory reads it; other tools'
  manifests are untouched).

Not changed: `logo` (`UNKNOWN_KEY_CROSS_TOOL`) and the `assets/logo.png`
reference in `SUBMISSION.md` (`UNREAD_ASSET_REFERENCED`) are informational —
the directory says they are safe to keep / left for the reviewer.

## Evidence
- `curl https://seo.geniedial.in/privacy` → 200 (`/privacy-policy` → 404, so
  the route is real, not a catch-all).
- `claude plugin validate . --strict`: only warnings are the unknown keys
  `privacyPolicyUrl` (local CLI doesn't know it yet; the directory requires
  it) and `logo`. No MCP errors.
- Confirm on the directory dashboard after the next scheduled scan.

## Follow-up before merge (human-review risks)
The automated policy didn't flag these, but the human reviewer reads the listing:
- **Duplicate MCP definition removed.** The root `.mcp.json` (Claude Code
  loads it by default) and `mcp.json` (named in the manifest) both defined
  `genieseo`. That's a likely cause of `INVENTORY_GAPS`. `.mcp.json` is deleted.
- **README keyword stuffing removed** ("SEO MCP, Google Search Console MCP,
  GA4 MCP, keyword research MCP, rank tracking MCP…"), along with
  unsupported product claims ("rank tracker", "full-time SEO analyst") and the
  reviewer-aimed "Brand Scoping" bullet.
- **Wrong "read-only" claim corrected.** The server requests
  `https://www.googleapis.com/auth/webmasters` (read/write) for Search Console,
  confirmed in `google-search-console-mcp/orchestrator/app/config.py`. Only
  GA4 uses `analytics.readonly`. The README now says so and links the privacy
  policy.
