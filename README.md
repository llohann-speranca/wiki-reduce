# wiki-reduce

Agent skills for a **collaborative LLM wiki**: ingest from many users without git conflicts, query via Obsidian Bases and backlinks, lint, then serially compile hub prose.

These skills follow the [Agent Skills specification](https://agentskills.io/specification) so they can be used by any skills-compatible agent, including Claude Code, Codex, OpenCode, and Cursor.

The pattern extends [Karpathy's LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). File conventions follow [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) (Obsidian Flavored Markdown, properties, Bases, JSON Canvas). Read [SCHEMA.md](SCHEMA.md) for the idea; [AGENTS.md](AGENTS.md) for session rules.

## Why not the gist as-is

Karpathy ingest rewrites shared pages (`index.md`, `log.md`, entity hubs). Parallel commits from independent users conflict. This pack makes ingest **create-only** (unique paths). Bases replace the catalog. Compile is the only writer of shared hub files.

The research/discovery cousin of the gist is [AutoSci](https://github.com/skyllwt/AutoSci) ([arXiv:2605.31468](https://arxiv.org/abs/2605.31468)): literature ingest, `/discover`, experiments, manuscripts. Use AutoSci for that lifecycle. Use this pack for a multi-user Obsidian vault.

Install [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills) alongside this pack (`obsidian-markdown`, `obsidian-bases`, `json-canvas`, `defuddle`).

## Installation

### npx skills

```
npx skills add git@github.com:kepano/obsidian-skills.git
npx skills add git@github.com:llohann-speranca/wiki-reduce.git
```

### Manually

#### Cursor

This repo already exposes skills under `.cursor/skills/` (symlinks into `skills/`). Open the repo as the workspace, or symlink:

```sh
ln -s /path/to/wiki-reduce/skills/wiki-ingest ~/.cursor/skills/wiki-ingest
ln -s /path/to/wiki-reduce/skills/wiki-query ~/.cursor/skills/wiki-query
ln -s /path/to/wiki-reduce/skills/wiki-lint ~/.cursor/skills/wiki-lint
ln -s /path/to/wiki-reduce/skills/wiki-compile ~/.cursor/skills/wiki-compile
```

#### Claude Code

Copy or symlink `skills/` into `.claude/skills` in the vault (or `~/.claude/skills`).

#### Codex

Copy `skills/` into `~/.codex/skills`.

#### OpenCode

Clone this repo under `~/.opencode/skills/` so paths look like `~/.opencode/skills/wiki-reduce/skills/<name>/SKILL.md`.

## Skills

| Skill | Description |
|-------|-------------|
| [wiki-ingest](skills/wiki-ingest) | Create-only ingest: source note, per-hub contributions, log file, alias stubs |
| [wiki-query](skills/wiki-query) | Answer from Bases and backlinks; federated glob of sibling `wiki/hot.json` + `wiki/log/*.json` |
| [wiki-lint](skills/wiki-lint) | Alias collisions, compile gaps, orphans, pin health |
| [wiki-compile](skills/wiki-compile) | Serial reduce into `wiki/hubs/` plus optional canvases |

## Example vault

[vault/](vault/) is a minimal Obsidian vault: two contributors ingested the same topic without touching the same files, then one compile built `wiki/hubs/llm-wiki.md`.

Open `vault/` as an Obsidian vault (or as the folder that contains `wiki/` and `raw/`).

## Layout

```
SCHEMA.md                 Idea file (Karpathy pattern + conflict rules)
AGENTS.md                 Session pointer
skills/                   Agent skills
vault/raw/                Immutable sources, per contributor
vault/wiki/sources/       One note per source
vault/wiki/contributions/ Per (hub, source) notes
vault/wiki/log/           One file per operation
vault/wiki/aliases/       Slug reservations
vault/wiki/pins/          Human intent that compile must keep
vault/wiki/hubs/          Compiled prose only
vault/wiki/*.base         Catalogs (not a shared index.md)
```
