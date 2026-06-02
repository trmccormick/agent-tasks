# Daily Overnight Failure Triage Protocol
**Path**: `docs/new_agent/rules/DAILY_HANDOFF_COMMAND.md`  
**Target Agent**: Qwen-3.5-9B / 27B (Local Triage & Automation Workers via Copilot)  

---

## 📋 Objective
You are the Local Triage Coordinator. Using your active `run_in_terminal` capabilities, you will autonomously look into `./data/logs/`, identify the most recent failure patterns, run verification specs inside Docker to gather live context, and provision verified 0x task files using `TASK_TEMPLATE.md`.

---

## 🚨 Critical Guardrails (Read Before Executing)
1. **Live Execution Mandate**: Do not simulate, guess, or hypothesize. Use `run_in_terminal` to interact directly with the file system and Docker container environment to pull raw truth.
2. **Codebase Verification Ledger**: You must verify if files exist. Output a "Codebase Verification Ledger" at the top of your response showing what target files are FOUND versus MISSING.
3. **No Unapproved Writes**: Focus entirely on structuring the task file layout, isolating the error backtraces, and mapping boundaries. Do not write or modify application code files until a task file is officially approved and transitioned to active status.

---

## 🛠️ Step-by-Step Execution Protocol

### Step 1: Autonomous Log Triage
- Use `run_in_terminal` to list files in `./data/logs/` and identify the most recent test run log.
- Read/cat the log file to isolate the specific failing spec file paths, line numbers, and exact failure backtraces.

### Step 2: Live Container Verification
- For the failing specs found, execute them directly inside the container using our isolated runner to confirm the current state:
```bash
  docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH_IN_CONTAINER] 2>&1 | tail -20'