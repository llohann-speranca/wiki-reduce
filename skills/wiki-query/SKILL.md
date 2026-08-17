---
name: wiki-query
description: Answer questions from the compiled wiki using Bases, backlinks, and source/contribution pages; file reusable answers as new unique pages. Use when asking what the wiki knows, what a hub claims, or how sources relate — not when ingesting a new document.
---

# Wiki query

Answer from the vault. Do not RAG raw dumps first. Do not patch hubs to "save" the answer.

## Vault root

Directory containing `wiki/` and `raw/`. If the git root has `vault/wiki/`, use `vault/`.

## Workflow

1. **Catalog.** Open `wiki/sources.base`, `wiki/contributions.base`, and `wiki/log.base` (or filter notes with `type` in frontmatter). There is no `index.md` to rewrite.
2. **Resolve names.** If the question uses a synonym, read `wiki/aliases/{stem}.md` for `canonical`.
3. **Hub view.** If `wiki/hubs/{id}.md` exists, read it as the last compile — then read **backlinks** / `wiki/contributions/{id}/` so you do not miss ingest that landed after compile.
4. **Drill into sources** cited by those contributions. Quote and wikilink.
5. **Union search.** If Bases miss a buried fact, `rg` (or qmd) over `wiki/` and **union** those hits with the Base set. Do not replace the Base set.
6. **Cite** wiki paths and `source_id`s. Distinguish compiled hub prose from create-only contributions.
7. **File back** if the answer should compound: write a **new** unique page (contribution, log with `op: query`, or a domain decision page the vault defines). Never edit the hub. Offer compile if the user wants hub prose updated.
8. **Federated wikis.** If sibling git repos each have `wiki/hot.json` or `wiki/log/*.json`, follow [FEDERATE.md](references/FEDERATE.md): read in place, do not copy files, write only in the repo where the work happened.

Answers that die in chat do not compound.

## Do not

- Start from Monday, email, or other external systems when wiki pages exist
- Edit `wiki/hubs/` (that is `wiki-compile`)
- Append to a shared `log.md`

## Filing a query result

Use a unique path, for example:

```text
wiki/contributions/{hub}/{YYYY-MM-DD}-query-{slug}.md
wiki/log/{YYYY-MM-DD}-query-{slug}.md
```

Set `op: query` on the log file. Set `contributor` to the asking user. In a federated wiki, prefer `wiki/log/{YYYY-MM-DD}-query-{slug}.json` ([JSON-RECORDS.md](../wiki-ingest/references/JSON-RECORDS.md)).

## Validation checklist

1. Citations point at existing `wiki/` files or unresolved `[[hub]]` stems that have contributions
2. Any new file follows [PAGE-TYPES.md](../wiki-ingest/references/PAGE-TYPES.md)
3. `git status` shows no modifications to hubs, Bases, or canvases unless the user explicitly asked to compile
