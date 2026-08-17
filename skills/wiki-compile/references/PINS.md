# Pins

Pins are create-only notes that record **human intent** about compiled hub prose. Compile re-applies them after regeneration so corrections are not reverted.

Karpathy's "LLM owns the wiki" fails at team scale when a human fix is overwritten by the next ingest-driven rewrite. Store the claim, not a line-based diff.

## Path

`wiki/pins/{hub-id}/{id}.md`

`{id}` is a kebab-case slug plus date if needed (`threshold-per-shipment`, `2026-08-17-rename-lid`).

## Properties

| Property | Required | Type | Notes |
|----------|----------|------|-------|
| `type` | Yes | string | `pin` |
| `hub` | Yes | string | Target hub stem |
| `kind` | Yes | string | `correction`, `addition`, `deletion` |
| `claim` | Yes | string | The intent in one sentence |
| `anchor` | No | string | Section heading, not line numbers (`## Registration thresholds`) |
| `provenance` | Yes | string | `human` |
| `status` | Yes | string | `active` or `superseded` |
| `date` | Yes | date | When the pin was filed |
| `contributor` | Yes | string | Who filed it |

## Kinds

- **correction** — compiled text must keep meaning aligned with `claim` (rewording is fine; contradiction is not)
- **addition** — compiled text must include `claim` even if no remaining source supports it (human-added knowledge). Source retirement must not drop this
- **deletion** — compiled text must not reintroduce the removed claim

## Compile behavior

After writing hub prose from contributions:

1. Load `wiki/pins/{hub}/*.md` with `status: active`
2. For each pin, check the new hub body against `claim` (and `anchor` if the section still exists)
3. Still satisfied → keep pin `active`
4. Contradicted by a newer **source** contribution → do not silently drop; add a `> [!warning]` callout on the hub and mention the pin in the compile log. Leave pin `active` until a human supersedes it
5. `anchor` section gone → list as orphaned pin in the compile log; do not delete the pin file
6. Never edit pin files during compile (create-only). To retire a pin, a human (or an explicit user request) writes a **new** pin or a new note — prefer setting status by adding `wiki/pins/{hub}/{id}-superseded.md` only if the vault allows replacing files; otherwise leave a log entry asking the human to set `status: superseded` in a follow-up that is **not** parallel ingest

This pack's ingest invariant: **ingest never touches pins**. Compile never deletes pins.

## Template

```markdown
---
type: pin
hub: llm-wiki
kind: correction
claim: "Threshold applies per shipment, not per seller account"
anchor: "## Registration thresholds"
provenance: human
status: active
contributor: alice
date: 2026-08-17
---

# Pin: threshold per shipment

Context the compiler should not infer away.
```
