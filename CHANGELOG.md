# Changelog

All notable changes to the POEditor MCP server.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [1.1.0] — 2026-06-05

### Added

- `get_proofread_progress` — proofreading progress per language
  (requires proofreading enabled)
- `propose_translations` — add translations flagged as fuzzy (pending review),
  without overwriting existing ones
- `fill_from_translation_memory` — fill empty translations from Translation
  Memory, with pagination for large projects
- `list_fuzzy_translations` — list translations currently marked fuzzy
- `list_pending_proofread` — list translated-but-not-proofread strings
  (requires proofreading enabled)
- `list_untranslated` — list terms with no translation yet for a given language

### Changed

- `view_project` renamed to `get_project_details`
- `export_project` renamed to `export_strings_file`
- `upload_project` renamed to `upload_strings_file`
- `add_translations` renamed to `commit_translations` (semantics clarified:
  fills untranslated only, never overwrites)
- Total tool count: 31 → 34

---

## [1.0.0] — 2026-06-03

### Added

- Remote MCP server hosted at `https://mcp.poeditor.com/mcp`
- Full POEditor v2 API coverage: projects, languages, terms, translations,
  contributors, exports
- Automatic translation via Google, Microsoft, and DeepL
- Translation progress and term detail inspection tools
- Streamable HTTP transport (MCP spec compliant)
- OAuth 2.1 with PKCE (for Claude Code, Claude.ai Connectors, and
  compatible clients)
- Bearer token authentication
- Client config examples for Claude Desktop, Cursor, Windsurf, Cline,
  VS Code, Continue
