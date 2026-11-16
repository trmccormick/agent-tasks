# Handoff — Profiles V2 Canonical Implementation
# Paste this entire block to Qwen

You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-06-HIGH-PROFILES-V2-CANONICAL-IMPLEMENTATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-06-HIGH-PROFILES-V2-CANONICAL-IMPLEMENTATION.md \
         projects/galaxy_game/tasks/active/2026-07-06-HIGH-PROFILES-V2-CANONICAL-IMPLEMENTATION.md
  Then open the moved file and change: status: backlog → status: active
  Commit the move: git add -A && git commit -m "chore: move Profiles V2 task to active"
  Paste the output of all commands in chat before proceeding.

LIFECYCLE: backlog → active → completed (git mv only, never cp or plain mv)
Verify with: find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
  -name "2026-07-06-HIGH-PROFILES-V2-CANONICAL-IMPLEMENTATION.md"
Only ONE result should exist. Paste this output before starting synthesis.

READ FIRST: Task file contains all prerequisites, gotchas, and stop conditions.
CRITICAL: Save synthesis report to summaries/ BEFORE starting any code changes.

IMPORTANT GOTCHAS (read before touching any file):
1. MISSIONS_PATH points to data/json-data/missions/ — missions_v2/ is a SIBLING folder,
   not a child. Do NOT use MISSIONS_PATH.join("missions_v2/...") — this produces a wrong path.
2. There are THREE separate path resolution points in luna_mission.rake that all need updating:
   - Profile resolution (lines 9-11)
   - phase_path_for lambda (lines 195-204)
   - task_ref resolution inside phase loop (line 434)
   Missing any one means v2 paths silently fall through to legacy.
3. BEFORE writing task_ref resolution logic, inspect the actual task_ref values in a v2
   phase file to determine what base path they are relative to. Paste findings in chat first.
4. Check whether TaskExecutionEngineV2.new uses the profile path internally before changing
   the engine init call on line 51.

NO GIT COMMITS without explicit human approval in chat. Show diffs and wait for "yes, commit".
