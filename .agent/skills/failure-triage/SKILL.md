---
name: failure-triage
version: 2026-04-16
triggers: ["diagnose failures", "why did tasks fail", "analyze trajectory", "group failures"]
tools: [bash]
preconditions: ["jobs/latest exists with trajectory files"]
constraints: ["group by root cause, not by task id", "prefer changes that fix a class of failures"]
category: engineering
---

# Failure triage

After every benchmark run, diagnose before you edit.

## Process

1. Read the latest `run.log` and `results.tsv` row.
2. For each failed or zero-score task, open its trajectory JSON under
   `jobs/latest/<task>/trajectory.json`.
3. Classify the root cause:
   - **Misunderstood the task** — prompt/instruction issue.
   - **Missing capability / tool** — agent needed something it didn't have.
   - **Weak information gathering** — didn't explore before acting.
   - **Bad execution strategy** — wrong approach given the info available.
   - **Missing verification** — agent thought it succeeded; output was wrong.
   - **Environment / dependency** — infra issue, not agent issue.
4. Group tasks by root cause. Pick the class with the most failures.
5. Propose ONE general harness change that addresses the class.

## Anti-pattern: overfitting

Before committing the change, apply the overfitting test (see
`skills/overfitting-check/SKILL.md`). If it fails, discard.

## Promotion

When the same root-cause class appears across ≥3 runs, promote it to
`memory/semantic/LESSONS.md` via the memory-manager skill so future
iterations recognize the pattern without re-deriving it.
