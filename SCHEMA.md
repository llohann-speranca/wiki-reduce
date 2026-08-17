# Collaborative LLM Wiki

A pattern for building a persistent markdown wiki with LLM agents — redesigned so **independent users can ingest in parallel without git conflicts**.

This is an idea file. Copy it to an agent with the skills in this repo. The skills are the operational contract; this file is the why.

It extends [Karpathy's LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). The writing and file conventions follow [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills): Obsidian Flavored Markdown, properties, wikilinks, Bases, and JSON Canvas.

## The core idea

Most people's experience with LLMs and documents looks like RAG: you upload files, the model retrieves chunks at query time, and generates an answer. Nothing accumulates. Ask a subtle question that requires five documents, and the model re-discovers the fragments every time.

Karpathy's alternative: the LLM **incrementally compiles** raw sources into a persistent wiki — interlinked markdown that sits between you and the sources. Knowledge is compiled once and kept current.

That pattern assumes **one ingest at a time**, rewriting shared pages (`index.md`, `log.md`, entity hubs). A single source may touch 10–15 files. Two independent users ingesting at once both edit the same hubs and catalogs. Git conflicts. Worse, concurrent planners create near-duplicate pages (`eori` vs `eori-number`) because each snapshot never saw the other's claim.

This redesign keeps the compiled wiki and splits the work:

- **Ingest (map)** is embarrassingly parallel. Each contributor only **creates unique files**. Git merge is clean by construction.
- **Query** works immediately via Obsidian Bases and backlinks. You do not wait for a compile.
- **Compile (reduce)** is serial. One maintainer or CI pass synthesizes hub prose, canvases, and a session cache from the contribution stream.

You still rarely write the wiki yourself. You curate sources and ask questions. Agents do the filing. Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase.

## Why shared pages conflict

Karpathy ingest is read-modify-write of shared artifacts:

- Patch every relevant hub
- Rewrite `index.md`
- Append `log.md` (two appends to the same file still conflict)
- Revise overview / synthesis

[AutoSci](https://github.com/skyllwt/AutoSci) (the research/discovery instantiation of the gist — see below) parallelizes with git worktrees and `merge=union` on accumulators, then a serial fan-in. That needs coordinated worktrees. Independent users committing on their own laptops do not share a worktree.

**Hard ingest rule:** ingest may **create** a file whose path is unique. It must **not modify any existing file**.

## Architecture

Four layers:

**Raw sources** — curated immutable documents under `raw/{contributor}/{source-id}/`. Agents read; they never edit. This is the source of truth for what was ingested.

**Contributions** — LLM-written, create-only notes: one source page, per-hub contribution pages, per-ingest log files, alias reservations, optional pins. Independent users only add files here.

**Compiled wiki** — hub prose, canvases, optional `hot.md`. Owned by the **compile** skill. The only layer allowed to rewrite existing derived files.

**Schema** — `SCHEMA.md` (this file) plus `AGENTS.md` and the skills. Co-evolve conventions; do not encode domain ontology here.

Obsidian **Bases** replace Karpathy's `index.md`. A Base is a query over properties, not a file everyone edits. Wikilinks to a hub work even if the hub file does not exist yet (unresolved links). Backlinks collect contributions without patching the hub.

## Operations

**Ingest.** Drop a source into `raw/{contributor}/{source-id}/` and run `wiki-ingest`. The agent writes:

- `wiki/sources/{source-id}.md`
- `wiki/contributions/{hub-id}/{source-id}.md` for each hub the source touches
- `wiki/log/{YYYY-MM-DD}-{source-id}.md`
- `wiki/aliases/{alias}.md` when reserving a slug

It wikilinks hubs. It does not create or patch hub files. Validate with `git status`: **only added paths**.

**Query.** Run `wiki-query`. Read Bases and backlinks first, then source/contribution pages. Cite them. File a reusable answer as a **new unique page** (decision or contribution), not a hub rewrite.

**Lint.** Run `wiki-lint`. Look for alias collisions, contributions whose hub was never compiled, stale pins, orphan sources, unresolved wikilinks that should become aliases.

**Compile.** Run `wiki-compile` after merging parallel ingest branches. Serial. Rebuilds `wiki/hubs/`, optional `.canvas` maps, optional `wiki/hot.md`. Re-applies pins so human corrections survive regeneration.

## Indexing without `index.md`

Karpathy's `index.md` is a catalog the agent rewrites on every ingest. At team scale it is both a merge bottleneck and a lossy summary.

Use:

- YAML properties on every note (`type`, `source_id`, `contributor`, `hubs`, `date`, `status`)
- Bases (`wiki/sources.base`, `wiki/log.base`, `wiki/contributions.base`) as the catalog
- Wikilinks + backlinks as the graph
- Optional JSON Canvas at compile time for a spatial map
- Full-text search (`rg`, or [qmd](https://github.com/tobi/qmd) when the vault outgrows grep) **unioned** with Base hits — never a replacement that can drop recall

`log.md` becomes one file per ingest. Chronological views are a Base sorted by `date`, not an append-only shared file.

## Aliases and near-duplicates

Concurrent ingest forks names when two agents invent slugs from the same concept. Reserve names as files:

```text
wiki/aliases/eori.md        → canonical: eori-number
wiki/aliases/eori-number.md → canonical: eori-number
```

Creating the same alias file with the same body merges cleanly. Disagreeing canonicals on the **same path** is a real git conflict — that disagreement should be visible, not silently forked. Lint then clusters remaining near-duplicates.

## Pins

If a human corrects compiled hub prose, the next compile must not revert it. Store the **intent**, not a line-diff, as `wiki/pins/{hub-id}/{id}.md` (`correction` | `addition` | `deletion`). Compile re-checks each pin against the new text. See `skills/wiki-compile/references/PINS.md`.

## What this is not

- Not RAG as the primary memory (query the wiki first).
- Not AutoSci: no experiment runner, paper drafts, or rebuttal loop.
- Not a requirement for worktrees, `merge=union`, or a lock server.
- Not a domain ontology. Page types here are structural (`source`, `contribution`, `hub`, …). Domain types (project, lab, paper) belong in the consuming vault's `AGENTS.md`.

## Research / discovery analogue

Karpathy's gist is domain-agnostic. The well-known research instantiation is **[AutoSci](https://github.com/skyllwt/AutoSci)** (evolved from [OmegaWiki](https://github.com/skyllwt/OmegaWiki)):

- Paper: [arXiv:2605.31468](https://arxiv.org/abs/2605.31468)
- `/discover` ranks candidate papers against the existing wiki without ingesting
- `/ingest` compiles papers into typed pages; `/daily-arxiv` is the feed
- Parallel ingest uses worktrees + union-merge, then serial fan-in

Use AutoSci when the loop is literature → idea → experiment → manuscript. Use this pack when the loop is **multi-user ingest into one Obsidian vault** without coordinated worktrees.

Thinner relatives: [MetamusicX/llm-research-wiki](https://github.com/MetamusicX/llm-research-wiki), Hermes bundled `research-llm-wiki`.

## Note

Directory names, domain page types, and extra output formats are yours. The invariant is the ingest rule: **unique new paths only**. Compile is the only writer of shared derived pages. Bases are the index.
