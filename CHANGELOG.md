# Changelog

All notable changes to this project will be documented in this file.

## [2.3.0] - 2026-03-17

### Added
- **Session grouping by project**: Daily notes now group session entries under `### {project}` headers within the `## Sessions` block
- **Session digest API**: `POST /api/v1/activity/digest` creates or appends structured session entries to today's daily note
- **Heading injection guard**: Session entries containing `#`, `##`, or `###` lines (e.g. from commit message milestones) are escaped before insertion, preventing section structure corruption on re-processing

### Fixed
- **ingestBatch per-event project resolution**: Was resolving project once from first event's `cwd` and applying it to all events in the batch. Each event now independently resolves its own project.
- **Atomic daily note writes**: Update path now uses tmp-file + POSIX rename to prevent partial writes on crash
- **Compaction script exit codes**: `vaultctl-compact.sh` was silently swallowing curl errors. Now exits non-zero on curl failure or non-2xx HTTP response.
- **PostToolUse hook**: `activity-buffer.sh` now runs with `async: true`, removing ~41ms of blocking latency per tool call

### Changed
- **Claude Code guide** (`docs/claude-guide.md`): Added full session activity tracking documentation (hooks setup, project mapping, REST API endpoints, daily note format)

### Tests
- 552 tests across 36 files (up from 539/35)

## [2.2.0] - 2026-03-12

### Added
- **MCP hardening**: Global error handlers, graceful shutdown, promise-locked index builds, per-file resilience, stderr-only logging
- **Keychain auth**: Server auth password stored in macOS Keychain (`security find-generic-password -a admin -s vaultctl-server -w`)
- **Native macOS app**: SwiftUI app v4.0.0 with graph, calendar, editor, chat, and intelligence features
- **Marketing site**: `vaultctl.testinginproduction.co` with docs and getting started guide
- **BSL 1.1 license**: Business Source License, converts to MIT 2030-03-04

## [2.1.0] - 2026-03-08

### Added
- **Activity Telemetry**: Full session tracking system with JSONL event store, project resolution, and event ingestion (`core`, `server`, `mcp`)
  - `ActivityEngine` with append, query, and compact operations
  - REST endpoints: `POST /api/v1/activity`, `POST /api/v1/activity/batch`, `GET /api/v1/activity`
  - MCP tools: `session_activity`, `log_activity`
- **CLI parity**: 3 new commands bringing CLI to 26 commands (from 23)
  - `suggest-links <path>`: AI-suggested wiki-links, tags, and summary (matches MCP `suggest_links`)
  - `activity query`: Query recent session activity telemetry (matches MCP `session_activity`)
  - `activity log`: Log decisions/patterns to the activity stream (matches MCP `log_activity`)
- **CLI `read` upgrade**: Now builds knowledge graph and returns backlinks + outbound links (matches MCP `knowledge_read`). Added `--no-graph` flag for fast path.
- **CLI `write` upgrade**: Now passes knowledge index to `writeNote()` for duplicate detection (matches MCP `knowledge_write`)
- Claude Code integration guide page on site
- Public releases repo with GitHub links
- Site: favicon, logo assets, activity telemetry docs section

### Fixed
- **Security**: Critical path traversal and file permission vulnerabilities
- **Native (macOS)**: Crash on launch under App Translocation (v3.0.1)
- Timeline view: empty times, unsorted groups, missing filters
- Production hardening: SEO, content accuracy, code cleanup
- Site: correct API endpoints, CLI syntax, status values, auth env vars
- Site: Tailwind v4 CSS bug, factual claims, stale references
- Input validation on activity REST endpoints and safe JSON in buffer hook
- Build: added `NODE_OPTIONS` for MCP heap allocation in npm scripts
- CLI version string corrected from `1.0.0` to `2.1.0`
- MCP version string corrected from `2.0.0` to `2.1.0`

### Changed
- Site: full content review, rebrand, tighten copy
- Removed npm publish workflow (source-only distribution)
- Repo cleanup: removed stale files, untracked build artifacts

## [2.0.0] - 2026-02-28

Initial open-source release. 7 packages, 496 tests, BSL 1.1 license.
