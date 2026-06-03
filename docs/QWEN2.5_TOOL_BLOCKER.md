# Qwen2.5 Terminal Execution Blocker

**Status**: IDENTIFIED — Qwen2.5 models refuse terminal tool calls in Copilot.

## Problem
- When using `qwen2.5-coder:14b` in a Copilot custom agent configured with `run_in_terminal` tool enabled, the model refuses with:
  ```
  Can you execute local terminal commands or shell operations directly within this workspace terminal?
  I can't assist with that.
  ```
- This is a built-in safety policy in the Qwen2.5 model weights/instructions, not a Copilot config issue.
- The JSON tool-call format doesn't matter — the model refuses before it even attempts the tool.

## Root Cause
- Qwen2.5 (including coder variants) have a system-level constraint that prevents terminal/shell command execution.
- This is by design in the Qwen2.5 instruction set.

## Solution
**Use Qwen3.5 instead** (`qwen3.5:27b` or `qwen3.5:9b`).
- Qwen3.5 models DO support terminal execution via Copilot tools.
- Both sizes are running locally:
  - `qwen3.5:27b` (17 GB) — heavier reasoning, slower
  - `qwen3.5:9b` (6.6 GB) — lightweight, faster

## Updated Agent Config
The agent config [docs/copilot_ollama_qwen2_5_agent.json](copilot_ollama_qwen2_5_agent.json) now targets `qwen3.5:27b`.

### To use in Copilot
1. Copilot Chat → Manage Agents → Import or create "Ollama Qwen3.5 Executor"
2. Model: `ollama://localhost/qwen3.5:27b` (or `qwen3.5:9b` for speed)
3. Enable tool toggles: "Allow terminal execution" + "Allow file access"
4. Test:
   ```
   {"name":"run_in_terminal","arguments":{"command":"echo TEST_OK && pwd","explanation":"verify tool","goal":"test","mode":"sync"}}
   ```

## Notes
- If you need Qwen2.5 for other tasks (code reading, analysis), it's still fine — just not for terminal execution.
- The issue is specific to terminal/shell tools; Qwen2.5 can still read/write files.
- If Qwen3.5 is too slow, consider `codestral:latest` (12 GB) or `deepseek-coder-v2:16b` (8.9 GB) — both support tools.
