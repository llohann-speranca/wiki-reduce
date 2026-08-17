---
type: source
title: Karpathy LLM Wiki
source_id: karpathy-llm-wiki
contributor: alice
date: 2026-04-04
raw: raw/alice/karpathy-llm-wiki/source.md
hubs:
  - llm-wiki
origin: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
status: active
---

# Karpathy LLM Wiki

Idea file for a persistent markdown wiki compiled by an LLM. Raw sources stay immutable; the wiki is the compounding layer.

## Key claims

- Wiki sits between you and raw sources; knowledge is compiled once, not re-derived at query time.
- Three operations: ingest, query, lint.
- Shared `index.md` and `log.md` are the navigation files — and the conflict surface for parallel users.

## Related

- [[llm-wiki]]
