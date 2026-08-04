---
status: active
priority: MEDIUM
type: bug-fix
system_domain: TERRA_SIM | OTHER
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-25-MEDIUM-BUG-FIX-SLOW-BASE-SATELLITE-SPEC-RUNTIME.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-25-MEDIUM-BUG-FIX-SLOW-BASE-SATELLITE-SPEC-RUNTIME.md \
         projects/galaxy_game/tasks/active/2026-07-25-MEDIUM-BUG-FIX-SLOW-BASE-SATELLITE-SPEC-RUNTIME.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-25-MEDIUM-BUG-FIX-SLOW-BASE-SATELLITE-SPEC-RUNTIME.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-BUG-FIX-SLOW-BASE-SATELLITE-SPEC-RUNTIME.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Investigate slow base_satellite_spec.rb runtime (37min for 13 examples)
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-07-25
**Last Updated**: 2026-07-25

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — no RSpec strings in this task yet requiring the wrapper check; verification steps below already use the correct format
- **MVP Alignment**: VALID — spec suite runtime affects iteration speed on all satellite/craft work, including recently closed Blueprint Ports and Craft Exhaust tasks
- **MVP Impact Note**: Slow specs directly reduce how many verification cycles can run per session; connects to SPEC_HEALTH
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Diagnostic/profiling task, well within local capability
**Local attempts before cloud**: N/A
**Supervision Level**: standard

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

`spec/models/craft/satellite/base_satellite_spec.rb` was confirmed passing (13 examples, 0 failures) on 2026-07-25 after an unrelated Bootsnap-cache fix resolved a separate LoadError blocking the Blueprint Ports task. However, the run took **37 minutes 43 seconds** total, while file loading itself only took 2 minutes 19.7 seconds — meaning roughly 35 minutes is spent somewhere in setup or example execution, not autoloading. This is disproportionate for 13 examples and needs investigation before it becomes the norm for this spec file or spreads to related satellite/craft specs.

A separate full-suite run earlier the same day showed `AutomaticTerrainGenerator` warnings (`uninitialized constant TerrainAnalysis::TerrainQualityAssessor`) firing for Earth, Mars, Luna, and Titan during celestial body creation. This is an unconfirmed but plausible lead: if satellite specs create celestial bodies as part of setup and that setup cascades into terrain generation, that could be the time sink. This has NOT been verified — treat as a hypothesis to check, not a known root cause.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules, especially `git mv` discipline (Rule 12) given recent stray-file incidents

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Problem Statement

`base_satellite_spec.rb` takes ~37 minutes to run 13 examples, of which only ~2 minutes is file loading. Need to identify where the remaining ~35 minutes goes: a specific slow example, a shared `before` hook running expensive setup (e.g., celestial body / terrain generation), or container resource starvation.

**Observed output** (2026-07-25 run):
```
Finished in 37 minutes 43 seconds (files took 2 minutes 19.7 seconds to load)
13 examples, 0 failures
```

**Current behavior**: Suite passes but takes ~37 minutes for 13 examples.
**Expected behavior**: Runtime proportionate to test count and complexity — no specific target time set yet; first goal is diagnosis, not a fix.

---

## Files Involved

### Primary Files — investigate, do not necessarily edit without approval
| File | Purpose | Key Method/Section |
|---|---|---|
| `spec/models/craft/satellite/base_satellite_spec.rb` | The slow spec file | all examples + any `before` blocks |
| `app/services/star_sim/automatic_terrain_generator.rb` | Suspected terrain generation cost, unconfirmed | `#generate_terrain_for_body`, `#store_generated_terrain` |
| `app/services/star_sim/system_builder_service.rb` | Builds celestial bodies possibly triggering terrain gen in test setup | `#build!`, `#create_celestial_body_record` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `spec/factories/*.rb` (satellite/craft related) | Check what factories create per example — may reveal expensive associations |
| `spec/support/*.rb` | Check for global `before(:suite)` or `before(:each)` hooks that could apply here |

### Migration (if needed)
- [x] No migration needed

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or modifying any files, post a synthesis report in chat per the standard template (see workflow README). State what you're about to do, which files you'll check, and expected outcome (a diagnosis, not necessarily a fix) before proceeding.

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
Per lifecycle rules above. Paste `find` output confirming only one copy before proceeding.

### Step 1 — Profile the spec file
```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec spec/models/craft/satellite/base_satellite_spec.rb --profile 13
```
This ranks all 13 examples by time and reports slow `before`/`after` hooks if `config.profile_examples` is enabled. Paste full output in chat.

### Step 2 — Check for container resource starvation (run in parallel with Step 1 if possible)
```bash
docker stats --no-stream web
```
Run this once while the Step 1 spec is executing, in a separate terminal, to rule out CPU/memory limits as the cause.

### Step 3 — If profiling points to terrain generation, confirm the connection
Check whether any example or shared `before` hook in `base_satellite_spec.rb` (directly or via a factory) creates a `CelestialBody` that triggers `StarSim::SystemBuilderService#build!` or `AutomaticTerrainGenerator`. Do NOT assume this without evidence from Step 1's profiling output — confirm the actual call path first.

### Step 4 — Synthesis Report (before proposing any fix)
```
SYNTHESIS REPORT
Slow example(s): [from --profile output]
Root cause (if identified): [one paragraph, evidence-based]
Proposed fix (if any): [exact change — e.g., stub terrain generation in test env, use build_stubbed instead of create, etc.]
Risk: [any shared code affected — this touches spec setup, check for other specs relying on same factories/hooks]

READY TO APPLY? — waiting for approval
```

Do not apply any fix until explicitly approved. This task's primary deliverable is diagnosis; a fix may become a follow-up task depending on scope.

---

## Acceptance Criteria
- [ ] `--profile 13` output captured and pasted in chat/completion report
- [ ] Root cause identified with evidence (not guessed)
- [ ] `docker stats` output captured to rule in/out resource starvation
- [ ] If a fix is proposed, it does not change test coverage or assertions — only setup/performance
- [ ] No regressions: full satellite/craft spec suite still passes after any change

---

## Stop Conditions — escalate to user immediately if:
- Root cause is in a shared factory, concern, or base class used across many other specs (this would mean the fix has wider blast radius than this task assumes)
- Fixing the slowness would require changing what the specs actually test (not just how fast setup runs)
- Terrain generation is confirmed as the cause but no clear way to stub/bypass it safely is found
- Any architectural decision is required

---

## Commit Instructions
Only if a fix is approved and applied:
```bash
git add [specific files only — never git add .]
git commit -m "perf: [description of root cause and fix] — base_satellite_spec runtime"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-07-25-MEDIUM-BUG-FIX-SLOW-BASE-SATELLITE-SPEC-RUNTIME.md \
       projects/galaxy_game/tasks/completed/2026-07/2026-07-25-MEDIUM-BUG-FIX-SLOW-BASE-SATELLITE-SPEC-RUNTIME.md
git commit -m "chore: move slow base_satellite_spec runtime task to completed/"
```

---

## Documentation
- [ ] No doc changes needed unless a systemic pattern (e.g., "always stub terrain gen in specs") is found — in that case, flag as a doc gap rather than writing it during this task

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: 2026-03-30-HIGH-BUG-FIX-BLUEPRINT-PORTS-REMOVE-FALLBACK.md (the spec that exposed this runtime issue during its own RSpec-blocker verification)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final test result**:

### What was changed


### Issues discovered


### Follow-up tasks needed


### Lessons learned


---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY:
