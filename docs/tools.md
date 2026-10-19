# Tools

The live tool list is served by the MCP endpoint via `tools/list`. The reference below matches the current server exactly.

## Important: term + context as composite key

POEditor uses **`term` + `context`** as the unique identifier. All operations on a term must include the same context it was created with. Mismatches result in silently unmatched updates.

```json
{ "term": "save", "context": "button" }
```

vs.

```json
{ "term": "save", "context": "menu" }
```

These are two different entities.

---

## Projects

| Tool | Description |
|---|---|
| `list_projects` | List all projects accessible to the authenticated user |
| `get_project_details` | Get details of a project: term count, reference language, fallback language, languages with per-language progress, tags, settings, assigned glossary, and creation date |
| `add_project` | Create a new project in the authenticated account — there is no account parameter. The response includes an `account` block showing where the project was created. |
| `update_project` | Update project name, description, reference language, fallback language, or proofreading settings (`proofreading`, `lock_proofread`) |
| `delete_project` | Permanently delete a project (owner only) |
| `sync_project` | Replace all project terms with the provided list — terms not in the list are deleted. Use with caution. |
| `upload_strings_file` | Import from a file; the `updating` param controls what is imported: `terms`, `translations`, or `terms_translations`. Format is auto-detected from `file_name` or forced with `type`. Optional `overwrite`, `sync_terms` (delete terms not in the file), `fuzzy_trigger`, and `tags`. Limited to one request every 20 seconds. |
| `export_strings_file` | Export translations in any supported format — returns a 10-minute download URL. Optional `filters`, `order`, `options`, and `tags` with `tags_match` (see [Tag filters](#tag-filters)). |

### Assigned glossary

`get_project_details` returns a `glossary` object, or `null` when the project
has none. When `accessible` is true it carries `id`, `name`,
`source_language`, and `shared`, and can be opened with `get_glossary_details`
or `list_glossary_terms`. When `accessible` is false the project has a glossary
you can't access, and its name is masked as `######`.

### Project settings flags

Returned by `get_project_details` in the `settings` object; some are writable via `update_project`. A few only appear when their prerequisite is in place.

| Flag | Meaning | Present / writable when |
|---|---|---|
| `public` | 1 = publicly viewable, 0 = private | Always present (read-only) |
| `open` | 1 = Open Source project — strings are free and don't count against the plan quota | Always present (read-only) |
| `fallback_language` | Language substituted on export when a translation is missing | Always present; writable via `update_project` |
| `proofreading` | 1 = proofreading enabled for the owner, admins, and contributors with proofreading rights | Writable via `update_project` |
| `lock_proofread` | 1 = contributors without proofreading rights cannot edit or delete translations flagged as proofread | Present only while `proofreading` is enabled; writable via `update_project` (takes effect only while proofreading is enabled) |
| `moderate`, `allow_add_language`, `lock_complete` | Public-project workflow flags | Present only when the project is public (`public: 1`) |
| `fuzzy_trigger_on_default_language` | Whether editing the reference-language translation marks the same term's other-language translations as fuzzy | Present only when a reference language is set |

### File formats

Used by `export_strings_file` and `upload_strings_file` (`type`):

`po`, `pot`, `mo` (export only), `json`, `key_value_json`, `i18next`, `arb`, `csv`, `ini`, `properties`, `resw`, `resx`, `ts`, `apple_strings`, `xliff`, `xcstrings`, `stringsdict`, `xliff_1_2`, `rise_360_xliff`, `xlf`, `xmb`, `xtb`, `android_strings`, `yml`, `xls`, `xlsx`

### Tag filters

`export_strings_file` and `automatic_translation` take `tags` (tag names) and
`tags_match`:

| `tags_match` | Selects |
|---|---|
| `all` (default) | Terms carrying every listed tag |
| `any` | Terms carrying at least one listed tag |
| `exclude` | Terms carrying none of the listed tags (untagged terms included) |

`automatic_translation` rejects a tag name that doesn't exist rather than
ignoring it, so a typo never translates the whole language.

---

## Languages

| Tool | Description |
|---|---|
| `available_languages` | Full list of all languages POEditor supports (280+), with names and codes |
| `list_languages` | Languages enabled in a project, with translation % and last update time |
| `add_language` | Add a language to a project |
| `update_language` | Bulk add-or-overwrite: fills untranslated terms AND overwrites existing in one call. Use `commit_translations` or `update_translations` for narrower behavior. Supports `fuzzy_trigger`. |
| `delete_language` | Remove a language and all its translations from a project |

---

## Terms

| Tool | Description |
|---|---|
| `list_terms` | List terms in a project; pass a language code to include translations |
| `add_terms` | Add one or more terms |
| `update_terms` | Rename terms or update context, reference, plural, or tags; `fuzzy_trigger` marks their translations fuzzy |
| `delete_terms` | Delete terms and all their translations |
| `get_term_details` | Full details for a single term: content, plural, tags, comments, and all translations across every project language |
| `add_term_comment` | Add a comment to one or more terms |

---

## Translations

### Writing translations

Four tools write translations — pick the right one:

| Tool | Fills untranslated | Overwrites existing | Marks as fuzzy |
|---|---|---|---|
| `commit_translations` | ✓ | ✗ | ✗ |
| `propose_translations` | ✓ | ✗ | ✓ always |
| `update_translations` | ✗ | ✓ | ✗ |
| `update_language` | ✓ | ✓ | per-item flag |

For a plural term, `translation.content` is an object keyed by CLDR plural
form (`zero`, `one`, `two`, `few`, `many`, `other`) instead of a string; only
the forms the target language uses are required:

```json
{ "term": "%d Projects available", "context": "project list",
  "translation": { "content": { "one": "Un projet disponible", "other": "%d Projets disponibles" } } }
```

| Tool | Description |
|---|---|
| `commit_translations` | Add translations for a language — fills only untranslated terms, never overwrites existing |
| `propose_translations` | Add translations flagged as fuzzy (pending review) — existing translations are not overwritten |
| `update_translations` | Overwrite existing translations for a language — untranslated terms are skipped |
| `update_language` | Fill untranslated AND overwrite existing in one call — see Languages section |
| `delete_translations` | Remove translations for specific terms in a language |
| `fill_from_translation_memory` | Fill empty translations with exact matches from Translation Memory. Processes at most 100 terms per call: when filling a whole language, call again with `start_from_term_id` set to `next_term` until `done` is true. A `terms` list is capped at 100. |

### Reading translations

| Tool | Description |
|---|---|
| `list_fuzzy_translations` | List translations currently marked as fuzzy for a language |
| `list_pending_proofread` | List translated-but-not-proofread strings for a language (requires proofreading enabled) |
| `list_untranslated` | List terms that have no translation yet for a given language |

### Proofreading

| Tool | Description |
|---|---|
| `mark_translations_proofread` | Set or clear the proofread flag on translations for a language. Pass a `data` array of objects with `term`, `context`, and `flag.proofread` set to 1 or 0. Returns parsed, matched, and updated counts. Requires proofreading to be enabled on the project. |

---

## Progress

| Tool | Description |
|---|---|
| `get_translation_status` | Translation progress per language: total strings, translated, fuzzy, and proofread counts |
| `get_proofread_progress` | Proofreading progress per language: total strings, proofread count, and percentage (requires proofreading enabled) |
| `get_project_stats` | Daily translation activity for a project over a date range — strings, words, chars, source counts, per-language breakdown, and contributor emails — plus a `total` for the whole range |

### Activity stats

`get_project_stats` and `get_contributor_stats` share the same date handling:

- `startDay` / `endDay` are `Y-m-d`, inclusive. `endDay` defaults to yesterday — no data exists for the current day yet. `startDay` defaults to 29 days before `endDay` (a 30-day window).
- Pass `language` to restrict the stats to one language.

---

## Contributors

| Tool | Description |
|---|---|
| `list_contributors` | List contributors across all projects, or filter by project, or by project and language |
| `add_contributor` | Add a contributor (or admin) to a project; optionally grant proofreading rights |
| `remove_contributor` | Remove a contributor or admin from a project |
| `get_contributor_stats` | Daily translation activity for one contributor (by `email`) over a date range, plus a `total`. Pass a project `id` to restrict to one project; omit it to aggregate across every project you own or administer. The contributor must belong to one of those projects. See [Activity stats](#activity-stats). |

---

## Quality

| Tool | Description |
|---|---|
| `qa_checks` | Run QA checks for a language. Returns flagged translations in a terms-list shape, each with an `errors` array describing what failed. |
| `evaluate_translation_quality` | Run an AI quality evaluation on completed translations for a language, scoring each 0–100 against a reference language using the MQM framework. Requires an AI provider configured on the account. |
| `list_quality_evaluations` | List the stored AI quality-evaluation scores for a language — the results produced by `evaluate_translation_quality`. Paginated. |

### evaluate_translation_quality details

- By default only translations without a score are evaluated; translations that already have a score are skipped unless `include_evaluated` is set, which re-evaluates and replaces them. Edited translations whose score has gone stale are always re-evaluated regardless of `include_evaluated`.
- Processes at most 50 terms per call, in ascending term-id order. When evaluating a whole language (no `terms` list), the response includes `next_term` and `done`; if `done` is false, call again with the same arguments plus `start_from_term_id` set to `next_term` to score the next batch, and repeat until `done` is true.
- Pass `terms` (max 50) to restrict evaluation to specific terms instead of the whole language.
- `source_language` optionally overrides the project's reference language as the comparison target; without it, a project with no reference language returns an error.
- `context` optionally gives the AI a short project description to improve scoring.
- Keep `include_evaluated` the same across every call of one paginated run.
- Scores and their MQM reasons are stored on the project and shown in the POEditor editor.

### list_quality_evaluations details

- Returns a terms-list-shaped JSON; each translation carries a `quality` object (score and MQM reasons) and a `source` block — the string the score was measured against and its language. The source isn't necessarily the project's reference language, since an evaluation can be run against any project language.
- Only translations that lost points are listed — a perfect (100) score has nothing to report. Translations never evaluated are also omitted; run `evaluate_translation_quality` to score them.
- Ordered by term, in the order strings are listed in the project.
- A score describes the translation as it was when evaluated. A translation edited since it was scored has no valid score and is dropped from the list until re-evaluated — compare the translation's `updated` date against the score's `evaluated_at` for the edge case of an edit landing mid-page.
- Paginated: when the response carries `nextCursor`, call again with the same arguments plus `cursor` set to that value, and repeat until there is no `nextCursor`. Treat the cursor as opaque — don't parse, modify, or construct one.

---

## Glossaries

Glossaries are account-level: owned by one account and assigned to projects,
where they're used in the editor, in QA checks, and in automatic and AI
translation where enabled. A glossary can be assigned to many projects; a
project has at most one glossary. Requires a plan that includes glossaries.

| Tool | Description |
|---|---|
| `list_glossaries` | Glossaries the account owns, plus shared glossaries of projects it's an admin or contributor in — with source language, shared flag, your role, term/translation counts, and assigned projects. Also returns the account's glossary string limit. |
| `get_glossary_details` | One glossary: name, source language, shared flag, your role and permissions, counts, languages, assigned projects, and the owner's string limit |
| `add_glossary` | Create a glossary (owner). Name must be unique, at most 30 characters, with at least 3 consecutive letters or digits. Optionally `shared`. |
| `update_glossary` | Rename, change source language, or share/unshare (owner). Unsharing removes access for the admins and contributors of its projects. |
| `delete_glossary` | Permanently delete a glossary with all its terms and translations, and unassign it everywhere (owner). Cannot be undone. |
| `assign_glossary_to_projects` | Assign a glossary to one or more projects. Projects that already have another glossary are skipped unless `replace_existing` is true. |
| `unassign_glossary_from_projects` | Remove a glossary from projects; the glossary and its terms are kept |
| `list_glossary_terms` | Terms with their translations; filter by `languages`, `part_of_speech`, or `search`. Paginated, 100 terms per call. |
| `add_glossary_terms` | Add terms, optionally with translations. Max 100 per call. |
| `update_glossary_terms` | Edit term text, part of speech, description, or `do_not_translate` by term id. Max 100 per call. |
| `delete_glossary_terms` | Permanently delete terms and their translations by term id. Max 100 per call. |
| `set_glossary_translations` | Add or replace term translations (called *definitions* in the POEditor UI). Max 100 per call. |
| `delete_glossary_translations` | Delete translations of terms; the terms stay. Max 100 per call. |
| `import_glossary` | Bulk-load terms and translations from CSV text (owner) |
| `export_glossary` | Export a whole glossary as CSV text, in the same format `import_glossary` reads |

### Glossary roles

| Role | Can do |
|---|---|
| Owner | Everything, including create, update, delete, assign/unassign, and import |
| Admin of an assigned project | Add, edit, and delete terms; translate into any language; export (shared glossaries only) |
| Contributor of an assigned project | See the glossary and translate into the languages they have access to (shared glossaries only) |

Assign/unassign requires owning both the glossary and the projects.

### Glossary terms

- A term is unique by its **text + part of speech** — the same text can exist
  once as a noun and once as a verb. `part_of_speech` is one of `other`
  (default), `noun`, `verb`, `adjective`, `pronoun`.
- `do_not_translate` marks brand or product names that must stay
  untranslated; such a term cannot have translations. Setting it to `true`
  via `update_glossary_terms` (or `import_glossary` with `overwrite`)
  **deletes** all existing translations of that term.
- `description` is optional, at most 200 characters.
- Every term and every translation counts as one string against the owner's
  glossary string limit. Items past the limit are rejected (or, on import,
  skipped and reported as `limit_reached`).
- `add_glossary_terms`, `update_glossary_terms`, and
  `set_glossary_translations` process items one by one and report a result
  per item — one bad item doesn't stop the rest.
- `list_glossary_terms` paginates with an opaque `nextCursor`: call again with
  the same arguments plus `cursor`, until there is no `nextCursor`.
- A project with unreviewed AI glossary term suggestions can't have its
  glossary unassigned or replaced until they're resolved in the POEditor UI.

### import_glossary / export_glossary CSV

Same format the POEditor UI uses. The header row requires `term`; `part_of_speech`,
`description`, and `do_not_translate` (`1`/`yes`/`true`) are optional; every
other column is a language code holding translations (unknown codes are ignored).

```csv
term,part_of_speech,description,fr,de
Dashboard,noun,Main screen,Tableau de bord,Dashboard
```

Terms are matched by text + part of speech: new ones are added; existing ones
only get their missing translations — unless `overwrite` is true, which also
replaces description, `do_not_translate`, and existing translations. Import
never deletes terms.

---

## Automation

| Tool | Description |
|---|---|
| `automatic_translation` | Machine-translate untranslated terms using Google, Microsoft, or DeepL; filter with `tags` and `tags_match` (see [Tag filters](#tag-filters)). When POEditor's automatic translation characters are used, the response includes how many are left on the account. |

### automatic_translation providers

| Provider | `provider` value | Language code format |
|---|---|---|
| Google Translate | `google` | `en`, `fr`, `zh-CN` |
| Microsoft Translator | `microsoft` | `en`, `fr`, `zh-Hans` |
| DeepL | `deepl` | `EN`, `FR`, `ZH` (uppercase) |

---

## Figma

Round-trips text between a Figma file and a POEditor project. Requires the
user's own Figma MCP connection and client-side hook — this server never
contacts Figma or holds a Figma credential; it only reads/writes the
POEditor side and exchanges opaque scripts with the client for the Figma side.

| Tool | Description |
|---|---|
| `figma_export` | Export the text layers of one Figma page into a POEditor project as terms + reference-language translations |
| `figma_import` | Import POEditor translations into Figma Variables for one page — one variable collection (`POEditor Localization`), one mode per language, text layers bound to their variable |
| `figma_remove_localization_collection` | Destructive recovery: remove the `POEditor Localization` variable collection and its bindings from a file, freezing whatever text is currently displayed |
| `figma_remove_obsolete` | Delete POEditor terms whose source Figma text node no longer exists |

### key_mode

All four tools take a `key_mode`, required with no default, describing how a
Figma text layer maps to a POEditor term. It must be the same mode across
export, import, and cleanup for a given project/file pair — mixing modes
creates a second, unrelated set of terms.

| Mode | Term shape | Notes |
|---|---|---|
| `id` | Figma node id, e.g. `123:456` | Survives renames and moves to another page; node ids are unique within a file |
| `name` | Figma layer name, e.g. `login_button` | Readable in POEditor; renaming a layer creates a new term; two same-named layers with different text collide (last in document order wins, reported under `duplicates`) |
| `page_scoped_id` | `page:<page_id>\|node:<node_id>` | The only mode where a term is provably one this integration created — required for `figma_remove_obsolete` to delete rather than preview |

Neither the tool nor the assistant chooses a `key_mode` on the user's
behalf — omitting it returns `needs_input` listing the three options.

### Project and file identification

- All four tools take `project` as a POEditor project **name or id** — the
  server resolves it, so there's no need to call `list_projects` first. A
  name matching several projects or none returns `needs_input` with the
  candidates. The older `id` / `project_id` fields are still honoured.
- `figma_file_key` and `figma_url` each accept either a bare file key or a
  full Figma URL. (`figma_export` is the exception: its client-side hook needs
  the bare key in `figma_file_key`, with the link in `figma_url`.) Two fields
  naming different files are refused.
- `figma_export` and `figma_import` discover the page: omit `page`/`page_id`
  and a single-page file runs immediately, while a multi-page file returns
  `needs_input` with the choices. A `node-id` in a pasted URL selects its page.
  `page` accepts a page name or id.

### figma_export

- Project, page, and reference language are discovered, not looked up or
  guessed: pass the bare `figma_file_key` (and `figma_url` if the user gave a
  link) and `project` by name — a file with one page exports immediately, a
  file with several returns `needs_input` with the page choices.
- `host_figma_snapshot` is attached by a client-side PreToolUse hook that runs
  a read-only script over the user's existing Figma MCP connection; it is
  never supplied by the caller. A response of `figma_host_snapshot_required`
  means that hook isn't installed — there is no fallback read path.
- Paginates large pages via an opaque `continuation` token, echoed back
  unmodified on the next call.
- `accept_conflicts` resolves terms edited in both Figma and POEditor since
  the last `figma_import` (default: left unwritten) — `true` for all
  conflicts, or a list of specific POEditor terms.

### figma_import

- Non-destructive and idempotent — only creates, updates, and binds; never
  deletes a variable, collection, node, or POEditor term, and never touches a
  remote/library collection. Re-running is always safe.
- One page per run; there is no whole-file mode. The page is discovered as
  described above.
- `languages` and `reference_language` default to the project's own — omit
  them rather than pre-fetching with `list_languages`. A Figma variable
  collection holds at most 10 modes, capping languages per run at 10.
- Values edited directly in Figma since the last import are kept and
  reported as `figma_edit_kept`, not overwritten — run `figma_export` to send
  those edits back to POEditor as fuzzy translations, or pass
  `overwrite_figma_edits: true` to let POEditor's text win instead.
- Drives Figma through round trips with the client: each response may carry a
  script plus a signed `session_state` to echo back unchanged along with
  `result` (whatever the client's `use_figma` call returned, untouched).

### figma_remove_localization_collection

- **Destructive** and user-driven — never run without the user explicitly
  asking, and never combine "switch language, preview, confirm" into one
  automated step.
- Whole file, not one page — a variable collection is file-level.
- Freezes whatever the file is **currently displaying**, not the POEditor
  reference language; switch the file (and any frame set to its own mode) to
  the language to keep before confirming.
- Preview by default (`confirm: false`); pass `confirm: true` to execute.
  Atomic: if any text layer can't be unbound safely, nothing is deleted.
- A confirmed run is refused if any bound text layer is showing a mode other
  than the collection's default (the reference language); pass
  `accept_non_default_mode: true` only when the user wants that other
  language frozen.
- `project_id` is required when the collection carries an ownership stamp
  from the POEditor Figma plugin, so the stamp can be checked. Collections
  created by `figma_import` carry no stamp.

### figma_remove_obsolete

- **Destructive when confirmed**, read-only against Figma otherwise — no
  Figma node, variable, collection, or binding is ever modified.
- Safety depends on `key_mode`: `page_scoped_id` is provably safe to confirm;
  `id` checks existence authoritatively but ownership is only heuristic
  (review before confirming); `name` is preview-only — `confirm` is refused.
- Preview by default (`confirm: false`); a positive "not there" lookup is
  required to delete — a failed lookup keeps the term rather than deleting it.
- Only context-less terms of the shape the chosen `key_mode` creates are
  considered. With `page_scoped_id`, a node moved to another page counts as
  obsolete; with `id`, the page isn't checked.

---

## Account

| Tool | Description |
|---|---|
| `get_account_info` | Account name, email, plan, plan expiry date, enabled features, and total string count |
