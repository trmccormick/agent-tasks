---
status: backlog
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: ISRU_PRODUCTION
local_worker_safe: true
---

> **[FILL IN — Tracy]**: `system_domain` and `mvp_alignment` above are Claude's best-guess categorization (pricing/availability resolution touches `Market::NpcPriceCalculator` and several `ai_manager/` service consumers; ISRU/procedural-world production is the practical stake). Please confirm or correct before dispatch — this task's own taxonomy isn't something Claude can verify against the repo.

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Investigation Steps
- [ ] All file paths are verified to exist (several below are carried over from prior chat summaries and NOT independently repo-confirmed by Claude — see Gotcha 3)
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/[SUBFOLDER]/2026-09-16-HIGH-ARCHITECTURE-GEOGRAPHY-AGNOSTIC-PRICING-AVAILABILITY-RESOLVER-DECISION-BRIEF.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/[SUBFOLDER]/2026-09-16-HIGH-ARCHITECTURE-GEOGRAPHY-AGNOSTIC-PRICING-AVAILABILITY-RESOLVER-DECISION-BRIEF.md \
         projects/galaxy_game/tasks/active/2026-09-16-HIGH-ARCHITECTURE-GEOGRAPHY-AGNOSTIC-PRICING-AVAILABILITY-RESOLVER-DECISION-BRIEF.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-16-HIGH-ARCHITECTURE-GEOGRAPHY-AGNOSTIC-PRICING-AVAILABILITY-RESOLVER-DECISION-BRIEF.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, investigation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Geography-Agnostic Pricing/Availability Resolver — Architecture Decision Brief
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-09-16
**Last Updated**: 2026-09-16

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*

- **Template Conformance**: [FILL IN]
- **Docker Wrapper Check**: N/A — this task runs no application code, no migrations, no writes
- **MVP Alignment**: [FILL IN]
- **MVP Impact Note**: [FILL IN — one line on how this connects to procedural-world settlement/production]
- **Action Line**: [FILL IN]

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires terminal/grep access to trace live call sites and consumer behavior — not available to Claude
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully (first dispatch of this investigation)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/path/to/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/path/to/agent-tasks/projects/galaxy_game/README.md`
3. **Source context**: the epoxy_resin task's blocker report and synthesis report (paths [FILL IN] — attach exact locations before dispatch)
4. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context
An epoxy-resin material data-rework task was activated, hit preflight, and was blocked: its premise depended on a geography-agnostic material-sourcing model that does not exist. Investigation during that preflight found the more urgent gap is not sourcing schema but a live pricing/availability resolver problem — `Market::NpcPriceCalculator` has body-specific assumptions (`pricing.lunar_production`) hardcoded at multiple call sites, and it is currently unknown whether procedurally-generated non-Sol worlds ever reach that code path at all. This task investigates and produces a decision brief only — it does not implement or design a schema.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- Epoxy_resin task's blocker report and synthesis report — [FILL IN exact path] — primary source context for this task, do not re-derive findings already established there

> If a doc doesn't exist for this area, do not create one during this task.
> Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Credentials
None needed — this is a read-only codebase investigation task.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is an evidence/decision task, not an implementation or schema-design task.
- ❌ Wrong: proposing or writing a material-JSON `sourcing` schema, even as a "just a draft" example.
- ✅ Right: documenting what candidate owners *could* hold sourcing/availability data, with tradeoffs, and stopping there.
- Why: no validated consumer of any sourcing/production field exists yet (per epoxy blocker report); designing a schema before that's settled risks repeating the `production.input_materials` situation — a populated field structure nothing reads (see `material-schema-production-gap` prior findings, referenced in project memory).

⚠️ **GOTCHA 2**: Do not assume the gap is "live" without checking.
- ❌ Wrong: treating `pricing.lunar_production`'s Sol-only naming as automatically an active production bug.
- ✅ Right: first confirm whether any procedurally-generated non-Sol world's settlement actually invokes `NpcPriceCalculator` today, in a reachable path — not just theoretically possible.
- Why: if nothing currently reaches this code with a non-Sol body, the issue is latent (a risk for later work), not a live bug — this materially changes urgency and should be stated plainly in the brief, not glossed over.

⚠️ **GOTCHA 3**: Several file paths below come from prior chat-summarized findings, not from Claude's own repo access — Claude has no filesystem access and cannot verify exact paths/line numbers itself.
- ❌ Wrong: treating paths like `app/services/market/npc_price_calculator.rb` or line numbers as ground truth without checking.
- ✅ Right: verify every path and line number against the actual repo state before relying on it; if a path has drifted, note the correction in your synthesis report.
- Why: this task's whole value is trustworthy evidence — a wrong path presented as fact defeats the purpose.

⚠️ **GOTCHA 4**: Do not conflate this task with production-input schema normalization.
- ❌ Wrong: investigating or commenting on `production.input_materials` population/schema-drift as part of this brief.
- ✅ Right: leave that entirely to its own separate task (see Dependencies below); if you notice something relevant while working here, note it in Completion Report → Follow-up tasks needed, don't fold it in.
- Why: keeping these two threads separate was an explicit decision — mixing them dilutes both.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or reading further, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Geography-Agnostic Pricing/Availability Resolver — Architecture Decision Brief
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Investigate whether procedurally-generated non-Sol worlds currently reach `NpcPriceCalculator`'s body-specific pricing logic, inventory all related consumers and body-name assumptions, evaluate candidate architectural owners for resolving material availability/pricing by location, and produce a decision brief with a recommendation — no code, schema, or data changes.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/market/npc_price_calculator.rb` | Pricing oracle, `pricing.lunar_production` call sites | not started |
| `app/services/ai_manager/precursor_capability_service.rb` | ISRU feasibility by body | not started |
| `app/services/ai_manager/procurement_service.rb` | Local production vs. market pricing consumer | not started |
| [FILL IN any others found during Step 1] | | |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read epoxy_resin blocker report / synthesis report
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
A single decision-brief MD file in the summaries folder containing: live/latent/unresolved classification with citations; consumer and body-name-assumption inventories; scored candidate-owner comparison; explicit unresolved unknowns; one recommended direction for human approval.

### Critical Gotchas I Will Avoid
- ❌ Designing a sourcing schema — instead ✅ evaluating candidate owners only, no schema
- ❌ Assuming the gap is live without checking — instead ✅ confirming reachability first
```

**SYNTHESIS COMPLETE.** Ready to proceed with investigation.

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement
It is not established whether procedurally-generated non-Sol worlds ever reach `NpcPriceCalculator`'s body-specific pricing logic (`pricing.lunar_production`), nor which architectural component should own resolving material availability/pricing for an arbitrary `(material, location)` pair. This blocks any future geography-agnostic material work, including the epoxy_resin task that surfaced it.

**Current behavior**: `pricing.lunar_production`-style assumptions are hardcoded at multiple call sites in `NpcPriceCalculator`; behavior for a non-Sol body when this value is absent is unknown/unverified.
**Expected outcome of this task**: A decision brief classifying the issue's liveness and recommending an architectural owner — not a fix, not a schema.

---

## Files Involved

### Primary Files — read-only investigation targets (DO NOT EDIT)
| File | Purpose | Key Method/Section |
|---|---|---|
| `galaxy_game/app/services/market/npc_price_calculator.rb:12` | Pricing oracle; `pricing.lunar_production` assumptions at 4 call sites | class def line 12; `pricing.lunar_production` lines 112, 173, 252, 491 |
| `galaxy_game/app/services/ai_manager/precursor_capability_service.rb:13-253` | ISRU feasibility assessment by body | methods at lines 13-253 (can_produce_locally?, local_resources, production_capabilities, isru_options, etc.) |
| `galaxy_game/app/services/ai_manager/procurement_service.rb:3-106` | `check_market_price` (confirmed stub, hardcoded 4-item `base_prices` hash), `purchase_from_market` (unimplemented) | methods at lines 3-106 |
| `galaxy_game/app/services/ai_manager/escalation_service.rb:10-342` | EAP enforcement, bid pricing via `Market::NpcPriceCalculator.calculate_bid` | methods at lines 10-342 (handle_resource_shortage at line 10) |
| `galaxy_game/app/services/ai_manager/resource_acquisition_service.rb:6-139` | EAP ceiling check, `calculate_gcc_contract_price` | methods at lines 6-139; **NOTE**: earlier report elsewhere in this thread said "148 lines" — discrepancy between grep (lines 6-139) and that claim needs resolution by dispatcher |
| `galaxy_game/app/services/lookup/material_lookup_service.rb:6` | Returns raw JSON with no sourcing-schema validation; verify this still holds | class def line 6 |

**⚠️ Sourcing-block contradiction (Step 6)**: Grep confirms **zero** `sourcing` blocks in any material JSON file in the current repo state. The epoxy blocker report's "3 of 207 files have a sourcing block" claim could not be reproduced — flagged as either stale or inaccurate, not resolved here.

### Reference Files — read for context, do not edit
| File | Why You Need It |
|---|---|
| Material JSON files with a `sourcing` block — **grep confirms ZERO in current repo state** (see contradiction note above) | Check age/commit history and whether anything actually consumes them |
| `galaxy_game/app/services/lookup/material_lookup_service.rb:6` | Returns raw JSON with no sourcing-schema validation; verify this still holds |

### Migration
- [x] No migration needed — this task produces a decision brief only

---

## Investigation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order. This task makes NO code, data, schema, or configuration changes — every step below is read-only investigation.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
```bash
git mv projects/galaxy_game/tasks/backlog/[SUBFOLDER]/2026-09-16-HIGH-ARCHITECTURE-GEOGRAPHY-AGNOSTIC-PRICING-AVAILABILITY-RESOLVER-DECISION-BRIEF.md \
       projects/galaxy_game/tasks/active/2026-09-16-HIGH-ARCHITECTURE-GEOGRAPHY-AGNOSTIC-PRICING-AVAILABILITY-RESOLVER-DECISION-BRIEF.md
```
Then update `status: backlog → status: active`, then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-16-HIGH-ARCHITECTURE-GEOGRAPHY-AGNOSTIC-PRICING-AVAILABILITY-RESOLVER-DECISION-BRIEF.md"
```
**Paste the output of the find command in chat before proceeding.**

### Step 1 — Classify liveness: does anything reach `NpcPriceCalculator` for a non-Sol body?
Trace every call path into `NpcPriceCalculator`'s pricing methods. For each, determine whether it can be reached with a settlement/body that is not Earth/Luna/Mars-named (i.e. a procedurally-generated world). Cite exact file:line for each call site and each reachability finding. Classify the overall issue as **LIVE** (a non-Sol body currently reaches this code), **LATENT** (the code path exists but nothing currently invokes it with a non-Sol body), or **UNRESOLVED** (could not determine — state exactly why).

### Step 2 — Inventory `pricing.lunar_production` consumers and absent-value behavior
For every consumer of `pricing.lunar_production` or equivalent body-specific pricing fields, document: file:line, what it does with the value, and what happens when the value is absent (raises, falls back to a default, silently returns a wrong number, etc.).

### Step 3 — Inventory runtime body-name assumptions beyond pricing
Search for hardcoded body names (Earth, Luna, Mars, etc.) affecting availability, transport/logistics, or precursor/local-production logic — not limited to the pricing calculator. Cite file:line for each.

### Step 4 — Trace `PrecursorCapabilityService` arbitrary-world behavior
Document its inputs and behavior specifically for a procedurally-generated (non-Sol) world: does it degrade gracefully, error, or silently return incorrect/default data? Cite file:line.

### Step 5 — Trace EAP/extraction/CapEx pricing-strategy boundaries
Document what triggers each strategy, who calls each, and whether any assumes a known/named body. Cite file:line.

### Step 6 — Inspect material `sourcing` blocks
**⚠️ IMPORTANT**: Grep confirms **zero** `sourcing` blocks in any material JSON file in the current repo state. The epoxy blocker report's "3 of 207 files have a sourcing block" claim could not be reproduced.

Action: Report this contradiction explicitly in your decision brief. Do not assume the 3 files exist. If they once existed, note that as a finding — do not fabricate file paths.

### Step 7 — Evaluate candidate architectural owners
Evaluate 2–4 candidates for resolving a `(material, location)` availability/pricing decision: material metadata, geology/deposits, recipes/facilities, settlement-market, logistics/import, or a hybrid runtime resolver. Score each against: fit with current call-site needs (from Steps 1-5), procedural-world support, migration risk, ownership clarity, testability, and compatibility with existing systems.

### Step 8 — Write the decision brief
Save as a synthesis-style MD file in the summaries folder (not pasted in chat) containing: the Step 1 classification with citations; Step 2-6 inventories; the Step 7 scored comparison; explicit unresolved unknowns; and one recommended direction — clearly marked as a recommendation awaiting human approval, not a decision already made.

---

## Acceptance Criteria
- [ ] Liveness classification (LIVE / LATENT / UNRESOLVED) delivered with file:line citations
- [ ] `pricing.lunar_production` consumer inventory complete with absent-value behavior documented for each
- [ ] Runtime body-name assumption inventory complete (not limited to pricing)
- [ ] `PrecursorCapabilityService` arbitrary-world behavior traced and documented
- [ ] EAP/extraction/CapEx strategy boundaries traced and documented
- [ ] All 3 existing `sourcing` blocks checked for age/commit history and actual consumer status
- [ ] 2-4 candidate architectural owners evaluated and scored against the 5 stated criteria
- [ ] Decision brief delivered as an MD file in summaries/, with one recommended direction clearly flagged as pending human approval
- [ ] Zero code, schema, data, configuration, or test files modified

---

## Stop Conditions — escalate to user immediately if:
- Any step would require writing a schema, migration, or code change to proceed
- The investigation reveals the dual-deposit or other unrelated live bug requiring immediate attention — flag separately, do not fix here
- A file path in this task cannot be found or has clearly moved — note the correction, do not guess and proceed silently
- Findings suggest production-input schema normalization work is actually required to answer this task's questions — stop and flag the overlap rather than absorbing that scope

---

## Commit Instructions
This task produces no code changes. Only the synthesis report and decision brief (both MD files in `summaries/`) should be committed:
```bash
git add projects/galaxy_game/summaries/[synthesis-report-filename].md
git add projects/galaxy_game/summaries/[decision-brief-filename].md
git commit -m "docs: geography-agnostic pricing/availability resolver — decision brief"
git push
```

**Task file move on completion:**
```bash
git mv projects/galaxy_game/tasks/active/2026-09-16-HIGH-ARCHITECTURE-GEOGRAPHY-AGNOSTIC-PRICING-AVAILABILITY-RESOLVER-DECISION-BRIEF.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-16-HIGH-ARCHITECTURE-GEOGRAPHY-AGNOSTIC-PRICING-AVAILABILITY-RESOLVER-DECISION-BRIEF.md
git commit -m "chore: move geography-agnostic pricing resolver decision brief to completed/"
```

---

## Documentation
- [ ] No doc changes needed for this task itself
- [x] Flag doc gap: the wiki's economy pages likely need updating once a resolver direction is approved — do not update now, add to backlog as a follow-up once the decision lands

---

## Dependencies
**Blocked by**: none
**Blocks**: any future material-sourcing rework (including a re-opened, narrower epoxy_resin follow-up task, once this decision lands)
**Related tasks**: `2026-09-16-MEDIUM-DATA-MATERIAL-PRODUCTION-INPUT-SCHEMA-NORMALIZATION-PLANNING.md` (independent in scope, no blocking relationship either direction, but shares the epoxy_resin blocker report as source context); the original epoxy_resin task (superseded by this task — see its closure note for traceability)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final result**: Decision brief delivered — LIVE / LATENT / UNRESOLVED (circle one), recommendation: [one line]

### What was changed
- None — investigation only, decision brief saved to `summaries/`

### Issues discovered
[Any problems found during investigation that weren't anticipated]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [decision brief filename] | [LIVE/LATENT/UNRESOLVED] | [next action needed — human approval of recommended direction]
