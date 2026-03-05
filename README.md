# vaultctl

Structured access to your markdown vault. TypeScript toolkit with frontmatter parsing, wikilink resolution, tag extraction, schema governance, and AI enrichment. CLI, REST API, MCP server, and native macOS app.

**Site**: [vaultctl.testinginproduction.co](https://vaultctl.testinginproduction.co) | **Docs**: [vaultctl.testinginproduction.co/docs](https://vaultctl.testinginproduction.co/docs)

## About

vaultctl gives AI agents and humans structured access to a three-tier knowledge system (vault, skills, memory) through a CLI, REST API, MCP server, and native macOS app. Your markdown files are the data layer. vaultctl parses, indexes, validates, and enriches them.

This repository hosts **releases** (pre-built binaries, changelogs). Source code is maintained in a private repository under BSL 1.1.

## Packages

| Package | Description |
|---------|-------------|
| `@vaultctl/core` | TypeScript library: frontmatter, wikilinks, tags, search, health, intelligence, schema governance |
| `vaultctl` | CLI (20 command groups, JSON-first output) |
| `@vaultctl/server` | REST API server (Express 5, SSE events, AI chat) |
| `@vaultctl/mcp` | MCP server (22 tools, 3 resources, 1 prompt) |
| `Vaultctl.app` | Native macOS app (SwiftUI, graph, calendar, editor, chat) |
| `@vaultctl/site` | Documentation site |

## Installation

### Docker (Server)

```bash
docker run -d \
  --name vaultctl \
  -p 3000:3000 \
  -v /path/to/your/vault:/vault \
  -e VAULT_PATH=/vault \
  ghcr.io/testing-in-production/vaultctl:latest
```

### Native macOS App

Download `Vaultctl.app` from the [Releases](https://github.com/testing-in-production/vaultctl-releases/releases) page. Code-signed and notarized by Apple.

### MCP Server

```bash
claude mcp add vaultctl -s user -- node /path/to/vaultctl/packages/mcp/dist/index.js
```

## Documentation

- [Full documentation](https://vaultctl.testinginproduction.co/docs)
- [Claude Code guide](CLAUDE_GUIDE.md) -- MCP setup, CLAUDE.md patterns, skills, memory

## License

[Business Source License 1.1](LICENSE) -- free for personal, educational, and internal use. Commercial use requires a license. Converts to MIT on 2030-03-04.
