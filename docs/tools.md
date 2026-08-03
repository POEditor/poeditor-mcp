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
| `get_project_details` | Get details of a project: term count, reference language, languages, tags, settings, and creation date |
| `add_project` | Create a new project |
| `update_project` | Update project name, description, reference language, fallback language, or proofreading settings (`proofreading`, `lock_proofread`) |
| `delete_project` | Permanently delete a project (owner only) |
| `sync_project` | Replace all project terms with the provided list — terms not in the list are deleted. Use with caution. |
| `upload_strings_file` | Import from a file; the `updating` param controls what is imported: `terms`, `translations`, or `terms_translations` |
| `export_strings_file` | Export translations in any supported format — returns a 10-minute download URL |

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

### export_strings_file formats

`po`, `pot`, `mo`, `json`, `key_value_json`, `i18next`, `arb`, `csv`, `ini`, `properties`, `resw`, `resx`, `ts`, `apple_strings`, `xliff`, `xliff_1_2`, `rise_360_xliff`, `xlf`, `xmb`, `xtb`, `android_strings`, `yml`, `xcstrings`, `xls`, `xlsx`

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
| `update_terms` | Rename terms or update context, reference, plural, or tags |
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

| Tool | Description |
|---|---|
| `commit_translations` | Add translations for a language — fills only untranslated terms, never overwrites existing |
| `propose_translations` | Add translations flagged as fuzzy (pending review) — existing translations are not overwritten |
| `update_translations` | Overwrite existing translations for a language — untranslated terms are skipped |
| `update_language` | Fill untranslated AND overwrite existing in one call — see Languages section |
| `delete_translations` | Remove translations for specific terms in a language |
| `fill_from_translation_memory` | Fill empty translations with exact matches from Translation Memory; paginates in batches of 100 |

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

---

## Contributors

| Tool | Description |
|---|---|
| `list_contributors` | List contributors across all projects, or filter by project or language |
| `add_contributor` | Add a contributor (or admin) to a project; optionally grant proofreading rights |
| `remove_contributor` | Remove a contributor or admin from a project |

---

## Quality

| Tool | Description |
|---|---|
| `qa_checks` | Run QA checks for a language. Returns flagged translations in a terms-list shape, each with an `errors` array describing what failed. |
| `evaluate_translation_quality` | Run an AI quality evaluation on completed translations for a language, scoring each 0–100 against a reference language using the MQM framework. Requires an AI provider configured on the account. |

### evaluate_translation_quality details

- By default only translations without a score are evaluated; translations that already have a score are skipped unless `include_evaluated` is set, which re-evaluates and replaces them. Edited translations whose score has gone stale are always re-evaluated regardless of `include_evaluated`.
- Processes at most 50 terms per call, in ascending term-id order. When evaluating a whole language (no `terms` list), the response includes `next_term` and `done`; if `done` is false, call again with the same arguments plus `start_from_term_id` set to `next_term` to score the next batch, and repeat until `done` is true.
- Pass `terms` (max 50) to restrict evaluation to specific terms instead of the whole language.
- `source_language` optionally overrides the project's reference language as the comparison target.
- Scores and their MQM reasons are stored on the project and shown in the POEditor editor.

---

## Automation

| Tool | Description |
|---|---|
| `automatic_translation` | Machine-translate untranslated terms using Google, Microsoft, or DeepL; can filter by tag |

### automatic_translation providers

| Provider | `provider` value | Language code format |
|---|---|---|
| Google Translate | `google` | `en`, `fr`, `zh-CN` |
| Microsoft Translator | `microsoft` | `en`, `fr`, `zh-Hans` |
| DeepL | `deepl` | `EN`, `FR`, `ZH` (uppercase) |

---

## Account

| Tool | Description |
|---|---|
| `get_account_info` | Account name, email, plan, enabled features, and total string count |
