You are Qwen3.5-27B acting as the Implementation Agent.

Task:
Implement the approved change described in the active task file:
[PASTE TASK FILE PATH HERE]

**CRITICAL FIRST STEP: Move Task File Before Starting Work**
1. The task file is currently in `tasks/backlog/` 
2. **Before writing any code**, move the file to `tasks/active/`:   
3. Note: The new_agent folder (`docs/new_agent`) is a symlink to agent-tasks repo at `/Users/tam0013/Documents/git/agent-tasks`
4. Commit from agent-tasks root (not symlink path) to preserve correct Git metadata:
   - Git mv: `git mv tasks/backlog/[FILENAME] tasks/active/[FILENAME]`
   - Update YAML header: change `status: backlog` to `status: active`
   - Commit: `git add tasks/active/[FILENAME] && git commit -m "Start task: [FILENAME]"`
3. This signals to the strategist that work has begun.
4. **Do not skip this step.**

Before changing code:
1. Read `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/README.md` and follow all repository rules.
2. Confirm the task is still valid against the current codebase.
3. Inspect the target files listed below.
4. If a target path is missing or renamed, stop and report the current path before editing anything.

Target files:
- [PASTE PRIMARY FILE PATH HERE]
- [PASTE SECONDARY FILE PATH HERE]
- [PASTE RELATED SPEC FILE PATH HERE]

Requirements:
- Preserve existing behavior unless the task explicitly changes it.
- Make only the changes needed for this task.
- Do not create documentation.
- Do not edit unrelated files.
- Keep all command execution inside Docker.
- For RSpec, do not stream full output; run the targeted spec and report only the final summary line plus any relevant failure snippets.

Implementation scope:
- [PASTE BULLETS OF REQUIRED BEHAVIOR HERE]

Testing:
- Update or add focused specs for the changed behavior.
- Run only the targeted spec file(s) needed to verify the change.
- If a nil-related failure appears after model creation, stop and validate the factory/model setup before changing service logic.

Output required:
1. Brief action plan if implementation is not yet started.
2. Code changes once approved.
3. Targeted test result summary.
4. Any assumptions or blockers.
5. Stop immediately if any requirement is unclear.