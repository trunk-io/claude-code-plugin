# Trunk Flaky Tests — Claude Code Plugin

Flaky test detection, root cause analysis, and fix suggestions for development teams.

## What it does

This plugin connects Claude Code to the [Trunk Flaky Tests](https://trunk.io) MCP server, giving you AI-powered tools, slash commands, and built-in knowledge for debugging and fixing flaky tests.

### MCP Tools

| Tool                  | Description                                                      |
| --------------------- | ---------------------------------------------------------------- |
| `fix-flaky-test`      | Retrieve root cause analysis and fix suggestions for flaky tests |
| `setup-trunk-uploads` | Generate a setup plan to upload test results to Trunk            |

### Slash Commands

| Command                | Description                                                  |
| ---------------------- | ------------------------------------------------------------ |
| `/trunk:fix-flaky`     | Get root cause analysis and apply a fix for a flaky test     |
| `/trunk:why-flaky`     | Explain why a test is flaky without making changes           |
| `/trunk:setup-uploads` | Set up test result uploads to Trunk for your repository      |

### Skills

| Skill                  | Description                                                                          |
| ---------------------- | ------------------------------------------------------------------------------------ |
| `flaky-test-patterns`  | Common flaky test patterns and fixes — activates when debugging or writing tests     |
| `trunk-ci-setup`       | CI pipeline best practices for test uploads — activates when editing CI config files |

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

## Quick Start

```
# Fix a flaky test in one command
/trunk:fix-flaky test_user_login

# Understand why a test is flaky before deciding what to do
/trunk:why-flaky test_payment_processing

# Set up test uploads for your repo
/trunk:setup-uploads
```

## Authorization

The Trunk MCP server uses **OAuth 2.0 + OpenID Connect**. When you first connect, you'll be prompted to authenticate via your Trunk account.

## Documentation

- [Trunk Flaky Tests docs](https://docs.trunk.io/flaky-tests)
- [MCP server docs](https://docs.trunk.io/flaky-tests/use-mcp-server)
- [Claude Code setup guide](https://docs.trunk.io/flaky-tests/use-mcp-server/configuration/claude-code-cli)

## Also Available For

- [Cursor](https://github.com/trunk-io/cursor-plugin)
- [Any MCP client](https://github.com/trunk-io/mcp-server) (GitHub Copilot, Gemini CLI, and more)

## License

MIT
