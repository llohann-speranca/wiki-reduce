# Federated query

When knowledge is split across git repos (a cortex vault plus per-repo wikis), **read in place**. Do not copy `hot.json` or log files between repos.

## Codebase root

Default: `~/leonardo/codebase` (or the parent of the current `leonardo-*` git root). Skip folders without `wiki/`.

## Read order

1. **Overarching day tree** (if this federation has one): `{cortex}/wiki/days/YYYY-MM-DD.md` for today. Cortex is typically `leonardo-internal`.
2. **Local** `wiki/hot.json` if the current git root has one.
3. **Federated hots:** glob `{codebase}/leonardo-*/wiki/hot.json` (and cortex). Parse JSON. Sort by `updated` descending. Report the newest few; do not merge onto disk.
4. **Routing hub** in the cortex (`wiki/projects/{id}.md` or `wiki/hubs/{id}.md`): `repos`, `agent_ready`, skill pointers.
5. **Owning repo wiki:** `{codebase}/{repo}/wiki/` markdown (next-actions, decisions) plus `{codebase}/{repo}/wiki/log/*.json` (newest first).
6. **Union search:** `rg` over those wiki trees. Do not RAG Monday/email first.

## Writing

Write only in the repo where the work happened:

- New `wiki/log/{id}.json` (create-only) — [JSON-RECORDS.md](../../wiki-ingest/references/JSON-RECORDS.md)
- Update that repo’s `wiki/hot.json` (overwrite allowed)
- New markdown notes under that repo’s `wiki/` (create-only ingest invariant)

Never append `{cortex}/wiki/log.md`. Never patch another repo’s hub next-action from here.

## Validation

1. You did not copy hot/log files across repos
2. New log JSON `id` matches the filename stem and does not overwrite
3. Citations include repo + path (`leonardo-eda:wiki/log/….json`)
