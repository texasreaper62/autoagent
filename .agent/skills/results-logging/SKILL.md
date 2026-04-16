---
name: results-logging
version: 2026-04-16
triggers: ["log result", "record experiment", "update results.tsv"]
tools: [bash]
preconditions: ["results.tsv exists with header"]
constraints: ["tab-separated, never comma", "never git-add results.tsv", "7 columns in order"]
category: engineering
---

# Results logging — autoagent

## Schema
```
commit	avg_score	passed	task_scores	cost_usd	status	description
```

- `commit`: short (7-char) git hash.
- `avg_score`: aggregate benchmark score.
- `passed`: `n/total`, e.g. `20/58`.
- `task_scores`: per-task scores (comma-free; use `;` if you must list).
- `cost_usd`: total cost, or `N/A`.
- `status`: `keep`, `discard`, or `crash`.
- `description`: short. No commas. No tabs.

## Rules
- `results.tsv` is a run **ledger**, not a unique-commit ledger. Same commit
  can appear multiple times (rerun for variance).
- Untracked by git. Append-only. Never rewrite past rows.

## Primary metric
`passed` wins over `avg_score` wins over `cost_usd`. Simpler harness breaks ties
even when `passed` is equal — see `skills/simplicity-judge`.
