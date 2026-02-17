# Changelog

All notable changes to this project will be documented in this file.

## [1.1.0] - 2026-02-17

### Added
- `toggl_create_entry` tool for creating completed time entries with specific start/stop times
- `toggl_create_client` tool for creating clients in a workspace
- `toggl_create_project` tool for creating projects in a workspace
- `toggl_update_project` tool for renaming, reassigning, or archiving projects
- `toggl_update_time_entry` tool for modifying existing time entries
- `toggl_delete_time_entry` tool for removing time entries
- `CreateProjectRequest`, `UpdateProjectRequest`, `CreateClientRequest` types

### Changed
- Repository transferred to 84emllc/mcp-toggl (forked from verygoodplugins/mcp-toggl)
- Updated server.json with correct tool manifest and 84EM authorship
- Updated README with full tool documentation and fork attribution
- Moved project instructions from CLAUDE.md to AGENTS.md

## 1.0.0 - 2025-09-06
- Initial public release
- Added npx usage documentation for Claude Desktop and Cursor
- Added CLI flags: `--help` and `--version`
- Added .npmignore to publish only compiled output
- Added package metadata (repository, homepage, bugs, engines)
