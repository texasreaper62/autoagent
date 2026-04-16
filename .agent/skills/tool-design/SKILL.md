---
name: tool-design
version: 2026-04-16
triggers: ["add tool", "design tool", "new function_tool", "agent.as_tool"]
tools: []
preconditions: []
constraints: ["return structured data, not raw stdout, wherever possible"]
category: engineering
---

# Tool design

Specialized tools beat a single `run_shell` because:
1. Name-based priors: the model pattern-matches names before reading descriptions.
2. Structured returns cut error-recovery turns.
3. Clear error messages let the model self-correct without retries.

## Naming
- Verb-first, concrete: `read_cell`, `list_sheets`, `write_cell`.
- Avoid generic names: `run`, `do`, `execute`.

## Return shape
- Prefer JSON-serializable dicts over strings.
- On error, return `{"error": "<short reason>", "hint": "<what to do next>"}`.

## Argument shape
- Required args first, optional with defaults.
- Type-annotate everything; the SDK uses annotations to generate schemas.

## agent.as_tool()
Wrap an agent as a callable tool. Common use: a verification sub-agent that
re-reads output and returns `{"valid": bool, "issues": [...]}`. The main
agent calls it before finishing.

## Examples

Spreadsheet workflow:
- `list_sheets()` → `[{name, rows, cols}]`
- `read_range(sheet, a1)` → `[[...]]`
- `write_cell(sheet, a1, value)` → `{"ok": true}` or error

Filesystem workflow:
- `list_dir(path)` → `[{name, type, size}]` — better than `ls -la` stdout.
- `read_file(path, start, end)` → structured range, avoids full-file reads.

## Budget
Adding tools costs tokens in every tool call (schema in system prompt).
A new tool must pay for itself by eliminating at least 2 turns of
boilerplate shell across the task set.
