---
name: experiment-loop
version: 2026-04-16
triggers: ["run experiment", "kick off experiment", "autoagent loop", "benchmark run"]
tools: [bash, git]
preconditions: ["Dockerfile.base built", "tasks/ has benchmark tasks", "results.tsv header exists"]
constraints: ["never modify the FIXED ADAPTER BOUNDARY section", "never change MODEL without human approval"]
category: engineering
---

# Experiment loop

The meta-agent loop. Runs until the human interrupts.

## Each iteration (spawn a fresh-context subagent per iteration; see skills/subagent-dispatcher)

1. Check branch/commit; read latest `run.log` and last `results.tsv` row.
2. Invoke `skills/failure-triage` on the latest run. Pick ONE general improvement.
3. Invoke `skills/harness-modify` to edit `agent.py`.
4. Apply `skills/overfitting-check`. Discard if it fails.
5. Commit: `harness: <short description>`.
6. Rebuild + rerun the benchmark:
   ```
   docker build -f Dockerfile.base -t autoagent-base .
   rm -rf jobs; mkdir -p jobs && uv run harbor run -p tasks/ -n 100 \
     --agent-import-path agent:AutoAgent -o jobs --job-name latest > run.log 2>&1
   ```
7. Append a row to `results.tsv` (see skills/results-logging).
8. Keep/discard per `skills/simplicity-judge` (passed as primary, simpler as tie-break).
9. Repeat.

## Baseline rule
The first run on any branch is ALWAYS the unmodified baseline. No exceptions.

## Never-stop rule
Once the loop begins, do not pause to ask "should I continue?" The human
is gone. Run until explicitly interrupted.

## Self-rewrite hook
After 10 iterations, invoke memory-manager to promote recurring failure
classes from episodic memory into `memory/semantic/LESSONS.md`.
