---
name: wiki-lint
description: Health-check the collaborative wiki for alias collisions, orphan sources, stale pins, missing hub compiles, and ingest invariant violations. Use when asked to lint, after merging parallel ingest branches, or on a weekly pass.
---

# Wiki lint

Read-only except for a **new** log file. Do not silently rewrite hubs or raw sources.

## Vault root

Directory containing `wiki/` and `raw/`. If the git root has `vault/wiki/`, use `vault/`.

## Workflow

1. **Ingest invariant.** `git log` / `git diff` is optional. On the working tree, flag any uncommitted **modifications** under `wiki/hubs/` mixed with ingest files — ingest should not touch hubs.
2. **Alias collisions.** Group `wiki/aliases/*.md` by `canonical`. Flag two alias files that disagree, and distinct hub stems that look like duplicates (`eori` vs `eori-number`) without an alias linking them.
3. **Contributions without compile.** For each folder `wiki/contributions/{hub}/`, if `wiki/hubs/{hub}.md` is missing or its `compiled` date is older than the newest contribution `date`, record a compile gap (not an error).
4. **Orphan sources.** `wiki/sources/{id}.md` with no `wiki/contributions/*/{id}.md` and no hubs listed.
5. **Dangling raw.** `raw/` trees with no matching source note, or source notes whose `raw` path is missing.
6. **Pins.** Pins whose hub folder is gone, or `status: superseded` still treated as active. See [PINS.md](../wiki-compile/references/PINS.md).
7. **Unresolved links.** Wikilinks that are not a hub stem, source stem, alias stem, or other existing note — suggest an alias or a missing contribution.
8. **Write findings** to a new file `wiki/log/{YYYY-MM-DD}-lint-{slug}.md`. Propose questions and sources. Do not compile unless asked.

## Log template

```markdown
---
type: log
op: lint
contributor: alice
date: 2026-08-17
---

# [2026-08-17] lint | weekly

## Alias collisions

- …

## Compile gaps

- [[llm-wiki]] — 2 contributions newer than `compiled`

## Orphans

- …

## Questions

- …
```

## Validation checklist

1. Lint did not modify hubs, sources, contributions, aliases, or pins
2. The only new file is the lint log (or none, if the user asked for chat-only)
3. Findings cite paths; they do not invent hubs
