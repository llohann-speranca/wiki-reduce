# AutoSci README (clip)

Immutable clip. Agents read this; they never edit it.

Source: https://github.com/skyllwt/AutoSci

Karpathy's LLM-Wiki vision applied to the scientific research lifecycle: ingest papers, `/discover` ranked reading lists against the wiki, experiments, manuscripts.

Parallel ingest uses git worktrees and merge=union on shared index/log/edge files, then a serial fan-in.
