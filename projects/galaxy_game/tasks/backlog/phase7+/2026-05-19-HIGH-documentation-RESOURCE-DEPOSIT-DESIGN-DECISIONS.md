---
status: backlog
priority: HIGH
type: documentation
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT | ISRU_PRODUCTION
local_worker_safe: false
---

# TASK: Document ResourceDeposit Design Decisions and Spawn Architecture

**Status**: BACKLOG
**Priority**: HIGH
**Type**: documentation
**Created**: 2026-05-19
**Last Updated**: 2026-05-19

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — documentation task, no RSpec runs
- **MVP Alignment**: VALID — ResourceDeposit is Luna MVP critical path
- **MVP Impact Note**: Design decisions made during Task 4 implementation session are not yet recorded in any permanent doc. Missing decisions will cause incorrect implementation in Tasks 5 and 6.
- **Action Line**: READY FOR CLOUD HANDOFF

---

## Agent Assignment

**Assigned To**: GPT-4.1 0x or Gemini
**Why This Agent**: Documentation writing from a clear decision list — no reasoning required, just accurate recording
**Supervision Level**: Watched carefully

---

## Context

During the 2026-05-18/19 session, the ResourceDeposit model (Task 4) was designed and implemented. Several critical architectural decisions were made in conversation that are not yet recorded in any permanent documentation. These decisions directly govern Task 5 (DepositSpawner) and Task 6 (Game Event Integration). If not documented before those tasks begin, implementing agents will make incorrect assumptions.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions, update this file
- `docs/new_agent/README.md` — workspace overview
- `docs/new_agent/tasks/backlog/2026-05/2026-05-18-OVERVIEW-Resource-Spawning-Task-Breakdown.md` — parent overview for this system

---

## Problem Statement

The following design decisions were made in session on 2026-05-18/19 and exist only in conversation history. They must be recorded in `DECISIONS.md` and the relevant task files before Task 5 begins.

**Current behavior**: Decisions exist only in chat history
**Expected behavior**: Decisions recorded in `DECISIONS.md` and referenced in Task 5 and Task 6 task files

---

## Decisions to Document

Record all of the following in `docs/new_agent/rules/DECISIONS.md` under a new section:
`## ResourceDeposit & Spawning System (2026-05-19)`

### Decision 1 — Lazy Spawning Only
Deposits are **never pre-seeded at world generation or game start**.
A `ResourceDeposit` record does not exist until a survey action creates it via the `DepositSpawner`.
The plausibility engine holds scientific truth about what *should* exist — the deposit record is only created when triggered.

### Decision 2 — Survey Action is the Canonical MVP Spawn Trigger
For MVP, the survey action is the only trigger that spawns deposits.
Future triggers (settlement founding, mission completion, first-visit) are post-MVP.
Task 3 (Trigger System Design) should reflect this as the confirmed MVP trigger.

### Decision 3 — Squatters Rights Ownership Model
Possession is active, not declarative.
A faction owns what it is actively extracting via an active mine structure.
If a mine goes inactive or is abandoned, the claim lapses and the deposit becomes available to new claimants.
Ownership is tracked on the **mine/extraction structure**, not on the `ResourceDeposit` record.

### Decision 4 — Deposits Permanently Anchored to Geological Parent
`ResourceDeposit` records are permanently parented to their geological feature or celestial body via the `depositable` polymorphic association.
Deposits **never re-parent** to a settlement or structure.
Discovery is a separate concern handled outside the deposit model.

### Decision 5 — Status Enum Meaning
```
undiscovered  (0) — deposit exists in DB (spawned), not yet known to any faction
available     (1) — known, no active claim
claimed       (2) — active mine exists at this deposit
depleted      (3) — current_mass_kg is zero, no longer mineable
```
Default on spawn: `undiscovered`.

### Decision 6 — Feature Association Uses AdaptedFeature
The `feature` association on `ResourceDeposit` uses `CelestialBodies::Features::AdaptedFeature`, not `BaseFeature`.
Real geological features in the database (Luna craters, lava tubes) are `AdaptedFeature` records.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Section |
|---|---|---|
| `docs/new_agent/rules/DECISIONS.md` | Locked architectural decisions | Add new section at bottom |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/new_agent/tasks/backlog/2026-05/2026-05-18-IMPL-Create-DepositSpawner-Service.md` | Add reference to these decisions in context section |
| `docs/new_agent/tasks/backlog/2026-05/2026-05-18-IMPL-Integrate-Spawning-With-Game-Events.md` | Add reference to these decisions in context section |
| `docs/new_agent/tasks/backlog/2026-05/2026-05-18-DESIGN-Deposit-Trigger-System-And-Equipment-Gating.md` | Update to reflect survey as confirmed MVP trigger |

---

## Implementation Steps

### Step 1 — Add decisions section to DECISIONS.md
Open `docs/new_agent/rules/DECISIONS.md`.
Add a new section at the bottom titled `## ResourceDeposit & Spawning System (2026-05-19)`.
Record all 6 decisions above verbatim.

### Step 2 — Update Task 5 context section
Open `2026-05-18-IMPL-Create-DepositSpawner-Service.md`.
Add a reference line in the Context section:
> Design decisions governing this task are locked in `docs/new_agent/rules/DECISIONS.md` under "ResourceDeposit & Spawning System (2026-05-19)". Read before implementing.

### Step 3 — Update Task 6 context section
Same as Step 2 for `2026-05-18-IMPL-Integrate-Spawning-With-Game-Events.md`.

### Step 4 — Update Trigger System design task
Open `2026-05-18-DESIGN-Deposit-Trigger-System-And-Equipment-Gating.md`.
Add a note that survey action is confirmed as the canonical MVP trigger per session decision 2026-05-19.

---

## Acceptance Criteria
- [ ] `DECISIONS.md` contains all 6 decisions under correct section header
- [ ] Task 5 file references DECISIONS.md
- [ ] Task 6 file references DECISIONS.md
- [ ] Trigger design task updated with survey as confirmed MVP trigger
- [ ] No code changes made
- [ ] No new task files created beyond what is specified

---

## Stop Conditions — escalate to user immediately if:
- `DECISIONS.md` does not exist — do not create it, flag to human
- Task 5 or Task 6 files are not in the expected location — flag, do not search codebase

---

## Commit Instructions
Run git commands on **host** (Intel Mac), not inside container:
```bash
git add docs/new_agent/rules/DECISIONS.md
git add docs/new_agent/tasks/backlog/2026-05/2026-05-18-IMPL-Create-DepositSpawner-Service.md
git add docs/new_agent/tasks/backlog/2026-05/2026-05-18-IMPL-Integrate-Spawning-With-Game-Events.md
git add docs/new_agent/tasks/backlog/2026-05/2026-05-18-DESIGN-Deposit-Trigger-System-And-Equipment-Gating.md
git commit -m "documentation: record ResourceDeposit design decisions from 2026-05-19 session"
git push
```

---

## Documentation
- [x] Update `docs/new_agent/rules/DECISIONS.md` — add ResourceDeposit spawning decisions

---

## Dependencies
**Blocked by**: none
**Blocks**: `2026-05-18-IMPL-Create-DepositSpawner-Service.md` — Task 5 should not begin until these decisions are recorded
**Related tasks**: `2026-05-18-OVERVIEW-Resource-Spawning-Task-Breakdown.md`

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**:
**Completion date**:
**Final result**: All 6 decisions recorded, 4 task files updated

### What was changed
-

### Issues discovered
-

### Follow-up tasks needed
-

---

## Handoff Summary

HANDOFF SUMMARY: DECISIONS.md updated with ResourceDeposit spawn decisions | Task 5 and 6 files reference decisions | Survey confirmed as MVP trigger | Task 5 unblocked after this completes
