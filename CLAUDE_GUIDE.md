# Using vaultctl with Claude Code

This guide shows how to set up vaultctl as the knowledge backbone for Claude Code sessions: persistent memory, reusable skills, and structured vault access via MCP, CLI, or filesystem.

## Quick Setup

### 1. Install vaultctl

```bash
git clone https://github.com/testing-in-production/vaultctl.git
cd vaultctl
npm install && npm run build
alias vaultctl="node $PWD/packages/cli/dist/index.js"
vaultctl config set vault ~/path/to/your/vault
```

### 2. Register the MCP Server

```bash
claude mcp add vaultctl -s user -- node /path/to/vaultctl/packages/mcp/dist/index.js
```

Add the vault path as an environment variable:

```json
// ~/.claude.json
{
  "mcpServers": {
    "vaultctl": {
      "type": "stdio",
      "command": "node",
      "args": ["/path/to/vaultctl/packages/mcp/dist/index.js"],
      "env": {
        "VAULTCTL_PATH": "/path/to/your/vault"
      }
    }
  }
}
```

### 3. Add a CLAUDE.md

Create a `CLAUDE.md` in your project root. Claude Code reads this automatically at session start. It should contain project-specific context that helps the AI understand your codebase.

```markdown
# My Project

One-line description of what this project does.

## Stack

- Language, framework, runtime
- Database, cache, message queue
- Testing framework (test count helps calibrate expectations)
- Node/Python/Go version

## Commands

\`\`\`bash
npm run build          # Build
npm run test           # Run tests
npm run dev            # Dev server
\`\`\`

## Project Structure

\`\`\`
src/
  models/     Data models and database schema
  routes/     API endpoints
  services/   Business logic
  utils/      Shared utilities
\`\`\`

## Key Patterns

- **Config**: Where config lives, how it's loaded
- **Auth**: How authentication works
- **Testing**: Patterns, fixtures, mocking approach
- **Deployment**: Where it runs, how to deploy

## Branches

- `main`: Current stable version
```

Keep CLAUDE.md under 200 lines. It's loaded into every conversation, so brevity matters. Put detailed documentation in separate files and reference them.

## Three-Tier Knowledge Model

vaultctl organizes knowledge into three tiers, each with different access patterns:

### Tier 1: Vault (read/write)

Your markdown vault. Structured notes with YAML frontmatter, `[[wikilinks]]`, and tags. Full CRUD via CLI, API, and MCP.

Good for: decisions, meeting notes, project briefs, reference material, any durable fact you want to retrieve later.

```bash
# Search the vault
vaultctl search "database migration"

# Read a specific note
vaultctl read "decisions/use-postgres.md"

# Create a note
vaultctl create --template decision "Switch to PostgreSQL"
```

### Tier 2: Skills (read-only)

Claude Code skill files in `.claude/skills/`. Procedural knowledge: how to do things, not what things are. Skills are indexed and searchable through vaultctl.

Good for: debugging procedures, deployment checklists, framework-specific gotchas, coding patterns you want Claude to follow.

Example skill structure:

```
.claude/skills/
  express-5-migration.md     # Express 4 to 5 breaking changes
  docker-monorepo.md          # Multi-stage Docker builds for monorepos
  testing-patterns.md         # Project-specific test conventions
```

### Tier 3: Memory (read-only)

Claude Code memory files in `.claude/projects/*/memory/`. Fast context that persists across conversations. Memory files are indexed and searchable through vaultctl.

Good for: user preferences, active project state, cross-project patterns, tool configuration notes.

```
.claude/projects/-Users-you-Projects-myapp/memory/
  MEMORY.md       # Always loaded (keep under 200 lines)
  patterns.md     # Reusable patterns discovered across sessions
  decisions.md    # Why we chose X over Y
```

## MCP Tools Reference

When the MCP server is registered, Claude Code gets access to 25 tools:

### Knowledge Operations

| Tool | Description |
|------|-------------|
| `knowledge_query` | Search across all three tiers (vault, skills, memory) |
| `knowledge_read` | Read a specific note by path |
| `knowledge_write` | Create or update a note with frontmatter |
| `knowledge_timeline` | Activity timeline for a note or the whole vault |
| `suggest_links` | Find related notes that could be wikilinked |
| `find_similar` | Find semantically similar notes |
| `enrich` | AI-generated enrichment suggestions |

### Session Management

| Tool | Description |
|------|-------------|
| `session_start` | Begin a tracked work session |
| `session_end` | End session with summary |
| `session_decision` | Record a decision made during the session |

### Health and Governance

| Tool | Description |
|------|-------------|
| `health_check` | Run vault health checks (9 validators) |
| `health_fix` | Auto-fix health issues |
| `schema` | Show the vault schema |
| `schema_diff` | Compare declared schema vs code enforcer |
| `schema_impact` | Simulate the impact of a schema change |

### Intelligence

| Tool | Description |
|------|-------------|
| `intelligence_patterns` | Detect patterns across vault notes |
| `daily_summary` | AI-generated daily summary |
| `weekly_summary` | AI-generated weekly summary |
| `project_brief` | Generate a project brief |
| `review_queue` | Notes due for review |

### Utilities

| Tool | Description |
|------|-------------|
| `stats` | Vault statistics |
| `tags_list` | All tags with counts |
| `note_create` | Create a note from a template |
| `index_rebuild` | Rebuild the search index |
| `config_get` | Read vaultctl configuration |

## CLI Fallback

If MCP is unavailable (server not running, stale process), use the CLI directly:

```bash
# Search
vaultctl search "keyword" --type project --status active

# Read
vaultctl read "path/to/note.md"

# Health
vaultctl health --check broken-links

# Schema
vaultctl schema diff

# Tags
vaultctl tags list

# Info
vaultctl info
```

All CLI output is JSON by default (AI-friendly). Add `--format table` for human-readable output.

## Setting Up Memory

### MEMORY.md (auto-loaded)

Create a `MEMORY.md` in your project's memory directory. Claude Code loads this automatically. Use it for fast context that should be available in every session.

```markdown
# Memory

## Project
- **Name**: Brief description
- **Stack**: Key technologies
- **Repo**: Where the code lives

## Preferences
- Testing framework and patterns
- Commit message style
- Code style preferences

## Active Work
- Current focus area
- Recent decisions (link to vault notes for details)

## Key Files
- `src/config.ts` - All configuration
- `src/db/schema.ts` - Database schema
- `tests/fixtures/` - Test data
```

### Topic Files

For detailed knowledge that doesn't need to be in every conversation, use separate topic files:

```
memory/
  MEMORY.md          # Always loaded, keep concise
  patterns.md        # Reusable patterns
  decisions.md       # Decision log
  infrastructure.md  # Deployment, servers, networking
```

Reference these from MEMORY.md so Claude knows they exist:

```markdown
## Topic Files
- **patterns.md**: Cross-project patterns organized by concept
- **decisions.md**: Decision log with rationale
- **infrastructure.md**: Servers, deployment, networking
```

## Setting Up Skills

Skills are procedural knowledge: step-by-step instructions for specific tasks. Claude Code reads them when relevant.

### Skill Structure

```markdown
# Skill Name

One-line description. Use when: (1) trigger condition, (2) trigger condition.

## Steps

1. Do this first
2. Then do this
3. Finally do this

## Common Pitfalls

- Thing that looks right but is wrong
- Edge case to watch for
```

### Good Skill Candidates

- Framework migration gotchas (Express 4 to 5, React class to hooks)
- Deployment procedures for your infrastructure
- Debugging playbooks for recurring issues
- API quirks in tools you use (gray-matter date parsing, Docker layer caching)
- Project-specific conventions (test patterns, naming, file organization)

### Bad Skill Candidates

- Generic programming knowledge (Claude already knows this)
- One-time procedures you won't repeat
- Information that changes frequently (put this in vault notes instead)

## Vault Schema

vaultctl enforces note types via frontmatter schema. The default schema includes 11 types:

| Type | Purpose |
|------|---------|
| `project` | Active projects with status tracking |
| `area` | Ongoing areas of responsibility |
| `resource` | Reference material |
| `person` | People and contacts |
| `concept` | Ideas and mental models |
| `meeting` | Meeting notes |
| `daily` | Daily journal entries |
| `decision` | Recorded decisions with rationale |
| `tool` | Software tools and configurations |
| `inbox` | Unprocessed items |
| `context` | Contextual/background information |

### Universal Frontmatter Fields

Every note has these fields:

```yaml
---
type: project
status: active
tags:
  - engineering
  - backend
created: 2025-01-15
updated: 2025-03-04
summary: Brief description of the project
confidence: high
source_ai: human
---
```

Customize the schema in `_meta/schema.yaml` and validate with `vaultctl schema diff`.

## REST API

For programmatic access beyond the CLI, run the server:

```bash
VAULT_PATH=/path/to/vault node /path/to/vaultctl/packages/server/dist/index.js
```

Key endpoints:

```
GET    /api/v1/knowledge           # Search
GET    /api/v1/knowledge/:id       # Read
PUT    /api/v1/knowledge/:id       # Write
GET    /api/v1/health              # Health checks
GET    /api/v1/tags                # Tag index
GET    /api/v1/stats               # Statistics
GET    /api/v1/events              # SSE stream
POST   /api/v1/chat                # AI chat (requires Anthropic API key)
```

## Recommended Workflow

1. **Session start**: Claude reads MEMORY.md automatically. If using MCP, it can query the vault for additional context.

2. **During work**: Record decisions in the vault as you go. Use `session_decision` (MCP) or write directly to the vault.

3. **Session end**: Update memory files with new patterns, decisions, or preferences discovered during the session. Update vault notes with any durable facts.

4. **Between sessions**: Run `vaultctl health` periodically to catch broken links, missing frontmatter, orphaned notes. Use `vaultctl review` to surface notes due for review.

## Troubleshooting

### MCP server not responding

```bash
# Kill stale processes
pkill -f "vaultctl.*mcp"

# Verify the build exists
ls /path/to/vaultctl/packages/mcp/dist/index.js

# Test manually
VAULTCTL_PATH=/path/to/vault node /path/to/vaultctl/packages/mcp/dist/index.js
```

### Vault not found

```bash
# Check config
vaultctl config get vault

# Set it
vaultctl config set vault /path/to/your/vault

# Or use environment variable
export VAULTCTL_PATH=/path/to/your/vault
```

### Health check failures

```bash
# See all issues
vaultctl health --json

# Auto-fix what's fixable
vaultctl health --fix

# Check specific issue
vaultctl health --check broken-links
```
