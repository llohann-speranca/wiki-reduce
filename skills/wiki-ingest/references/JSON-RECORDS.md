# JSON log and hot records

Use these shapes when a wiki is federated across git repos (one vault per repo). Markdown log/hot files are forbidden as shared append/overwrite surfaces.

## Log event (create-only)

Path: `wiki/log/{id}.json` where `{id}` is `{YYYY-MM-DD}-{slug}` (kebab-case, unique). Never edit an existing log file. Never append to `log.md`.

```json
{
  "id": "2026-08-17-mail-abc123",
  "ts": "2026-08-17T15:06:00+02:00",
  "repo": "leonardo-eda",
  "op": "ingest",
  "hub": "data-requests",
  "title": "mail abc123",
  "summary": "One-paragraph what happened.",
  "paths": ["wiki/sources/mail-abc123.md"],
  "contributor": "alice"
}
```

| Field | Required | Notes |
|-------|----------|-------|
| `id` | Yes | Equals filename stem |
| `ts` | Yes | ISO-8601 with offset |
| `repo` | Yes | Git folder name (`leonardo-internal` for cortex ops) |
| `op` | Yes | `ingest`, `query`, `lint`, `compile`, `hub`, `day`, `idea`, `sync` |
| `hub` | No | Routing hub id |
| `title` | Yes | Short label |
| `summary` | Yes | Human-readable body |
| `paths` | No | New files this event created |
| `contributor` | No | Git user or `unknown` |

## Hot cache (overwrite in this repo only)

Path: `wiki/hot.json`. Last writer **in this repo** wins. Do not copy this file into other repos. Federated “what is hot” is computed at read time — see [FEDERATE.md](../../wiki-query/references/FEDERATE.md).

```json
{
  "updated": "2026-08-17T17:00:00+02:00",
  "repo": "leonardo-eda",
  "focus_hub": "data-requests",
  "day": "2026-08-17",
  "do_next": "Send Matteo drafted reply",
  "blockers": [],
  "day_page": "~/leonardo/codebase/leonardo-internal/wiki/days/2026-08-17.md"
}
```

| Field | Required | Notes |
|-------|----------|-------|
| `updated` | Yes | ISO-8601; federation sorts on this |
| `repo` | Yes | This git folder name |
| `focus_hub` | No | Routing hub id |
| `day` | No | `YYYY-MM-DD` |
| `do_next` | No | One line |
| `blockers` | No | List of strings |
| `day_page` | No | Absolute or `~/…` path to the overarching day tree page |
