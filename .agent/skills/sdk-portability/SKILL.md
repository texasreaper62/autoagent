---
name: sdk-portability
version: 2026-04-16
triggers: ["switch SDK", "OpenAI vs Claude SDK", "agent-claude.py", "dual SDK", "portability"]
tools: []
preconditions: []
constraints: ["both files must produce the same ATIF trajectory schema"]
category: meta
---

# SDK portability

The repo ships two harnesses:
- `agent.py` — OpenAI Agents SDK (`agents` package, `Agent`, `Runner.run`).
- `agent-claude.py` — Claude Agent SDK (`claude_agent_sdk`, `ClaudeSDKClient`).

Both mount the same `.agent/` brain. The brain is SDK-agnostic.

## What lives in the brain (portable)
- System prompt ideas (as skills, not inlined in `SYSTEM_PROMPT` constants).
- Tool *design* principles (see `skills/tool-design`).
- Failure classes (`memory/semantic/LESSONS.md`).
- The meta-agent loop itself (`skills/experiment-loop`).

## What's SDK-specific (stays in agent*.py)
- Tool registration: `@function_tool` (OpenAI) vs `@tool` (Claude).
- Agent construction: `Agent(...)` vs `ClaudeAgentOptions(...)`.
- Handoff mechanics: `agent.as_tool()` (OpenAI) vs `SUBAGENTS` dict (Claude).
- Trajectory serialization (already below the FIXED ADAPTER BOUNDARY).

## Rule

When you find a harness improvement that expresses an SDK-specific pattern
(e.g. "use `agent.as_tool()` for verification"), generalize it in the skill
("add a verification sub-agent") and let each harness implement the pattern
idiomatically. Never let an SDK-specific construct leak into `memory/semantic/`.
