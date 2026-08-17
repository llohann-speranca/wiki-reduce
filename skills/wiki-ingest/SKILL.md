---
name: wiki-ingest
description: Ingest a raw source into the wiki as create-only notes (source page, per-hub contributions, log file, alias stubs). Use when adding a document, article, paper, meeting note, URL, or dump to the wiki. Never patch existing pages.
---

# Wiki ingest

Compile a source into **new unique files**. Do not modify `raw/` or any existing wiki page.

Read [PAGE-TYPES.md](references/PAGE-TYPES.md) for properties and paths. Use Obsidian Flavored Markdown (wikilinks, properties, callouts) from `obsidian-markdown`. Extract web pages with `defuddle` when the source is a URL.

## Vault root

The vault root is the directory that contains `wiki/` and `raw/`. If the git root has `vault/wiki/`, the vault root is `vault/`. All paths below are relative to the vault root.

## Workflow

1. **Identify the source.** Leave it immutable. If it is not already under `raw/{contributor}/{source-id}/`, copy or clip it there first (copy is a new path; do not edit the original elsewhere).
2. **Choose `source-id`.** Kebab-case slug. If `wiki/sources/{source-id}.md` exists, append an 8-char hex suffix. Never overwrite.
3. **Read for routing.** Open existing Bases (`wiki/sources.base`, `wiki/contributions.base`) or grep `canonical:` under `wiki/aliases/` to reuse hub stems. Do not invent a second stem for a known concept; write `wiki/aliases/{new-stem}.md` pointing at the canonical instead.
4. **Write the source note** `wiki/sources/{source-id}.md`.
5. **Write one contribution per hub** `wiki/contributions/{hub-id}/{source-id}.md`. Wikilink `[[hub-id]]` even if `wiki/hubs/{hub-id}.md` does not exist.
6. **Reserve aliases** only for stems you introduced, as new files under `wiki/aliases/`.
7. **Write a log file** `wiki/log/{YYYY-MM-DD}-{source-id}.md` with `op: ingest`.
8. **Validate** (checklist below). Stop. Do not run compile unless the user asked.

A single source may produce many contribution files. That is expected. Prefer many small create-only notes over patching a hub.

## Do not

- Edit `wiki/hubs/`, `wiki/hot.md`, or any existing `.md` / `.base` / `.canvas`
- Append to a shared `log.md` or rewrite `index.md` / `skills-map.md`
- Create a hub file so the wikilink "resolves"
- Copy secrets, passwords, or credentials into git

## Source note template

```markdown
---
type: source
title: Example title
source_id: example-title
contributor: alice
date: 2026-08-17
raw: raw/alice/example-title/source.md
hubs:
  - llm-wiki
origin: https://example.com
status: active
---

# Example title

One-paragraph summary.

## Key claims

- Claim, with a [[llm-wiki]] link.

## Quotes

> Short quote from the raw source.

## Related

- [[llm-wiki]]
```

## Contribution template

```markdown
---
type: contribution
hub: llm-wiki
source_id: example-title
contributor: alice
date: 2026-08-17
kind: claim
status: active
---

# llm-wiki ← example-title

- What this source adds to [[llm-wiki]].
- Cite [[example-title]].

> [!warning] Contradiction
> Optional callout if this source disagrees with another contribution.
```

## Log template

```markdown
---
type: log
op: ingest
source_id: example-title
contributor: alice
date: 2026-08-17
hubs:
  - llm-wiki
---

# [2026-08-17] ingest | Example title

Created:

- `wiki/sources/example-title.md`
- `wiki/contributions/llm-wiki/example-title.md`
```

## Alias template

```markdown
---
type: alias
canonical: llm-wiki
contributor: alice
date: 2026-08-17
---
```

Filename stem = alias (`llm-wiki.md`, `llmwiki.md`).

## Validation checklist

After writing, verify:

1. Every new file has required properties for its `type` (see [PAGE-TYPES.md](references/PAGE-TYPES.md))
2. `source_id` matches the source filename stem
3. Each contribution path is `wiki/contributions/{hub}/{source-id}.md`
4. Wikilinks use `[[stem]]` for vault notes, `[text](url)` for external URLs only
5. No existing file was modified — `git status` shows added paths only (plus untracked). If `git status` lists a modified file, revert it
6. Raw path exists and was not edited
7. You did not create `wiki/hubs/{hub}.md`

If validation fails, delete the new files you just added and redo; do not "fix" by editing a hub.
