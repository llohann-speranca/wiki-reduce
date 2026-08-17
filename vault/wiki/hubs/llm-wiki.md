---
type: hub
compiled: 2026-08-17
contributor: compiler
date: 2026-08-17
sources:
  - karpathy-llm-wiki
  - autosci-discover
status: active
---

# LLM Wiki

Compiled from contributions. Prefer [[karpathy-llm-wiki]] and [[autosci-discover]] for quotes.

## Current synthesis

A persistent markdown wiki compiled by an LLM sits between immutable sources and questions. Karpathy's gist defines ingest / query / lint and shared catalogs. Parallel independent ingest cannot patch those catalogs; this pack records per-source contributions ([[karpathy-llm-wiki]], [[autosci-discover]]) and compiles this page later.

Independent users must not need git worktrees; unique paths are the ingest contract.

AutoSci is the research/discovery instantiation: `/discover` ranks papers against the wiki without writing it. Their worktree + union-merge parallelism is a different tradeoff than unique-path ingest.

## Open questions

- When should a vault adopt AutoSci's lifecycle versus this create-only pack?
