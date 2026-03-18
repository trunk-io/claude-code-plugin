# Publishing to the Claude Code Plugin Directory

The plugin is built and the repo is public, but it's not listed in the official
Anthropic plugin directory yet. Until it is, the `/plugin install trunk@claude-plugin-directory`
command won't resolve for users.

## What needs to happen

### 1. Submit the directory listing

Anthropic uses a form for external plugin submissions:

**https://clau.de/plugin-directory-submission**

Fill it out with:

| Field | Value |
|---|---|
| Plugin name | `trunk` |
| Repository | `https://github.com/trunk-io/claude-code-plugin` |
| Description | Flaky test detection, root cause analysis, and fix suggestions for development teams. |
| Author | Trunk |
| Homepage | `https://trunk.io` |

### 2. What Anthropic adds to their repo

Once approved, Anthropic adds a thin listing to `anthropics/claude-plugins-official` under
`external_plugins/trunk/`. Based on how other external plugins (Linear, Slack, Stripe) are listed,
the directory entry is just two files that mirror what's already in this repo:

```
external_plugins/trunk/
├── .claude-plugin/
│   └── plugin.json      # Minimal metadata (name, description, author)
└── .mcp.json             # MCP server URL
```

**plugin.json** (directory version — lighter than the repo's full version):
```json
{
  "name": "trunk",
  "description": "Flaky test detection, root cause analysis, and fix suggestions for development teams.",
  "author": {
    "name": "Trunk"
  }
}
```

**.mcp.json:**
```json
{
  "trunk": {
    "type": "http",
    "url": "https://mcp.trunk.io/mcp"
  }
}
```

Note: the directory `.mcp.json` uses a flat format (no `mcpServers` wrapper), unlike the repo's
version. Check the current directory format before submitting in case this has changed.

### 3. Verify the install command works

After the listing is live:

```bash
claude
# then inside Claude Code:
/plugin install trunk@claude-plugin-directory
```

Confirm it installs, authenticates via OAuth, and the slash commands (`/trunk:fix-flaky`, etc.)
are available.

### 4. Merge the docs page

Once the install command works, merge the docs PR:

- **PR:** https://github.com/trunk-io/docs/pull/515
- **Branch:** `samgutentag/claude-code-plugin-setup`

## Current state of the plugin repo

Everything here is ready to go — the plugin structure matches what Claude Code expects:

- `.claude-plugin/plugin.json` — metadata with name, description, author, repo, license, keywords
- `.mcp.json` — points to `https://mcp.trunk.io/mcp`
- `commands/` — three slash commands (fix-flaky, why-flaky, setup-uploads)
- `skills/` — two auto-activating skills (flaky-test-patterns, trunk-ci-setup)

No code changes needed in this repo. The blocker is purely the directory listing.
