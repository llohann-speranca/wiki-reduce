# Karpathy LLM Wiki (clip)

Immutable clip of the idea file. Agents read this; they never edit it.

Source: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

The LLM incrementally builds and maintains a persistent wiki — a structured, interlinked collection of markdown files that sits between you and the raw sources. Knowledge is compiled once and then kept current, not re-derived on every query.

Operations: ingest, query, lint. Catalog: index.md. Timeline: log.md.
