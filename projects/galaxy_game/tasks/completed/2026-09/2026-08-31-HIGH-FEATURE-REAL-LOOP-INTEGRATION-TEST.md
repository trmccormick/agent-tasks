---
status: active
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate — **EXCEPT** `[project]`/`[SUBFOLDER]` path segments; fill in before dispatch
- [x] All Step 0-N instructions are clear and actionable
- [x] Synthesis report template provided
- [ ] All file paths are verified to exist — **NOT DONE**, Claude has no filesystem access; Step 1 is exactly that verification, building on the file:line evidence already found in the live-game-loop research
- [x] Architecture Gotchas are specific
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-FEATURE-REAL-LOOP-INTEGRATION-TEST.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  Confirm whether this file is tracked in git first (`git ls-files <path>`) —
  if tracked, use `git mv`; if untracked, use `mv` then `git add` the final
  path. Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis
  until this is done.

LIFECYCLE: backlog → active → completed
  Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-31-HIGH-FEATURE-REAL-LOOP-INTEGRATION-TEST.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat.
```

**IMPORTANT: Do not modify or abbreviate the text above.**

---

# TASK: Real Game-Loop Integration Test (toggle-on, run, dispatch, observe)
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-08-31

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*
*Local models run via Copilot have terminal/tool-use access — they can grep the codebase,
check status.md, and run read-only research commands to verify state before triaging.
Continue is installed but is not part of the active workflow — a Continue session is
read-only (task files only, no commands, no DB access) and should not be assumed to have
the same capability.*

- **Template Conformance**: [FILL IN — Qwen to verify against current TASK_TEMPLATE.md]
- **Docker Wrapper Check**: PASS | FAIL | N/A — [verify RSpec strings use correct docker exec format without cd /home/galaxy_game]
- **MVP Alignment**: VALID | STALE | OBSOLETE — [does this task still apply to current codebase]
- **MVP Impact Note**: [one line on how this connects to AI Manager Luna settlement or spec health]
- **Action Line**: READY FOR LOCAL DISPATCH | NEEDS MANUAL REVIEW | OBSOLETE — ARCHIVE

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot
**Why This Agent**: [one line — if cloud agent, state why local failed]
**Local attempts before cloud**: [N/A | 1 | 2 — cloud only dispatched after 2 local failures]
**Supervision Level**: watched carefully — first real exercise of the live loop found in the reality-check research

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.
> If assigning to cloud, document which local attempts failed and why.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/path/to/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/path/to/agent-tasks/projects/[project]/README.md`
3. `2026-08-30-FINDINGS-LIVE-GAME-LOOP-REALITY-CHECK.md` and `2026-08-31-FOLLOWUP-LIVE-GAME-LOOP-DEEP-DIVE.md` (summaries folder) — this task builds directly on those findings, don't re-derive them
4. This Task File

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
Two research passes this week (2026-08-30, 2026-08-31) established, with file:line evidence:
- A real live game loop exists: `GameSimulationJob` (Sidekiq, self-schedules every 1 min) calls `Game#advance_by_days`, which has genuine side effects processing settlements/units/planets (`game.rb:42-57`).
- The loop is OFF by default (`GameState#running` defaults false) and only activates via a manual UI toggle (`game_controller.rb:53-56`) — nothing has ever auto-enabled it, which plausibly explains why no prior testing session has observed it running.
- Craft (Luna precursor mission craft, GCC mining satellite, Venus skimmer) are NOT wired into this loop at all — they inherit `ApplicationRecord`, not `Units::BaseUnit`, and are driven entirely by separate bespoke rake tasks / AIManager services.

Tracy's direction: rather than waiting on the (larger, still-unscoped) craft-transit architecture fix, build an **integration test** that (1) actually turns the real loop on, (2) lets it run for real via its real mechanism (not a hand-rolled day-loop reimplementation), (3) *in parallel*, dispatches craft/mission actions through whatever already works today (existing rake/service entry points — e.g. the precursor mission launch, GCC sat deployment), and (4) produces observable, human-readable, datestamped output of what happens — the same "watch it happen" goal from the original event-log idea, but as an automated test script rather than a UI feature.

This does NOT require the craft-transit state-machine fix to be done first. It's explicitly testing the CURRENT state of both systems side-by-side (the real loop ticking settlements/units/planets, and separately-dispatched craft actions), which is valuable precisely because it will make the current gap between them directly observable in one run, rather than inferred from separate isolated scripts.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: [name from filename]
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: the goal, the verification method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `path/to/file` | [description] | [not started / pending / done] |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ [wrong approach] — instead ✅ [right approach]
- ❌ [wrong approach] — instead ✅ [right approach]

---

**SYNTHESIS COMPLETE.** Ready to proceed with [PRIORITY 1 / PRIORITY 2 / etc].
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement
Build an integration test (RSpec feature/system spec, or a dedicated rake task if that fits the existing test conventions better — confirm which) that:
1. Sets `GameState#running` to true via the real toggle mechanism (not a raw DB write, unless the toggle method itself is confirmed to only flip that one field).
2. Drives the real game loop forward for a meaningful simulated period — using Sidekiq's testing utilities (e.g. `Sidekiq::Testing.inline!` or manually invoking `GameSimulationJob.new.perform` N times) rather than waiting on real wall-clock minutes.
3. Concurrently (or interleaved) dispatches at least one real craft/mission action through an existing working entry point (e.g. whatever `luna_mission:execute`, `orbital_mining:gcc_sat`, or the AIManager services underneath them actually call at the service level — NOT by re-invoking the rake task's CLI, but by calling the underlying service objects directly from within the test).
4. Logs every observable event (settlement/unit state changes from the real loop, plus the dispatched craft's state changes) to a human-readable, datestamped output — reusing the format already discussed: e.g. `[Game Day N] <event description>`.
5. Produces a test result that's inspectable afterward (either an actual log file the test writes, or captured RSpec output) — not just a pass/fail assertion with no visibility into what happened.

**Current behavior**: The live game loop exists but is off by default and craft are NOT wired into it.
**Expected behavior**: An integration test that toggles the real loop on, runs it, dispatches craft actions alongside it, and produces observable datestamped output of what happens.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Do not use `advance_by_days` as a hand-rolled day-loop the way `gcc_mining_sat.rake` does (calling it once per simulated day in a manual loop). Drive it through the real job (`GameSimulationJob`), even if accelerated for test speed — the whole point is testing the real mechanism, not another bespoke simulation.
- ❌ Wrong: Calling `advance_by_days` N times in a manual loop like `gcc_mining_sat.rake` does
- ✅ Right: Invoking `GameSimulationJob.new.perform` (or using `Sidekiq::Testing.inline!`) for each simulated tick
- Why: The whole point is testing the real mechanism, not another bespoke simulation

⚠️ **GOTCHA 2**: Craft dispatch in this test is expected to NOT be picked up or advanced by the live loop — that's the confirmed current state, not a bug to fix here. The test should dispatch craft actions through their existing working path and log what happens to them via whatever mechanism already reports their state (their own service objects/rake output), not expect the live loop to move them. If the test's assertions implicitly assume the loop moves craft, that's testing something not yet true — flag this rather than "fixing" it by quietly changing the test's expectations to hide the gap.
- ❌ Wrong: Asserting that craft state changes after the loop ticks
- ✅ Right: Dispatch craft via existing service path, log their state independently, flag the gap
- Why: The gap between loop and craft is exactly what this test makes observable

⚠️ **GOTCHA 3**: Confirm `toggle_running!`'s exact side effects before relying on it in a test (per `game_state.rb:22-26`) — make sure it only flips the `running` boolean and doesn't have other side effects (e.g. broadcasting, triggering other jobs) that would need handling/stubbing in a test context.
- ❌ Wrong: Assuming `toggle_running!` is a no-op aside from flipping `running`
- ✅ Right: Read `game_state.rb:22-26` and verify side effects before relying on it
- Why: Unexpected side effects could break test isolation

⚠️ **GOTCHA 4**: This test will likely run against a shared test DB/game state pattern — make sure toggling `running` true and running the job doesn't leak into other tests or leave `running` true afterward. Reset state in an `after` block.
- ❌ Wrong: Leaving `GameState#running = true` after the test
- ✅ Right: Reset to false in an `after` block
- Why: State leakage causes flaky downstream tests

⚠️ **GOTCHA 5**: Per standing guardrail — don't claim this test "proves the loop works" from a green pass alone. The log output itself needs to be reviewed (by a human, or pasted back for review) to confirm real events actually happened, not just that no exception was raised.
- ❌ Wrong: Asserting `expect(output).to include("loop works")` as the success criterion
- ✅ Right: Produce datestamped log output; require human review of what actually happened
- Why: A green pass with no exceptions doesn't prove real events occurred

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.
- Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
# From inside agent-tasks repo root:
git mv projects/galaxy_game/tasks/backlog/current/2026-08-31-HIGH-FEATURE-REAL-LOOP-INTEGRATION-TEST.md \
       projects/galaxy_game/tasks/active/2026-08-31-HIGH-FEATURE-REAL-LOOP-INTEGRATION-TEST.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-31-HIGH-FEATURE-REAL-LOOP-INTEGRATION-TEST.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Verify file paths exist (research)

Confirm all referenced files exist in the workspace before proceeding:
- `galaxy_game/app/jobs/game_simulation_job.rb`
- `galaxy_game/app/models/game_state.rb`
- `galaxy_game/app/models/game.rb`
- Service object(s) underlying `luna_mission:execute` or `orbital_mining:gcc_sat`
- Existing spec conventions (check `spec/` for feature/system/integration test patterns)

### Step 2 — Build the integration test

Create a new spec file (location per existing conventions, e.g. `spec/integration/` or `spec/features/`) that:

1. **Setup**: Uses `Sidekiq::Testing.inline!` to accelerate job execution
2. **Toggle on**: Calls `GameState.first_or_create.toggle_running!` via the real method
3. **Run loop**: Invokes `GameSimulationJob.new.perform` N times for simulated days
4. **Dispatch craft**: Calls the underlying service object(s) for at least one craft/mission action (e.g. Luna precursor or GCC sat)
5. **Log events**: Produces datestamped output like `[Game Day N] <event description>` for both loop and craft events
6. **Reset**: In an `after` block, resets `GameState#running` to false

### Step 3 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH] 2>&1 | tail -20'
```

Expected result: X examples, 0 failures

### Step 4 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Spec: [file:line]
Error: [exact message]
Expected: [value]
Got: [value]

ROOT CAUSE
[one paragraph]

PROPOSED FIX
[exact code change]

RISK
[any shared code affected]

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] Test toggles the real game loop on via `toggle_running!` (or equivalent), not a raw field write, unless confirmed safe
- [ ] Test drives `GameSimulationJob` for real (not a hand-rolled day-loop), accelerated for test speed
- [ ] Test dispatches at least one real craft/mission action via existing service-level code, in the same run
- [ ] Test produces a human-readable, datestamped log of what happened — reviewed by a human afterward, not just asserted pass/fail
- [ ] Test resets `GameState#running` afterward, confirmed not to leak into other tests
- [ ] No claims of "the loop now works end-to-end" — the test's actual output is reported as-is, including any gap it surfaces (e.g. craft still not moving via the loop)

---

## Stop Conditions — escalate to user immediately if:
- If building this reveals the craft-dispatch entry point requires more setup/mocking than is reasonable for an integration test (e.g. it needs a fully deployed settlement with specific state), stop and report — don't build an elaborate fixture-generation system as a side quest
- Stop once the test runs and produces real output — report the output back for review before considering any follow-up (e.g. "now let's fix craft wiring") in scope

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: [spec/file name] — [brief description of root cause and fix]"
git push
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/[FILENAME] projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]

# New/untracked file (just created this session): move with filesystem, then add the final path
mv projects/galaxy_game/tasks/active/[FILENAME] projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]
git add projects/galaxy_game/tasks/completed/[YYYY-MM]/[FILENAME]

git commit -m "chore: move [FILENAME] to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update `docs/[path]/[file].md` — [what to update]
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none — explicitly does NOT require the craft-transit architecture fix first
**Blocks**: nothing directly, but its output will likely inform how the craft-transit architecture work and the event-log/admin-UI work (both still unscoped) get prioritized
**Related**: `2026-08-30-FINDINGS-LIVE-GAME-LOOP-REALITY-CHECK.md`, `2026-08-31-FOLLOWUP-LIVE-GAME-LOOP-DEEP-DIVE.md`

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [test built y/n] | [loop ran for real y/n] | [craft dispatched alongside y/n] | [log output attached y/n]
