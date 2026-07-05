---
status: completed
priority: HIGH
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
cross_project_impact: true
---

# TASK: GUARDRAILS.md Consolidation — Four-Way Sort & Unified Task File

**Status**: COMPLETED ✅
**Priority**: HIGH
**Type**: documentation
**Created**: 2026-07-02
**Last Updated**: 2026-07-03
**Completed**: 2026-07-03
**Author**: STRATEGIST (session strategy)
**Moved to active**: 2026-07-03 by Implementation Agent
**Audit & Remediation**: Completed 2026-07-03 by Claude (Strategist Agent)

**⚠️ CROSS-PROJECT IMPACT**: This task edits `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md`, the authoritative generic agent rules file read by every project (Galaxy Game, Samvera, WVU Libraries), not just this one. Review this task with that in mind — a bad edit here affects agent behavior everywhere, not just galaxyGame.

---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/2026-07-02-HIGH-DOCUMENTATION-GUARDRAILS-CONSOLIDATION.md

READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Create STATUS SYNTHESIS REPORT in chat BEFORE starting any work (template in task file).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

## Supersedes These Prior Task Files

This consolidated task supersedes the prior GUARDRAILS task files listed below. Move to
`tasks/deprecated/` after this task is approved (not after execution — the originals are
deprecated once their replacement plan exists and is approved, per `TASK_UPDATE_GUIDE.md`).

### Original Stale Tasks Under Audit (review/) — the actual source material
1. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/review/TASK_GUARDRAILS_SPLIT.md`
2. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/review/TASK_WEDNESDAY_SURGICAL_AUDIT.md`

### Refactored-Task-Files (2026-07-01 research batch) — superseded drafts
3. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/refactored-task-files/2026-07-01-TASK-UPDATE-2/2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md`
4. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/refactored-task-files/2026-07-01-TASK-UPDATE-2/GUARDRAILS_REF_PACKAGE.md`
5. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/refactored-task-files/2026-07-01-TASK-UPDATE-2/TASK_REF_GUARDRAILS_MIGRATION_V2.md`
6. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/refactored-task-files/2026-07-01-TASK-UPDATE-2/2026-07-01-PRE-WORK-CREATE-DIRECTORIES.md`
7. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/refactored-task-files/2026-07-01-TASK-UPDATE-2/2026-07-01-PRE-WORK-RESOLVE-AMBIGUITIES.md`

### Working-Temp (ephemeral handoff files) — delete, do not archive
Their findings are already captured in this consolidated task. Per the summaries-folder
lifecycle, these have no further purpose once a task is approved.
8. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/refactored-task-files/_working-temp/2026-07-01-TASK-GUARDRAILS-VALIDATION-QWEN.md`
9. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/refactored-task-files/_working-temp/GEMINI_HANDOFF_GUARDRAILS_RECONCILIATION.md`
10. `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/refactored-task-files/_working-temp/QWEN_HANDOFF_GUARDRAILS_VALIDATION.md`

---

## Explicitly Excluded From This List (Do Not Archive or Delete as Part of This Task)

- **`DISPATCH_GUARDRAILS_CONSOLIDATION_RYZEN.md`** — this is a dispatch instructions
  file generated while developing/testing the stale-task-audit workflow itself, not a
  task file, and was never actually used (Ryzen was dispatched through a different
  session). Not part of the superseded list. Leave alone or clean up separately as
  workflow-development scratch material — not in scope for this task.
- **`tasks/backlog/reorganization attempt 2/` (all files, Feb–Apr 2026)** — human has
  assessed this folder as likely invalid/stale data from an earlier abandoned
  reorganization effort. **Not included in this task's scope.** Leave untouched. If this
  folder needs cleanup, it should be its own separately-scoped task, not folded into this
  one — those files predate the four-way sort decided here and haven't been individually
  verified against it.

---

## Context

The root `docs/GUARDRAILS.md` (680 lines) is a monolithic document conflating four distinct concerns:
1. **Generic agent operational rules** — Docker, RSpec, database migrations, token conservation
2. **Galaxy Game–specific process/agent rules** — project-specific guardrails not applicable to other projects
3. **Game design / system intent content** — EM power transition, shield tiers, terrain generation, economics
4. **Historical notes** — time-bound bug fixes, implementation precedents

The authoritative generic agent rules file is `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` (425 lines, as of 2026-07-02 — re-verify before merging, see Phase 1). This task produces the plan to sort galaxyGame's GUARDRAILS.md into four destinations without data loss.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **Generic Agent Rules**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` (authoritative, do not edit directly — merge gaps into it)
4. **Source File**: `/Users/tam0013/Documents/git/galaxyGame/docs/GUARDRAILS.md` (680 lines, being audited)
5. **This Task File**: Everything below

---

## Four-Way Sort — Settled Decisions (Human-Confirmed 2026-07-01/02)

### Destination 1: DUPLICATES → Discard from docs/GUARDRAILS.md
Generic agent rules already covered in `agent-tasks/rules/GUARDRAILS.md`. These lines get removed from galaxyGame's GUARDRAILS.md. No new files created.

### Destination 2: GAPS → Merge into agent-tasks/rules/GUARDRAILS.md
Generic rules genuinely MISSING from `agent-tasks/rules/GUARDRAILS.md`. These are real gaps that need to be merged in. **Executor must re-verify the current content of agent-tasks/rules/GUARDRAILS.md before merging** — line count alone doesn't confirm content hasn't been reorganized. **This is the cross-project-impact destination — treat merges here with extra care and present for explicit approval before writing.**

### Destination 3: GAME-SPECIFIC PROCESS RULES → New project-specific file
Galaxy Game–specific process/agent rules (not generic, not game design) go to a new file under `/Users/tam0013/Documents/git/galaxyGame/docs/`, **NOT named "GUARDRAILS"** — to avoid recreating the exact multi-file confusion this task exists to resolve. Name TBD during execution (e.g. `GALAXY_GAME_AGENT_RULES.md` or similar).

### Destination 4: GAME DESIGN CONTENT → galaxyGame docs/ subdirectories
Game design / system intent content goes to the appropriate galaxyGame docs/ subdirectory per this mapping:

| Content Type | Target Directory |
|---|---|
| EM Power Transition & Shield Tech | `docs/architecture/systems/em_power_shield_tiers.md` |
| Terrain Generation & Rendering | `docs/architecture/terrain/` (create if needed) |
| Economic System Guardrails | `docs/architecture/economy/` (verify exists) |
| AI Manager Guardrails (sections 1-7) | `docs/architecture/ai_manager/` (28 existing docs — merge carefully) |
| Player Experience Boundaries | `docs/gameplay/` |
| Sci-Fi Easter Eggs | `docs/flavor/` |
| Monitor Interface & Layer System | `docs/systems/` or `docs/architecture/systems/` (verify which exists) |
| Sphere Creation Optimization | `docs/architecture/systems/` |
| Resource Allocation Engine Integration | `docs/architecture/ai_manager/` |

---

## Open Item Resolved: em_power_shield_tiers.md Path

**Decision**: `/Users/tam0013/Documents/git/galaxyGame/docs/architecture/systems/em_power_shield_tiers.md`

**Evidence:**
- The GUARDRAILS.md section (~35 lines, lines 648-681) covers: energy system progression (Fusion→EM→Wormhole), shield technology tiers (Tier 1-3), upgrade events/vulnerabilities, security/market dynamics. This is **gameplay systems** content — not station architecture or building structures.
- `docs/architecture/systems/` EXISTS with 14 files including `em_technology_tree.md` (directly related EM tech content).
- `docs/architecture/stations/` has 10 files focused on specific station designs (CERES_GATEWAY, asteroid_relocation_tug, etc.) — these are station blueprints, not system mechanics.
- `docs/architecture/structures/` exists but contains only README.md — too sparse, and "structures" implies physical buildings, not technology systems.
- The content fits naturally alongside `em_technology_tree.md` in `docs/architecture/systems/`.

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Do NOT edit agent-tasks/rules/GUARDRAILS.md directly**
- ❌ Wrong: Open the file and start editing it
- ✅ Right: Produce a merge plan in the task execution report, present to human for approval, then execute
- Why: This is the authoritative generic rules file across all projects — Galaxy Game, Samvera, WVU Libraries. Changes must be deliberate and reviewed. See cross-project impact note at top of this file.

⚠️ **GOTCHA 2: Do NOT edit galaxyGame/docs/GUARDRAILS.md directly**
- ❌ Wrong: Start trimming lines from the source file
- ✅ Right: Produce a migration index mapping every section to its destination, present to human for approval
- Why: 680-line monolith with mixed concerns. Trimming without a plan risks data loss.

⚠️ **GOTCHA 3: Content conflicts in target directories**
- `docs/architecture/economy/` may already contain FISCAL_POLICY_AND_FEES.md with economic constants
- `docs/architecture/ai_manager/` has 28 existing docs — migrating sections 1-7 (~90 lines) could overlap
- **Action**: Executor must audit target directories before creating new files. Merge into existing content where appropriate.

⚠️ **GOTCHA 4: Market vs. Build logic**
- Core game-design rationale about sourcing strategy is embedded in code (`galaxy_game/app/models/structures/converted_base.rb` line 20)
- The GUARDRAILS.md references this concept — the rationale must be preserved, not deleted
- **Action**: Extract to `docs/architecture/economy/market_vs_build_logic.md` or similar

⚠️ **GOTCHA 5: Scope discipline on superseded files**
- Do NOT include `DISPATCH_GUARDRAILS_CONSOLIDATION_RYZEN.md` or anything from
  `tasks/backlog/reorganization attempt 2/` when archiving/deprecating superseded files —
  see "Explicitly Excluded" section above. If uncertain whether a file belongs in the
  superseded list, stop and ask rather than including it.

---

## Complete Migration Index (Produced by STRATEGIST, 2026-07-03)

This index maps every section of docs/GUARDRAILS.md to its destination. Line numbers are approximate based on the 680-line file as of 2026-07-03 — executor should verify with `grep -n` before executing.

### Destination 1: DUPLICATES → Remove from docs/GUARDRAILS.md (no new files)
Generic agent rules already covered in agent-tasks/rules/GUARDRAILS.md.

| Lines | Section | Corresponding agent-tasks Rule(s) | Action |
|---|---|---|---|
| 6-35 | Planner Agent role definition | Rules 0, 13, 13a (roles defined by tool access) | Remove entire section |
| 22-35 | Executor Agent role definition | Rules 0, 13, 13a (roles defined by tool access) | Remove entire section |
| 36-42 | Golden Rule (host vs Docker) | Rule 1 + Rule 10 | Remove — covered by both rules |
| 44-54 | Universal Docking & Chassis Integration | Not a generic rule → see Destination 3 | **NOT a duplicate** |
| 55-65 | Material Loss Logic for Interplanetary Transit | Not a generic rule → see Destination 3 | **NOT a duplicate** |
| 443-517 | Environment & Container Management (Section 12) | Rule 1 (Docker), Rule 2 (migrations), Rule 3 (RSpec), Rule 10 (paths) | Remove entire section — verbose but covered by Rules 1-3, 10 |
| 518-577 | Database Environment Protection (Section 13) | Rule 2 + Rule 3 + **2 gaps** (see Destination 2) | Remove most; extract 2 gaps to Destination 2 |

### Destination 2: GAPS → Merge into agent-tasks/rules/GUARDRAILS.md
Generic rules genuinely MISSING from agent-tasks/rules/GUARDRAILS.md. **These two gaps require explicit human review before writing to agent-tasks/rules/GUARDRAILS.md.**

#### Gap A: RAILS_ENV=test + DATABASE_URL unset requirement (from Section 13, lines 518-577)
**What to add:** Mandatory `unset DATABASE_URL && RAILS_ENV=test` prefix for ALL test commands.

**Where in agent-tasks:** Enhancement to Rule 2 and Rule 3:
- Add to Rule 2 "After generating" block: `docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test rails db:migrate'`
- Add to Rule 3 "Correct form" example: include `unset DATABASE_URL && RAILS_ENV=test` in all examples
- Add a new sub-bullet under Rule 3a: "DATABASE_URL must be unset and RAILS_ENV=test set for ALL test commands — this prevents dev database corruption"

**Why it's a gap:** agent-tasks/rules/GUARDRAILS.md mentions `RAILS_ENV=test` in examples but does NOT have an explicit rule stating `unset DATABASE_URL` is mandatory. The galaxyGame version has this as a critical safety requirement with incident precedent (2026-01-24).

#### Gap B: Container lifecycle prohibition (from Section 12, lines 477-491)
**What to add:** "Containers always running" clause with explicit forbidden commands list.

**Where in agent-tasks:** Enhancement to Rule 1 Docker section — add as a new sub-heading after the current examples:

```markdown
### Containers Are Always Running
The full stack (web, db, and all services) is assumed to be running at all times during a development session.
- NEVER run `docker-compose up`, `docker-compose restart`, `docker-compose stop`, `docker-compose down`, or `docker-compose build` without explicit user instruction
- NEVER check container state as a precursor to starting them — they are already running
- If a command fails due to a container issue, report it to the user — do not attempt to restart anything
```

**Why it's a gap:** agent-tasks/rules/GUARDRAILS.md Rule 1 says "use `docker exec -it web`" but does NOT explicitly prohibit starting/stopping containers. The galaxyGame version has this as a critical rule with incident precedent (2026-01-23).

### Destination 3: GAME-SPECIFIC PROCESS RULES → New project-specific file
Galaxy Game–specific process/agent rules (not generic, not game design).

| Lines | Section | Proposed Filename | Content Summary |
|---|---|---|---|
| 44-54 | Universal Docking & Chassis Integration | `docs/galaxy_game_agent_rules.md` | Project-specific integration rules (docking handshake, hitchhiker state, life support sharing) |
| 55-65 | Material Loss Logic for Interplanetary Transit | `docs/galaxy_game_agent_rules.md` | Project-specific transit loss rates (5-10%) and ROI calculation rules |

**Proposed filename:** `docs/galaxy_game_agent_rules.md` (not "GUARDRAILS" — avoids recreating the multi-file confusion this task exists to resolve)

### Destination 4: GAME DESIGN CONTENT → galaxyGame docs/ subdirectories

#### Section-by-section mapping with merge strategies for conflict zones:

| Lines | Section | Target File/Directory | Merge Strategy |
|---|---|---|---|
| 66-69 | AI Manager §1: Code & Documentation Sync | `docs/architecture/ai_manager/AI_MANAGER_CODE_REVIEW_PROTOCOL.md` — **APPEND** as "Guardrails Integration" subsection. Content overlaps with review criteria but adds the "no doc update = no logic complete" mandate. |
| 70-79 | AI Manager §2: Anchor Law (Stability & Infrastructure) | `docs/architecture/ai_manager/AI_MANAGER_WORMHOLE_EXPANSION.md` — **APPEND** as "Anchor Law Constraints" subsection. Wormhole stability rules are directly relevant to expansion logic. |
| 80-92 | AI Manager §3: Market & GCC Integrity | `docs/architecture/economy/` — **MERGE into existing docs**. Contract ceiling limits → append to MARKET_OPERATIONS.md. GCC/Virtual Ledger rules → append to CURRENCY_AND_EXCHANGE.md. **HIGH CONFLICT** — read both files before merging. |
| 93-124 | AI Manager §5: Operational Boundaries + Namespace Rules | `docs/architecture/ai_manager/AI_MANAGER_ARCHITECTURE.md` — **APPEND** as "Operational Constraints" subsection. Namespace integrity rules are architectural constraints relevant to the architecture doc. |
| 125-132 | AI Manager §7: Path Configuration Standards | `docs/architecture/ai_manager/00_architecture_overview.md` — **APPEND** as "Path Configuration" subsection. Centralized path management is an architectural concern. |
| 133-286 | Section 7.5: Terrain Generation & Rendering Architecture | `docs/architecture/terrain/generation_and_rendering.md` — **NEW FILE**. Directory does not exist — create first. This is a complete new document (~154 lines). |
| 287-354 | Section 8: Economic System Guardrails | `docs/architecture/economy/` — **MERGE into existing docs**. Contract limits → MARKET_OPERATIONS.md. Debt controls → CURRENCY_AND_EXCHANGE.md. Reserve requirements → FISCAL_POLICY_AND_FEES.md. Market dynamics → MARKET_OPERATIONS.md. Earth-Luna Anchor → append to MARKET_OPERATIONS.md as "Earth Anchor Price" section. AI Manager Treasury → AI_MANAGER_ARCHITECTURE.md. **HIGH CONFLICT** — read all 12 economy files before merging. |
| 355-378 | Section 9: Sol as AI Training Data | `docs/architecture/ai_manager/AI_MANAGER_ARCHITECTURE.md` — **APPEND** as "Sol Training Data" subsection. Training data patterns are AI Manager domain. |
| 379-395 | Section 10: Player Experience Boundaries | `docs/gameplay/player_experience_boundaries.md` — **NEW FILE**. Gameplay design content, no existing file to merge with. |
| 396-442 | Section 11: Sci-Fi Easter Eggs | `docs/flavor/sci_fi_easter_eggs.md` — **NEW FILE**. Directory does not exist — create first. Complete new document (~47 lines). |
| 578-612 | Section 14: Monitor Interface & Layer System Guardrails | `docs/architecture/systems/monitor_interface_layers.md` — **NEW FILE** in systems directory. Systems architecture content, fits alongside em_technology_tree.md and sphere_creation_optimization.md. |
| 613-647 | Section 13 (duplicate): Sphere Creation Optimization Plan | `docs/architecture/systems/sphere_creation_optimization.md` — **NEW FILE** in systems directory. Systems architecture content, fits alongside em_technology_tree.md. |
| 648-675 | EM Power Transition & Shield Technology Evolution | `docs/architecture/systems/em_power_shield_tiers.md` — **NEW FILE**. Resolved path — fits naturally alongside em_technology_tree.md. |
| 676-end | Resource Allocation Engine Integration | `docs/architecture/ai_manager/AI_MANAGER_ARCHITECTURE.md` — **APPEND** as "Resource Allocation" subsection. AI Manager domain. |

---

## Detailed Merge Strategies for HIGH CONFLICT Zones

### Economy Directory (12 files) — Section-by-Section Merge Plan

| GUARDRAILS Section | Target File | Merge Strategy |
|---|---|---|
| §3 Contract Ceiling Limits (lines 85-92) | MARKET_OPERATIONS.md | Append as "Contract Ceiling Limits" subsection — overlaps with EAP section but adds player-first contract rules |
| §3 GCC/Virtual Ledger (lines 80-84) | CURRENCY_AND_EXCHANGE.md | Append as "Virtual Ledger Protocols" subsection — overlaps slightly with existing Virtual Ledger content but adds NPC-specific rules |
| §8 Contract Limits (lines 289-293) | MARKET_OPERATIONS.md | Merge into existing EAP section — adds daily/maximum contract limits |
| §8 Debt Controls (lines 294-298) | CURRENCY_AND_EXCHANGE.md | Append as "Debt and Overdraft Controls" subsection |
| §8 Reserve Requirements (lines 299-303) | FISCAL_POLICY_AND_FEES.md | Append as "Reserve Requirements" subsection |
| §8 Currency Stability (lines 304-308) | CURRENCY_AND_EXCHANGE.md | Merge with existing peg/exchange content — adds stability measures |
| §8 Market Dynamics (lines 309-336) | MARKET_OPERATIONS.md | Append as "Market Dynamics" subsection — largely new content, minimal overlap |
| §8 NPC Debt Influence (lines 337-343) | CURRENCY_AND_EXCHANGE.md | Append as "NPC Debt Decision Influence" subsection |
| §8 DC Structure (lines 344-348) | FISCAL_POLICY_AND_FEES.md | Append as "Development Corporation Structure" subsection |
| §8 Earth-Luna Anchor (lines 349-354) | MARKET_OPERATIONS.md | Merge into EAP section — directly relevant to Earth Anchor Price |
| §8 AI Manager Treasury (lines 349-354) | AI_MANAGER_ARCHITECTURE.md | Append as "GCC Treasury Management" subsection |

**Pre-merge action:** Read all 12 economy files before merging. FISCAL_POLICY_AND_FEES.md already has fee tables that may overlap with §8 content. MARKET_OPERATIONS.md already has EAP and Market vs. Build logic — merge carefully to avoid duplication.

### AI Manager Directory (28+ files) — Section-by-Section Merge Plan

| GUARDRAILS Section | Target File | Merge Strategy |
|---|---|---|
| §1 Code & Documentation Sync (lines 66-69) | AI_MANAGER_CODE_REVIEW_PROTOCOL.md | Append as "Guardrails Integration" subsection — adds "no doc update = no logic complete" mandate |
| §2 Anchor Law (lines 70-79) | AI_MANAGER_WORMHOLE_EXPANSION.md | Append as "Anchor Law Constraints" subsection — directly relevant to expansion logic |
| §5 Operational Boundaries (lines 93-124) | AI_MANAGER_ARCHITECTURE.md | Append as "Operational Constraints" subsection — namespace integrity rules are architectural constraints |
| §7 Path Configuration (lines 125-132) | 00_architecture_overview.md | Append as "Path Configuration Standards" subsection — centralized path management is architectural |
| §9 Sol Training Data (lines 355-378) | AI_MANAGER_ARCHITECTURE.md | Append as "Sol Training Data" subsection — training patterns are core architecture |
| Resource Allocation Engine (lines 676-end) | AI_MANAGER_ARCHITECTURE.md | Append as "Resource Allocation Engine Integration" subsection |

**Pre-merge action:** Read AI_MANAGER_CODE_REVIEW_PROTOCOL.md, AI_MANAGER_WORMHOLE_EXPANSION.md, and AI_MANAGER_ARCHITECTURE.md before merging. These files have their own structure — append as clearly labeled subsections to avoid confusion.

---

## Acceptance Criteria

- [x] **Migration Index Produced**: Every section of docs/GUARDRAILS.md mapped to one of four destinations with exact line ranges (verified via `grep -n` on 2026-07-03)
- [x] **Gap Analysis Complete**: agent-tasks/rules/GUARDRAILS.md re-verified (425 lines, no drift) — confirmed only 2 genuine gaps vs. already covered content
- [x] **em_power_shield_tiers.md Path Resolved**: Destination confirmed as `docs/architecture/systems/em_power_shield_tiers.md` with reasoning documented
- [x] **Target Directories Verified**: All destination directories exist or are created; content conflicts identified and merge strategies produced
- [x] **Consolidated Plan Ready**: One unified execution plan ready for executor handoff — no ambiguity remaining
- [x] **Prior Files Correctly Scoped**: Superseded list matches the corrected 10-file list above — excludes the dispatch file and `reorganization attempt 2/`
- [x] **Human Approval Received**: Approved by human on 2026-07-03
- [x] **Directories Created**: `docs/architecture/terrain/`, `docs/flavor/`
- [x] **New Files Created (7)**: galaxy_game_agent_rules.md, generation_and_rendering.md, player_experience_boundaries.md, sci_fi_easter_eggs.md, em_power_shield_tiers.md, sphere_creation_optimization.md, monitor_interface_layers.md
- [x] **Economy Merges Complete**: MARKET_OPERATIONS.md, CURRENCY_AND_EXCHANGE.md, FISCAL_POLICY_AND_FEES.md updated
- [x] **AI Manager Merges Complete**: AI_MANAGER_CODE_REVIEW_PROTOCOL.md, AI_MANAGER_WORMHOLE_EXPANSION.md, AI_MANAGER_ARCHITECTURE.md, 00_architecture_overview.md updated
- [x] **docs/GUARDRAILS.md Trimmed**: 680 lines → 73 lines (migration index only)
- [ ] **Gap A Approved & Merged**: `unset DATABASE_URL && RAILS_ENV=test` mandatory prefix — AWAITING HUMAN APPROVAL
- [ ] **Gap B Approved & Merged**: Container lifecycle prohibition clause — AWAITING HUMAN APPROVAL

---

## Execution Readiness — APPROVED BY HUMAN (2026-07-03)

**Status:** Migration index fully accepted. Ready for executor handoff.

**What the executor will do upon receiving this approved task:**

1. **Create directories:** `docs/architecture/terrain/` and `docs/flavor/`
2. **Create new files (5 total):**
   - `docs/galaxy_game_agent_rules.md` — Destination 3 content
   - `docs/architecture/terrain/generation_and_rendering.md` — Section 7.5 terrain (~154 lines)
   - `docs/gameplay/player_experience_boundaries.md` — Section 10 player experience
   - `docs/flavor/sci_fi_easter_eggs.md` — Section 11 Easter eggs (~47 lines)
   - `docs/architecture/systems/em_power_shield_tiers.md` — EM power/shield tech
   - `docs/architecture/systems/sphere_creation_optimization.md` — Sphere optimization
   - `docs/architecture/systems/monitor_interface_layers.md` — Monitor interface
3. **Merge into existing files (economy/ — 12 files):** Careful append/merge per detailed merge plan above
4. **Merge into existing files (ai_manager/ — 28+ files):** Careful append per detailed merge plan above
5. **Trim docs/GUARDRAILS.md:** Remove duplicate sections, extract 2 gaps for agent-tasks/rules/GUARDRAILS.md
6. **Present gap merges to human** for explicit approval before writing to agent-tasks/rules/GUARDRAILS.md (cross-project impact)

**⚠️ CRITICAL:** Do NOT edit either GUARDRAILS file until the migration index is fully accepted. This task file IS the accepted plan. The executor should present the diff of all changes for final approval before committing.

---

## Execution Steps (For Executor)

### Phase 1: Re-verify agent-tasks/rules/GUARDRAILS.md
1. Run `wc -l /Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` to confirm current line count
2. Read the full file — compare section-by-section against galaxyGame's GUARDRAILS.md generic rules sections
3. Identify genuine gaps (category 2) vs. already-covered content (category 1)
4. Document findings in execution report

### Phase 2: Audit target directories for conflicts
1. `ls -la /Users/tam0013/Documents/git/galaxyGame/docs/architecture/economy/` — check for FISCAL_POLICY_AND_FEES.md
2. `ls -la /Users/tam0013/Documents/git/galaxyGame/docs/architecture/ai_manager/` — verify 28 existing docs
3. `ls -la /Users/tam0013/Documents/git/galaxyGame/docs/architecture/systems/` — confirm em_technology_tree.md exists
4. Check each destination directory listed in the four-way sort table above

### Phase 3: Produce migration index
1. Map every section of docs/GUARDRAILS.md (with line numbers) to its destination
2. For duplicates (category 1): list lines to remove, no new files
3. For gaps (category 2): list content to merge into agent-tasks/rules/GUARDRAILS.md with exact diff
4. For game-specific process rules (category 3): propose filename and content for new project-specific file
5. For game design content (category 4): map each section to its target directory/file

### Phase 4: Present consolidated plan
1. Present migration index + gap analysis + conflict report to human
2. Wait for approval before any edits
3. Upon approval, execute in order: create directories → extract content → trim source → merge gaps

---

## Status Synthesis Report Template (Executor Must Complete Before Starting)

```
## STATUS SYNTHESIS REPORT

**Task**: GUARDRAILS.md Consolidation — Four-Way Sort
**Status**: backlog → active
**Date**: 2026-07-02

### What I Understand
[2-3 sentences: the goal, the four-way sort, what's settled vs. what I need to verify]

### What I Need to Verify (Not Settled)
- [ ] agent-tasks/rules/GUARDRAILS.md current line count and content (may have drifted since 2026-07-01)
- [ ] Target directory existence for each destination
- [ ] Content conflicts in economy/ and ai_manager/ directories

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| docs/GUARDRAILS.md | Source being audited (680 lines) | Pending audit |
| agent-tasks/rules/GUARDRAILS.md | Authoritative generic rules — re-verify | Pending re-verify |
| docs/architecture/systems/ | Target for em_power_shield_tiers.md | Pending check |
| [others as needed] | [description] | Pending check |

### Prerequisites Completed
- ✅ Read this task file
- ✅ Understand four-way sort (settled)
- ✅ em_power_shield path resolved (settled)
- ✅ Understand which prior files are in scope vs. explicitly excluded
- ⏳ Re-verify agent-tasks/rules/GUARDRAILS.md (in progress)
- ⏳ Audit target directories (in progress)
```

---

## Notes for Executor

- **Do NOT trim or edit either GUARDRAILS.md file yet** — this task produces the plan, not the execution. However, if you are the executor receiving handoff from this task file, proceed with Phases 1-4 above.
- **Historical notes**: Time-bound bug fixes (e.g., "Sabatier Bug Fix [2026-01-15]") should be preserved in their target architecture docs as historical context, not deleted.
- **Economic constants**: SCC Surcharge (0.5%), Broker Fee (0.3%), Sales Tax (3.37%) — verified aligned with GLOSSARY_SYSTEM_MECHANICS.md. Add cross-reference in trimmed GUARDRAILS.md pointing to GLOSSARY_SYSTEM_MECHANICS.md as canonical source.
- **Formatting debt**: docs/GUARDRAILS.md has duplicate section numbering (Section 13 appears twice, Section 4 missing). Note this in the migration index but do not fix during this task — it's cosmetic and doesn't affect content routing.
- **This task file itself was produced while testing/developing the stale-task-audit
  workflow** — some of the files referenced elsewhere in that development process
  (dispatch templates, workflow docs) are development scratch material, not part of
  Galaxy Game's task backlog. See "Explicitly Excluded" section — don't assume every
  GUARDRAILS-named file encountered belongs in this task's scope.
