# Changelog

All notable changes to the POEditor MCP server.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- `mark_translations_proofread` — set or clear the proofread flag on
  translations for a language; requires proofreading enabled on the project
- `qa_checks` — run QA checks for a language; returns flagged translations
  with per-item `errors` arrays
- `evaluate_translation_quality` — AI quality evaluation of completed
  translations, scored 0–100 per language against a reference language
  using the MQM framework; paginates in batches of 50 terms
- `list_quality_evaluations` — list the stored AI quality-evaluation
  scores for a language (the results produced by
  `evaluate_translation_quality`); paginated
- `rise_360_xliff` export format added to `export_strings_file`
- Documented project settings flags (`proofreading`, `lock_proofread`,
  `moderate`, `allow_add_language`, `lock_complete`,
  `fuzzy_trigger_on_default_language`) returned by `get_project_details`
  and writable via `update_project`
- `figma_export` — export a Figma page's text layers into a POEditor
  project as terms and reference-language translations, via the user's own
  Figma MCP connection and client-side hook
- `figma_import` — import POEditor translations into Figma Variables for a
  page, binding text layers to per-language modes; non-destructive and
  idempotent
- `figma_remove_localization_collection` — destructive recovery tool to
  remove the `POEditor Localization` variable collection and its bindings
  from a file
- `figma_remove_obsolete` — delete POEditor terms whose source Figma text
  node no longer exists
- `get_project_stats` — daily translation activity for a project over a
  date range (strings, words, chars, per-language breakdown, contributors)
- `get_contributor_stats` — daily translation activity for one contributor,
  per project or aggregated across all projects you own or administer
- Glossary management (15 tools): `list_glossaries`,
  `get_glossary_details`, `add_glossary`, `update_glossary`,
  `delete_glossary`, `assign_glossary_to_projects`,
  `unassign_glossary_from_projects`, `list_glossary_terms`,
  `add_glossary_terms`, `update_glossary_terms`, `delete_glossary_terms`,
  `set_glossary_translations`, `delete_glossary_translations`,
  `import_glossary`, `export_glossary` (CSV)
- Total tool count: 34 → 38 → 43 → 59 (the earlier 43 overcounted by one;
  the actual count before this release was 42)

---

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
