# Page types

Structural types for this pack. Domain types (project, paper, lab) belong in the consuming vault.

Every note uses YAML properties. Required fields are marked.

## Shared properties

| Property | Required | Type | Notes |
|----------|----------|------|-------|
| `type` | Yes | string | One of the types below |
| `date` | Yes | date | `YYYY-MM-DD` (ingest or event date) |
| `contributor` | Yes | string | Git user, vault handle, or `unknown` |
| `source_id` | No | string | Required on source, contribution, log |
| `hubs` | No | list | Wikilink targets (filename stems) |
| `status` | No | string | `active` (default), `superseded`, `draft` |
| `aliases` | No | list | Obsidian note aliases, not slug-reservation files |

## `source`

Path: `wiki/sources/{source-id}.md`

One page per ingested raw document. Never modify after create; ingest a follow-up source instead.

| Property | Required | Type | Notes |
|----------|----------|------|-------|
| `type` | Yes | string | `source` |
| `source_id` | Yes | string | Equals filename stem |
| `raw` | Yes | string | Path relative to vault root, under `raw/` |
| `title` | Yes | string | Human title |
| `origin` | No | string | URL or external id |

Body: summary, key claims, quotes, `[[hub]]` links. Do not duplicate the raw file.

## `contribution`

Path: `wiki/contributions/{hub-id}/{source-id}.md`

What this source adds to **one** hub. One file per (hub, source) pair.

| Property | Required | Type | Notes |
|----------|----------|------|-------|
| `type` | Yes | string | `contribution` |
| `hub` | Yes | string | Filename stem of the target hub |
| `source_id` | Yes | string | Matching source page |
| `kind` | No | string | `evidence`, `claim`, `next-action`, `contradiction`, `context` |

Body: bullets the compile skill can fold into hub prose. Wikilink `[[hub]]` and `[[source-id]]`.

If two sources speak to the same hub, they are two files — never a patched hub.

## `log`

Path: `wiki/log/{YYYY-MM-DD}-{source-id}.md`

One file per ingest (or lint run: `{YYYY-MM-DD}-lint-{slug}.md`).

| Property | Required | Type | Notes |
|----------|----------|------|-------|
| `type` | Yes | string | `log` |
| `op` | Yes | string | `ingest`, `query`, `lint`, `compile` |
| `source_id` | No | string | Required when `op` is `ingest` |

Body: one heading, short what-changed list (new paths only for ingest).

## `alias`

Path: `wiki/aliases/{alias}.md`

Slug reservation. Filename stem is the alias people might type.

| Property | Required | Type | Notes |
|----------|----------|------|-------|
| `type` | Yes | string | `alias` |
| `canonical` | Yes | string | Hub (or source) stem to use |

Body may be empty. Identical files merge cleanly. Different `canonical` on the same path is an intentional git conflict.

## `pin`

Path: `wiki/pins/{hub-id}/{id}.md`

Human intent that compile must respect. See [PINS.md](../../wiki-compile/references/PINS.md).

| Property | Required | Type | Notes |
|----------|----------|------|-------|
| `type` | Yes | string | `pin` |
| `hub` | Yes | string | Target hub stem |
| `kind` | Yes | string | `correction`, `addition`, `deletion` |
| `provenance` | Yes | string | `human` (default) |

## `hub`

Path: `wiki/hubs/{hub-id}.md`

Compiled only. Ingest must not create or edit these files.

| Property | Required | Type | Notes |
|----------|----------|------|-------|
| `type` | Yes | string | `hub` |
| `compiled` | Yes | date | Last compile day |
| `sources` | No | list | `source_id` values folded in |

Unresolved `[[hub-id]]` links are allowed before the first compile.

## `source-id` uniqueness

1. Prefer a kebab-case slug from the title (`karpathy-llm-wiki`).
2. If `wiki/sources/{slug}.md` already exists, do not overwrite. Append an 8-char lowercase hex suffix: `{slug}-{aabbccdd}` from a hash of the raw path or URL.
3. Contributor does not belong in `source-id` unless two people ingested the same URL independently and both should remain. Then suffix with contributor: `{slug}-{contributor}`.
