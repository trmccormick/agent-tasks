---
status: backlog
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

---

## 🎯 Objective

Fix inventory validation inconsistency in `FittingService`. Currently `install_units` validates inventory before installation, but `install_modules` and `install_rigs` bypass inventory checks entirely. This causes test failures and allows fitting components that aren't in inventory.

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: PASS — all required sections present
- **Docker Wrapper Check**: PASS — RSpec strings use correct docker exec format
- **MVP Alignment**: VALID — Phase 8 scope (shipyard/craft fitting validation)
- **MVP Impact Note**: Inventory parity ensures craft fitting only uses available components; critical for AI Manager autonomous decision-making accuracy in Phase 8 shipyard testing
- **Codebase State Verified**: 2026-07-20 — install_modules and install_rigs still lack inventory checks; spec passes (4 examples, 0 failures) because tests don't cover modules/rigs inventory validation
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Well-specified code change, clear pattern to follow from install_units
**Local attempts before cloud**: N/A — first dispatch
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
`FittingService.fit!` handles installing units, modules, and rigs onto a target craft. Units get inventory validation before installation, but modules and rigs bypass this check entirely. This is a code quality gap — the bug exists but current tests don't catch it because they only verify units get checked.

**Phase**: Phase 8 (shipyard + craft construction validation)
**Scope**: Fix inventory parity across all three install methods

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: Tests pass despite the bug**
- ❌ Wrong: Assuming tests will catch this fix — they won't. Current specs only verify units get inventory-checked.
- ✅ Right: You MUST add new test cases for modules and rigs inventory validation, OR update existing specs to cover all three types.

⚠️ **GOTCHA 2: Nil inventory is intentional**
- ❌ Wrong: Treating nil inventory as an error condition.
- ✅ Right: `inventory.nil?` means "skip inventory check" — fitting without inventory should succeed for all three types (units, modules, rigs).

⚠️ **GOTCHA 3: Dry run bypasses inventory for ALL types**
- The dry_run path intentionally skips inventory checks across the board. Do not add inventory logic to the dry_run branch.

---

## Problem Statement

`FittingService.fit!` has an inventory validation gap: units are checked against inventory before installation, but modules and rigs bypass this check entirely. This allows fitting components that aren't in inventory and creates inconsistent behavior across component types.

**Current behavior**: Units validated ✅ | Modules not validated ❌ | Rigs not validated ❌
**Expected behavior**: All three types (units, modules, rigs) validated against inventory before installation

### Why Tests Pass Despite the Bug

The existing 4 specs only verify units get inventory-checked. No test case exercises modules or rigs with an inventory that's missing those items. The gap is invisible to current coverage.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/fitting_service.rb` | Fix inventory parity | install_modules (line ~73), install_rigs (line ~98) |
| `galaxy_game/spec/services/fitting_service_spec.rb` | Add test coverage for modules/rigs | New test cases needed |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `galaxy_game/app/services/fitting_service.rb:35-62` | install_units pattern to replicate |
| `spec/factories/` | Verify module/rig factory structure for test setup |

### Migration (if needed)
- [x] No migration needed

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
git mv projects/galaxy_game/tasks/backlog/current/2026-04-01-HIGH-BUGFIX-FITTING-SERVICE-INVENTORY-PARITY.md \
       projects/galaxy_game/tasks/active/2026-04-01-HIGH-BUGFIX-FITTING-SERVICE-INVENTORY-PARITY.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-04-01-HIGH-BUGFIX-FITTING-SERVICE-INVENTORY-PARITY.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Add inventory check to install_modules

Pass `check_inventory` lambda to `install_modules` (currently only passed to `install_units`).

```ruby
# Before (line ~16):
if fit_data['modules']
  install_modules(target, fit_data['modules'], result, dry_run)
end

# After:
if fit_data['modules']
  install_modules(target, fit_data['modules'], result, dry_run, check_inventory)
end

# Before (install_modules signature):
def self.install_modules(target, modules, result, dry_run)

# After:
def self.install_modules(target, modules, result, dry_run, check_inventory)

# Before (inside install_modules, before target.add_module call):
module_result = target.add_module(module_id)

# After:
unless check_inventory.call(module_id)
  result.add_error("Missing inventory item: #{module_id}")
  next
end
module_result = target.add_module(module_id)
```

### Step 2 — Add inventory check to install_rigs

Same pattern for `install_rigs`.

```ruby
# Before (line ~19):
if fit_data['rigs']
  install_rigs(target, fit_data['rigs'], result, dry_run)
end

# After:
if fit_data['rigs']
  install_rigs(target, fit_data['rigs'], result, dry_run, check_inventory)
end

# Before (install_rigs signature):
def self.install_rigs(target, rigs, result, dry_run)

# After:
def self.install_rigs(target, rigs, result, dry_run, check_inventory)

# Before (inside install_rigs, before target.add_rig call):
rig_result = target.add_rig(rig_id)

# After:
unless check_inventory.call(rig_id)
  result.add_error("Missing inventory item: #{rig_id}")
  next
end
rig_result = target.add_rig(rig_id)
```

### Step 3 — Add test cases for modules and rigs inventory validation

Add test cases to `fitting_service_spec.rb` that verify modules and rigs are checked against inventory:

1. **"rejects modules not in inventory"** — create inventory with some items, try to fit including a module NOT in inventory → expect errors include missing module
2. **"rejects rigs not in inventory"** — same pattern for rigs
3. **"fits all components from inventory (modules and rigs)"** — verify modules/rigs pass when they ARE in inventory

### Step 4 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/galaxy_game — do NOT add cd /home/galaxy_game.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/fitting_service_spec.rb --format documentation 2>&1 | tail -40'
```

Expected result: 7 examples (4 existing + 3 new), 0 failures

### Step 5 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Spec: spec/services/fitting_service_spec.rb
Error: N/A (no errors expected after fix)
Expected: 7 examples, 0 failures
Got: [actual result]

ROOT CAUSE
install_modules and install_rigs bypass inventory validation that install_units correctly enforces. The check_inventory lambda was only passed to install_units.

PROPOSED FIX
- Pass check_inventory to install_modules and install_rigs
- Add inventory guard before target.add_module/add_rig calls (same pattern as install_units)
- Add test cases for modules/rigs inventory validation

RISK
- Shared concern methods (target.add_module, target.add_rig) — verify they don't have their own internal checks that would make this redundant
- Existing tests pass but don't cover this gap — new coverage required

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] `install_modules` receives `check_inventory` parameter and validates before calling `target.add_module`
- [ ] `install_rigs` receives `check_inventory` parameter and validates before calling `target.add_rig`
- [ ] Both methods handle `inventory: nil` case (skip check, allow fitting)
- [ ] New test cases added for modules/rigs inventory validation
- [ ] All 7 specs pass (4 existing + 3 new)
- [ ] No regressions in related craft fitting specs
- [ ] Isolation run: 0 failures
- [ ] Full suite run completed and logged (human runs overnight — agent does not trigger)

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- A database migration is needed that wasn't anticipated
- Any architectural decision is required
- Fix requires changing more files than the task specifies

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
- [x] No doc changes needed

---

## Dependencies
**Blocked by**: none
**Blocks**: Craft fitting pipeline (Phase 8)
**Related tasks**: `2026-04-01-HIGH-BUG-FIX-STORAGE-CAPACITY-FILTER.md` (inventory-related)

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
