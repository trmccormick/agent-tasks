Title: Copilot — Ollama agent template & instructions

Purpose
- Provide a Copilot custom-agent template that targets a local Ollama model and enables editor/terminal tools so agents can execute commands (rather than only returning JSON/YAML text).

Prerequisites
- Ollama running and reachable from this machine (HTTP API).
- GitHub Copilot Chat that supports custom agents (latest extension).
- Path to VS Code user prompts folder: /Users/tam0013/Library/Application Support/Code/User/prompts

Quick steps
1. Copy the agent JSON below to the VS Code prompts folder as `ollama-executor.json` (or use the Copilot Chat "Manage Agents" UI to create an agent with the same fields).
2. In Copilot Chat → Manage Agents, open the agent and ensure terminal/file tools are enabled for the agent.
3. Restart VS Code (or reload the Copilot extension) and select the new agent in Copilot Chat.
4. Test by asking the agent to run a simple terminal command and confirm any tool-run prompt.

Sample agent JSON (example)
{
  "name": "ollama-executor",
  "displayName": "Ollama Executor (tool-enabled)",
  "model": "ollama://localhost/qwen2.5-14b",
  "description": "Agent that can run terminal and file tools. Use with care; prompt for user-confirmation before destructive commands.",
  "temperature": 0.0,
  "instructions": "You are an executor agent backed by a local Ollama model. You MAY call editor/terminal/file tools when needed. Always ask for explicit user confirmation before running destructive commands (e.g., rm, docker, git push). Prefer idempotent checks first (ls, cat, rspec --dry-run). Return actions performed and results as a short report.",
  "tools": [
    {"name": "run_in_terminal", "enabled": true, "confirmBeforeRun": true},
    {"name": "read_file", "enabled": true},
    {"name": "write_file", "enabled": true},
    {"name": "list_dir", "enabled": true},
    {"name": "get_workspace_state", "enabled": true}
  ],
  "safety": {
    "forbidCommands": ["rm -rf /", "sudo reboot"],
    "requireConfirmationFor": ["docker", "git push", "rm"]
  }
}

Notes and testing
- The exact agent schema in Copilot may differ; if the Copilot UI only accepts a form, copy the fields into the UI instead of a raw file.
- If Copilot still returns text-only: open Manage Agents and verify the agent's model is set to the Ollama endpoint (or the provider string Copilot expects) and that the "Allow terminal" / "Allow file access" toggles are on for that agent.

Minimal interactive test (after agent is installed)
1. Open Copilot Chat and choose `Ollama Executor (tool-enabled)`.
2. Prompt: "Run `echo tool-check && pwd` in the workspace terminal and return the output. Ask for confirmation first."
3. If allowed, Copilot should prompt you to confirm the terminal run; confirm and then inspect output in the Copilot response and local terminal.

Log troubleshooting
- If tool runs fail, capture the Copilot debug log folder `.../workspaceStorage/.../GitHub.copilot-chat/debug-logs/<session>/main.jsonl` and grep for entries with `"status":"error"` around the timestamp of your test. Paste any error entries and I will triage them.

If you'd like, I can:
- Produce a ready-to-import agent JSON adapted to your exact Ollama model name.
- Generate a short checklist for configuring Copilot UI toggles and firewall/localhost access for Ollama.
- Inspect a specific Copilot debug log entry if you paste the `main.jsonl` error lines or the session folder name.
