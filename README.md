# vaultctl

Markdown-native knowledge operating system. CLI, MCP server, REST API, and native macOS app for AI agents and humans.

**Site**: [vaultctl.testinginproduction.co](https://vaultctl.testinginproduction.co) | **Docs**: [vaultctl.testinginproduction.co/docs](https://vaultctl.testinginproduction.co/docs)

## About

vaultctl gives AI agents and humans structured access to a three-tier knowledge system (vault, skills, memory) through a CLI, REST API, MCP server, and native macOS app. Your markdown vault is the filesystem. vaultctl is the operating system.

This repository hosts **releases only** (pre-built binaries, Docker images, changelogs). Source code is maintained in a private repository.

## Packages

| Package | Description |
|---------|-------------|
| `@vaultctl/core` | TypeScript library: frontmatter, wikilinks, tags, search, health, intelligence, schema governance |
| `vaultctl` | CLI for vault operations (20 command groups, JSON-first output) |
| `@vaultctl/server` | REST API server (Express 5, AI chat, SSE events) |
| `@vaultctl/mcp` | MCP server for AI agent integration (22 tools, 3 resources, 1 prompt) |
| `Vaultctl.app` | Native macOS app (SwiftUI, graph visualization, calendar, editor, chat) |
| `VaultctlMobile` | iOS mobile app (SwiftUI, connects to vaultctl server) |
| `@vaultctl/site` | Marketing site + documentation |

## Installation

### From Source

```bash
git clone https://github.com/testing-in-production/vaultctl.git
cd vaultctl
npm install && npm run build
alias vaultctl="node $PWD/packages/cli/dist/index.js"
vaultctl config set vault ~/path/to/your/vault
```

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
claude mcp add vaultctl -s user -- npx @vaultctl/mcp
```

## Documentation

Full documentation at [vaultctl.testinginproduction.co/docs](https://vaultctl.testinginproduction.co/docs).

## License

[Business Source License 1.1](LICENSE) -- free for personal, educational, and internal use. Commercial use requires a license. Converts to MIT on 2030-03-04.
