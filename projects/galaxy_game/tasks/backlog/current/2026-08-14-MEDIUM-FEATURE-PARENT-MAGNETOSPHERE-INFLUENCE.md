---
status: backlog
priority: MEDIUM
type: feature
system_domain: TERRA_SIM
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md \
         projects/galaxy_game/tasks/active/2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: 2026-08-14-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Implement parent-magnetosphere influence bonus (Option B)
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-08-14
**Last Updated**: 2026-08-14

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: [Qwen to fill during triage]
- **Docker Wrapper Check**: N/A — verify during triage whether generation specs exist for this area
- **MVP Alignment**: [Qwen to confirm — affects terraforming simulation for Titan/Europa/Ganymede, prime terraforming targets but not directly Luna MVP scope]
- **MVP Impact Note**: Indirect gameplay value only (terraforming timeline, shell printing cost, AI assessment) — confirmed low direct player-facing impact per synthesis report
- **Action Line**: NEEDS MANUAL REVIEW — depends on companion bugfix task landing first

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Well-specified from synthesis report, but blocked — do not dispatch until companion bugfix task is confirmed merged
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully (first dispatch of this logic)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below
4. **Reference**: `summaries/2026-08-07-PARENT-MAGNETOSPHERE-INFLUENCE-GAMEPLAY-VALUE.md` — full options analysis (A/B/C), 6-consumer impact map, and final recommendation this task implements. Do not re-derive the analysis — reference it.
5. **Confirm companion task status**: `2026-08-14-LOW-BUG-FIX-HAS-MAGNETOSPHERE-DERIVATION.md` must be merged before starting this task's implementation.

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
The synthesis report (completed 2026-08-14) recommends Option B: moons orbiting magnetized parent bodies (Titan/Saturn, Europa or Ganymede/Jupiter) should receive a magnetosphere protection bonus, affecting atmospheric-loss simulation during terraforming. Full options analysis (A/B/C) and the 6-consumer impact map are in the synthesis report.

**Depends on**: `2026-08-14-LOW-BUG-FIX-HAS-MAGNETOSPHERE-DERIVATION.md` must land first — this bonus would compound an incorrect baseline otherwise.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md`
- `docs/new_agent/rules/GUARDRAILS.md`
- `summaries/2026-08-07-PARENT-MAGNETOSPHERE-INFLUENCE-GAMEPLAY-VALUE.md`

> If a doc doesn't exist for this area, do not create one during this task. Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: `parent_influence_modifier` stub is hardcoded.
- ❌ Wrong: Assume the existing stub (referenced in synthesis report, ~line 1395 of the generation service) already does something — it's hardcoded to `0.0` and takes no parent data parameter.
- ✅ Right: Confirm current state of this stub first; it needs parent magnetosphere data threaded in, or the logic placed elsewhere entirely.
- Why: Building on top of a silent no-op stub without checking would produce a bonus calculation that never actually fires.

⚠️ **GOTCHA 2**: Parent-before-moon generation ordering.
- ❌ Wrong: Assume parent body data is available at any point during generation.
- ✅ Right: Confirm parent body is created in an earlier pass (Pass 2) before moons (Pass 3) in the current SystemBuilderService — this was true per the synthesis report but must be re-verified against current code, not assumed from the report alone.
- Why: If ordering has changed since the synthesis report, the parent lookup would silently fail or use stale/incomplete data.

⚠️ **GOTCHA 3**: Scope discipline — this is Option B only, not Option C.
- ❌ Wrong: Extend into orbital-position-aware modeling (time-varying position, magnetic dipole calculations) because it seems "more correct."
- ✅ Right: Static parent-influence bonus only, per the synthesis report's explicit recommendation against Option C (high cost, no player-facing mechanic currently justifies it).
- Why: The synthesis report already evaluated and rejected Option C as overkill given only 6 known consumers, none reading magnetosphere values with fine-grained enough resolution to benefit.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE
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
- ✅ Confirmed companion bugfix task (has_magnetosphere derivation) is merged
- ✅ Understand architecture gotchas above

### Expected Outcomes
[Exact description of what "done" looks like]

### Critical Gotchas I Will Avoid
- ❌ [wrong approach] — instead ✅ [right approach]

---

**SYNTHESIS COMPLETE.** Ready to proceed.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement
Moons currently get no magnetosphere benefit from their parent body, even when the parent has strong `magnetosphere_strength`. This makes prime terraforming targets (Titan, Europa, Ganymede) simulate atmospheric loss without realistic parent shielding.

**Current behavior**: No parent-influence calculation exists; `parent_influence_modifier` stub is hardcoded to `0.0`.
**Expected behavior**: Moons orbiting a parent with `magnetosphere_strength > 0.1` receive a bonus to their own `magnetosphere_strength`, capped at 1.0, applied during generation.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `[FILL IN — Qwen to confirm exact path]` (likely ProceduralGenerator) | Generates celestial body properties | `#calculate_magnetosphere_strength` or `#add_special_properties` — `[FILL IN exact method/line]` |
| `[FILL IN]` | Contains `parent_influence_modifier` stub referenced in synthesis report (~line 1395) | `[FILL IN — confirm exact file, since synthesis report line number may refer to a different file than the primary generation file above]` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `summaries/2026-08-07-PARENT-MAGNETOSPHERE-INFLUENCE-GAMEPLAY-VALUE.md` | Full consumer map, options analysis, recommendation this task implements |
| SystemBuilderService (path TBD) | Confirm Pass 2 (parent) → Pass 3 (moon) generation ordering still holds |

### Migration (if needed)
- [ ] No migration needed — properties field already exists (JSONB)

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

```bash
git mv projects/galaxy_game/tasks/backlog/current/2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md \
       projects/galaxy_game/tasks/active/2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md"
```

**Paste the output of the find command in chat before proceeding.**

### Step 1 — Confirm companion bugfix task is merged
Do not proceed past this step if `2026-08-14-LOW-BUG-FIX-HAS-MAGNETOSPHERE-DERIVATION.md` is not confirmed completed and merged. If unmerged, stop and escalate.

### Step 2 — Confirm stub location and generation pass ordering
Locate the `parent_influence_modifier` stub referenced in the synthesis report. Confirm parent-before-moon pass ordering in current SystemBuilderService (or equivalent).

### Step 3 — Implement the bonus calculation
```ruby
# Proposed (confirm exact file/method first — see Files Involved table):
if body_data[:type] == 'moon' && body_data[:parent_identifier].present?
  parent = find_parent_body(body_data[:parent_identifier])
  if parent && parent['magnetosphere_strength'].to_f > 0.1
    attrs[:properties]['has_magnetosphere'] = true if !attrs[:properties]['has_magnetosphere']
    attrs[:properties]['magnetosphere_strength'] = [
      body_data['magnetosphere_strength'].to_f + (parent['magnetosphere_strength'].to_f * 0.3)
    ].min(1.0)
  end
end
```

### Step 4 — Verify Titan, Europa or Ganymede
Confirm bonus applies correctly for known cases (Titan/Saturn, Europa or Ganymede/Jupiter) and does NOT apply to moons without a qualifying parent.

### Step 5 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [SPEC_PATH — identify relevant generation specs] 2>&1 | tail -20'
```

Expected result: 0 failures, no regressions.

### Step 6 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
File: [file:line]

ROOT CAUSE / FEATURE SUMMARY
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
- [ ] Companion bugfix task confirmed merged before this task's implementation begins
- [ ] Titan (Saturn), Europa or Ganymede (Jupiter) receive `magnetosphere_strength` bonus from parent per the formula above
- [ ] Bonus capped at 1.0
- [ ] No change to bodies without a qualifying parent (`magnetosphere_strength <= 0.1`)
- [ ] Isolation run: 0 failures
- [ ] No regressions in related specs
- [ ] Full suite run completed and logged (human runs overnight — agent does not trigger)

---

## Stop Conditions — escalate to user immediately if:
- Companion bugfix task is not confirmed merged
- Parent data isn't reliably available at moon-generation time in current code (contradicting synthesis report Gotcha 2) — do not restructure generation pass ordering unsupervised
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause touches a shared concern, base class, or factory used across many specs
- Scope creeps toward Option C (orbital-position-aware modeling) — stop and escalate rather than expanding

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "feature: parent-magnetosphere influence bonus (Option B)"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md \
       projects/galaxy_game/tasks/completed/2026-08/2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md

git commit -m "chore: move 2026-08-14-MEDIUM-FEATURE-PARENT-MAGNETOSPHERE-INFLUENCE.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Optional polish (not in scope for this task): UI hint for magnetosphere protection status on celestial body detail view — flag as follow-up, do not build here

---

## Dependencies
**Blocked by**: 2026-08-14-LOW-BUG-FIX-HAS-MAGNETOSPHERE-DERIVATION.md
**Blocks**: none
**Related tasks**: 2026-08-07-RESEARCH-PARENT-MAGNETOSPHERE-INFLUENCE-GAMEPLAY-VALUE.md (completed, source synthesis)

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

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
