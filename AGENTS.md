# AGENTS.md

Read [SCHEMA.md](SCHEMA.md) at the start of every session in this repo.

This repository is a **reusable LLM-wiki skill pack**, not a domain wiki. Operational rules live in `skills/`. The example vault lives in `vault/`.

## Vault root

Skills treat the directory that contains `wiki/` and `raw/` as the vault root.

- Developing this pack: vault root is `vault/`.
- Using the pack inside another vault: vault root is that vault (e.g. a repo that already has `wiki/`).

Do not assume the git root and the vault root are the same.

## Skills

| Skill | When |
|-------|------|
| `wiki-ingest` | Add a source. Create-only. Never patch existing files. |
| `wiki-query` | Answer from Bases, backlinks, and pages. File reusable answers as new unique pages. |
| `wiki-lint` | Health-check aliases, orphans, pins, missing compiles. |
| `wiki-compile` | Serial reduce after merge. Only skill allowed to rewrite `wiki/hubs/` and other derived files. |

Depend on [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) for Flavored Markdown, Bases, JSON Canvas, and Defuddle. Do not re-teach CommonMark.

## Ingest invariant

`git status` after ingest must show **added files only**. If you need to change a hub, write a contribution or a pin and leave compile for later.

## Domain wikis

Ontology (projects, papers, labs, `agent_ready`, …) belongs in the consuming vault's `AGENTS.md`. Do not encode a domain here.

The intended first conversion is `~/leonardo/codebase/leonardo-internal` (Karpathy-style shared `index.md` / `log.md` / hub patches). Do not migrate it until this pack is the source of ingest/query/lint/compile behavior.
