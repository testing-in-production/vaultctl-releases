# Changelog

All notable changes to this project will be documented in this file.

## [2.1.0] - 2026-03-08

### Added
- **Activity Telemetry**: Full session tracking system with JSONL event store, project resolution, and event ingestion (`core`, `server`, `mcp`)
  - `ActivityEngine` with append, query, and compact operations
  - REST endpoints: `POST /api/v1/activity`, `POST /api/v1/activity/batch`, `GET /api/v1/activity`
  - MCP tools: `session_activity`, `log_activity`
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

### Changed
- Site: full content review, rebrand, tighten copy
- Removed npm publish workflow (source-only distribution)
- Repo cleanup: removed stale files, untracked build artifacts

## [2.0.0] - 2026-02-28

Initial open-source release. 7 packages, 496 tests, BSL 1.1 license.
