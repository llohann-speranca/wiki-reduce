---
name: wiki-compile
description: Serially compile contribution notes into hub prose, apply human pins, and optionally rebuild JSON Canvas maps. Use after merging parallel ingest branches, when hub pages are stale, or when the user asks to compile. This is the only skill allowed to rewrite derived wiki files.
---

# Wiki compile

Reduce the create-only stream into derived pages. Run **one compile at a time** after git merge. Independent users should not compile concurrently.

Use `obsidian-markdown` for hub notes, `obsidian-bases` if a Base needs a new view (avoid editing Bases unless a new `type` appeared), and `json-canvas` for optional maps.

Read [PINS.md](references/PINS.md) before writing hubs. Page types: [PAGE-TYPES.md](../wiki-ingest/references/PAGE-TYPES.md).

## Vault root

Directory containing `wiki/` and `raw/`. If the git root has `vault/wiki/`, use `vault/`.

## What you may rewrite

| Path | Role |
|------|------|
| `wiki/hubs/{id}.md` | Synthesized hub prose |
| `wiki/canvases/{id}.canvas` | Optional map (compile-owned) |
| `wiki/hot.md` | Optional session cache; prefer gitignoring it |

Do not modify `raw/`, `wiki/sources/`, `wiki/contributions/`, `wiki/log/`, `wiki/aliases/`, or `wiki/pins/`.

## Workflow

1. **Merge first.** Confirm ingest branches are merged. If the working tree has mixed hub edits from a person, stop and ask.
2. **Select hubs.** Default: every `wiki/contributions/{hub}/` whose newest contribution `date` is newer than `wiki/hubs/{hub}.md` `compiled`, plus hubs the user named.
3. **Read inputs** for each hub: all contributions (sort by `date`), the source notes they cite, active pins, existing hub (if any) for section shape only.
4. **Draft hub prose.** Summarize claims; flag contradictions with `> [!warning]` callouts; wikilink sources (`[[source-id]]`) and related hubs. Set frontmatter `type: hub`, `compiled: {today}`, `sources: [...]`.
5. **Apply pins** per [PINS.md](references/PINS.md). Human additions survive even if sources dropped.
6. **Write** `wiki/hubs/{hub}.md` (create or overwrite — this is the reduce step).
7. **Optional canvas.** If the user asked for a map, write `wiki/canvases/{hub}.canvas` with file nodes pointing at the hub, contributions, and sources. Validate IDs per `json-canvas`.
8. **Optional hot cache.** If the vault uses `wiki/hot.md`, overwrite it with a short "what changed" list (~500 tokens). Do not create it unless the vault already expects it.
9. **Log** as a **new** file `wiki/log/{YYYY-MM-DD}-compile-{slug}.md` (`op: compile`). List hubs rewritten and pin warnings.

## Hub template

```markdown
---
type: hub
compiled: 2026-08-17
contributor: compiler
date: 2026-08-17
sources:
  - example-title
status: active
---

# LLM Wiki

Compiled from contributions. Prefer [[example-title]] over this page when you need quotes.

## Current synthesis

…

## Contradictions

> [!warning] Claim tension
> …

## Open questions

- …
```

## Do not

- Compile during ingest in the same session unless the user explicitly asked for both **and** no other contributor will write hubs concurrently
- Delete contribution files after folding them in
- Use `merge=union` on a shared `index.md` as a substitute for this skill

## Validation checklist

1. Only derived paths plus a new compile log changed
2. Every hub has `type: hub` and `compiled`
3. Wikilinks to sources resolve to `wiki/sources/`
4. Active pins are satisfied or called out (never silently dropped)
5. Canvas (if written) passes the `json-canvas` ID and edge checklist
6. Contributions and pins are byte-identical to pre-compile (compare `git diff -- wiki/contributions wiki/pins`)
