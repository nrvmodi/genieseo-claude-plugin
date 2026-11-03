# GenieSEO plugin

Plugin package (skills + remote MCP connector) for Claude Code, Cursor and
Grok. The MCP server itself lives in `nrvmodi/google-search-console-mcp`.

## Layout
- `.claude-plugin/plugin.json` — Claude manifest (what the Claude directory scans).
- `.cursor-plugin/plugin.json`, `.grok-plugin/plugin.json`, root `plugin.json` — other tools' copies.
- `mcp.json` — the one MCP server definition, shared by all manifests. Don't re-add a root `.mcp.json`: Claude Code also loads it by default, giving a second `genieseo` definition.
- `skills/<name>/SKILL.md` — the 9 skills.
- `SUBMISSION.md` — listing copy for the submission form (not loaded by Claude).

## Check
`claude plugin validate . --strict` — expected warnings: unknown keys `logo` and `privacyPolicyUrl`.

## Claude directory gotchas
- Remote MCP entries need `"type": "http"` next to `url`, or Claude Code drops the server and the directory blocks the version.
- `privacyPolicyUrl` (https://seo.geniedial.in/privacy) is required because the plugin uses a remote MCP server.
- The directory rescans the GitHub repo about every 6 hours; every pushed commit becomes a version that gets checked against policy.
- Change docs go in `docs/changes/YYYY-MM-DD-<slug>.md`.
