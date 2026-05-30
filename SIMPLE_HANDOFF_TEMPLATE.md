Implement the task in [TASK FILE].

**CRITICAL: Move Task from Backlog to Active FIRST**
Before writing any code:
1. Move file: `git mv tasks/backlog/[FILENAME] tasks/active/[FILENAME]`
2. Update YAML: `status: backlog` → `status: active`
3. Commit: `git commit -m "Start task: [FILENAME]"`
4. This signals work has begun. Do not skip.

Then follow README.md rules.
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