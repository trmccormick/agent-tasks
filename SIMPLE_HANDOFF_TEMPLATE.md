Implement the task in [TASK FILE].

**CRITICAL: Move Task from Backlog to Active FIRST**
Before writing any code:
1. Note: The new_agent folder (`docs/new_agent`) is a symlink to agent-tasks repo at `/Users/tam0013/Documents/git/agent-tasks`
2. Move file: `git mv tasks/backlog/[FILENAME] tasks/active/[FILENAME]` (from agent-tasks root)
3. Update YAML: `status: backlog` → `status: active`
4. Commit from `/Users/tam0013/Documents/git/agent-tasks`:  
   ```bash
   cd /Users/tam0013/Documents/git/agent-tasks
   git add 'projects/galaxy_game/tasks/active/[FILENAME]'
   git commit -m "Start task: [FILENAME]"
   ```
5. This signals work has begun. Do not skip.

Then read `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md` and follow all repository rules.
Inspect and confirm the target files first.
Do not code if paths or assumptions are unclear.
Keep all commands inside Docker.
Run only targeted specs and do not stream full output.
If a nil-related failure appears, validate factory/model setup before changing logic.
Produce synthesis report and stop. Do not apply changes until explicitly told to proceed.

Targets:
- [FILE 1]
- [FILE 2]
- [SPEC FILE]

Scope:
- [BEHAVIOR 1]
- [BEHAVIOR 2]
- [BEHAVIOR 3]

Return:
- brief plan or code changes,
- targeted test result summary,
- blockers or assumptions.