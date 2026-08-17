---
type: source
title: AutoSci — research LLM wiki
source_id: autosci-discover
contributor: bob
date: 2026-05-12
raw: raw/bob/autosci-discover/source.md
hubs:
  - llm-wiki
origin: https://github.com/skyllwt/AutoSci
status: active
---

# AutoSci — research LLM wiki

Research/discovery instantiation of Karpathy's pattern: papers, `/discover`, typed wiki, optional experiments.

## Key claims

- `/discover` ranks papers against the existing wiki without ingesting.
- Parallel ingest uses worktrees and `merge=union` on shared accumulators, then serial fan-in.

## Related

- [[llm-wiki]]
- [[karpathy-llm-wiki]]
