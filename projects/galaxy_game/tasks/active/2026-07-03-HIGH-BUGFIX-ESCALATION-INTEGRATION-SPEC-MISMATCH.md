---
status: backlog
priority: HIGH
type: bugfix
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

You are the Implementation Agent.

Project: galaxy_game
Task: Fix escalation_integration_spec.rb display name vs chemical formula mismatch
Task file (active): /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/active/2026-07-03-HIGH-BUGFIX-ESCALATION-INTEGRATION-SPEC-MISMATCH.md

READ FIRST:
1) /Users/tam0013/Documents/git/agent-tasks/README.md
2) /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md
3) The task file above

REQUIRED: Create the STATUS SYNTHESIS REPORT in the summaries folder
(/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries)
before making any changes, then wait for human approval.

---

# TASK: Fix escalation_integration_spec.rb — display name vs chemical formula

**Status**: BACKLOG
**Priority**: HIGH
**Type**: bugfix
**Created**: 2026-07-03

## Problem

`escalation_integration_spec.rb:205` and `:216` expect display names
in `operational_data['target_material']`:

expected: "oxygen"

got: "O2"
expected: "water"

got: "H2O"

The service is correct. Backend code uses chemical formulas throughout
(`O2`, `H2O`, `N2`, `CH4`, etc.). Display names are UI/JSON only.
The specs were written before this convention was locked and were never
updated. The fix is in the specs, not the service.

## Architecture Note — Read Before Touching Anything

The chemical formula convention is a locked architectural decision:
- Backend Ruby code: always chemical formulas (`O2`, `H2O`)
- Display names (`oxygen`, `water`): JSON data and UI layer only
- Do NOT change EscalationService or any service code
- Do NOT add any translation layer between display names and formulas
- Spec fix only: update expected values to match formula convention

## Step 1 — Pre-check (paste output before proceeding)

```bash
grep -n "oxygen\|water\|target_material" \
  galaxy_game/spec/integration/ai_manager/escalation_integration_spec.rb \
  | head -20
```

Confirm lines 205 and 216 and the surrounding context.

## Step 2 — Fix

At line 205: change `eq('oxygen')` to `eq('O2')`
At line 216: change `eq('water')` to `eq('H2O')`

Do not change anything else in the file.

## Step 3 — Verify syntax

```bash
ruby -c \
  galaxy_game/spec/integration/ai_manager/escalation_integration_spec.rb \
  2>&1
```

Must return: `Syntax OK`

## Step 4 — Run targeted spec

```bash
docker exec web bash -c \
  'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec \
  spec/integration/ai_manager/escalation_integration_spec.rb \
  --format progress 2>&1' > /tmp/escalation_spec_result.txt
cat /tmp/escalation_spec_result.txt | tail -20
```

Target: 0 failures in this file.

## Step 5 — Commit

```bash
git add \
  galaxy_game/spec/integration/ai_manager/escalation_integration_spec.rb
git commit -m "fix: update escalation_integration_spec to use chemical formulas (O2, H2O) per locked backend convention"
git push
```

## Stop Conditions

- Any service file touched — stop immediately, this is a spec-only fix
- Failures remain after fix that are not the pre-existing
  escalation_service_spec.rb:429 — stop and report

## Completion Report

Completed by: [agent]
Completion date: YYYY-MM-DD
Final test result: X examples, Y failures
What was changed:
- [file] — [description]

## HANDOFF SUMMARY
HANDOFF SUMMARY: [files updated] | [fix applied] | [remaining failures if any]