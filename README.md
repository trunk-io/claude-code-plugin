# Trunk Flaky Tests — Claude Code Plugin

Flaky test detection, root cause analysis, and fix suggestions for development teams.

## What it does

This plugin connects Claude Code to the [Trunk Flaky Tests](https://trunk.io) MCP server, giving you access to:

| Tool                  | Description                                                      |
| --------------------- | ---------------------------------------------------------------- |
| `fix-flaky-test`      | Retrieve root cause analysis and fix suggestions for flaky tests |
| `setup-trunk-uploads` | Generate a setup plan to upload test results to Trunk            |

## Install

Install from the Claude Code plugin marketplace:

```
/plugin install trunk@claude-plugin-directory
```

Or add manually to your MCP configuration:

```json
{
  "mcpServers": {
    "trunk": {
      "type": "http",
      "url": "https://mcp.trunk.io/mcp"
    }
  }
}
```

## Authorization

The Trunk MCP server uses **OAuth 2.0 + OpenID Connect**. When you first connect, you'll be prompted to authenticate via your Trunk account.

## Documentation

- [Trunk Flaky Tests docs](https://docs.trunk.io/flaky-tests)
- [MCP server docs](https://docs.trunk.io/flaky-tests/use-mcp-server)
- [Claude Code setup guide](https://docs.trunk.io/flaky-tests/use-mcp-server/configuration/claude-code-cli)

## License

MIT
