---
name: subagent-dispatcher
version: 2026-04-16
triggers: ["spawn subagent", "delegate iteration", "run benchmark in subagent", "fresh context"]
tools: []
preconditions: []
constraints: ["parent only ingests final metrics + 1-line summary", "subagents inherit permissions"]
category: meta
---

# Subagent-per-iteration — autoagent

Overnight harness-iteration runs flood the parent with trajectory JSON, Docker
build logs, and stack traces. Without a subagent, you hit context rot by
iteration ~20.

## When to spawn (each of these is an independent subagent)

- **Benchmark run**: `harbor run ...` produces huge logs. Parent doesn't need them.
- **Failure triage**: opening 50 trajectory JSONs and classifying is
  high-volume, low-output. Return format: `{class: count, top_examples: [...]}`
  only.
- **Docker/infra debug**: Dockerfile build failures, image cleanup — irrelevant
  to the meta-agent's knowledge.

## Parent keeps in-context

- The current `results.tsv` tail (last ~10 rows).
- `memory/semantic/LESSONS.md`.
- The next iteration's *plan* (one paragraph).
- Each subagent's *return value* (structured, small).

## Contract

Per `protocols/delegation.md`:
1. **Goal**: one sentence.
2. **Inputs**: paths, not file contents (subagent reads them).
3. **Return format**: JSON-ish, <500 tokens.
4. **Budget**: wall-clock + tool-call caps.

## Anti-pattern

Do not delegate the "what should we try next?" decision — that benefits from
the parent's accumulated context. Delegate the bloaty work, not the thinking.
