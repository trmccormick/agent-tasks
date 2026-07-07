---
status: backlog
priority: MEDIUM
type: research
system_domain: AI_MANAGER
mvp_alignment: NONE_FUTURE_ARCHITECTURE
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Research Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/research/2026-07-07-MEDIUM-RESEARCH-PATTERN-BASED-SETTLEMENT-DECISION-LOGIC.md

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv to new folder
  - New/untracked file: move with filesystem (mv), then git add the final path
  - Never copy task files between folders
READ FIRST: Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# RESEARCH: Pattern-Based Settlement Decision Logic Architecture

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: research
**Created**: 2026-07-07 — Planning Agent (Qwen3.6-35b)
**Origin**: Supersedes `deprecated/2026-02-15-HIGH-FEATURE-IMPLEMENT-SETTLEMENT-PATTERN-LOGIC.md`

---

## Context

The original task (`2026-02-15`) proposed creating a standalone `SettlementPattern` model and `PatternSerializer` service to cache successful base layouts as portable JSON templates. This approach was confirmed obsolete in the 2026-06-25 handoff — `SettlementPattern` never existed and the architecture has evolved.

**The underlying need is still valid**: reduce continuous geospatial map calculations by enabling the AI Manager to remember high-yielding settlement configurations and apply learned patterns to future decisions. This research task explores how to realize this need using the **current architecture** rather than a standalone model.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (RESEARCHER Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/README.md`
3. **Phase Structure**: `docs/new_agent/projects/galaxy_game/tasks/backlog/PHASE_STRUCTURE.md` — pattern learning is Act 2+ (Phase 14+) concept, not MVP-scoped
4. **This Task File**: Everything below

---

## Research Questions

### 1. Current Architecture Alignment
**What to investigate**: How does the current `tasks_v2` library + `StrategySelector`/`StateAnalyzer` handle settlement pattern learning?

- Audit `AIManager::StrategySelector` — how does it score settlement options today?
- Audit `AIManager::StateAnalyzer` — what state data is available for pattern matching?
- Check if `tasks_v2` already supports pattern-based task selection (vs. scoring)
- Review `WorldhouseLearning` service (`phase6+/2026-06-22-MEDIUM-FEATURE-WORLDHOUSE-AI-LEARNING-INTEGRATION.md`) — does it cover the general case or only worldhouses?

**Success criteria**: Clear map of what pattern learning exists today vs. what's missing.

### 2. Data Source for Pattern Capture
**What to investigate**: What data structures represent "successful base layouts" in the current codebase?

- `CelestialBody#geosphere.terrain_map` — StarSim-driven, world-generic terrain data
- `AIManager::Manager#advance_time` — how does it track successful deployments?
- `SystemOrchestrator` / `ServiceOrchestrator` — do they maintain deployment history?
- Any existing "golden pattern" or "best practice" tracking in the codebase?

**Success criteria**: Identify concrete data sources that could serve as pattern capture input.

### 3. Pattern Storage Architecture
**What to investigate**: How should learned patterns be stored and retrieved?

- Option A: Extend `tasks_v2` library with pattern metadata fields
- Option B: Create a lightweight `SettlementPattern` model (revisit the original approach)
- Option C: Store patterns as JSON in `data/json-data/missions/patterns/` directory
- Option D: Use existing AI Manager state persistence mechanism

**Success criteria**: Recommend one architecture with justification based on current codebase conventions.

### 4. Pattern Application Mechanism
**What to investigate**: How should learned patterns influence future settlement decisions?

- Should patterns override `StrategySelector` scoring?
- Should patterns be advisory (hints) or mandatory (constraints)?
- How does terrain compatibility validation work today (`SurfaceSuitabilityAnalyzer`)?
- What's the data contract between pattern storage and AI Manager decision logic?

**Success criteria**: Clear mechanism for how patterns flow from capture → storage → application.

---

## Architecture Gotchas

⚠️ **GOTCHA 1**: `SettlementPattern` was confirmed obsolete in the 2026-06-25 handoff
- ❌ Wrong: Assume a standalone `SettlementPattern` model is the right approach
- ✅ Right: Evaluate whether pattern learning should live within existing AI Manager services or as a separate concern
- Why: The handoff explicitly noted "pattern-based settlement decision logic is being realized through `tasks_v2` + `StrategySelector`/`StateAnalyzer`, not a standalone `SettlementPattern` model"

⚠️ **GOTCHA 2**: This is Act 2+ (Phase 14+) concept, not MVP-scoped
- ❌ Wrong: Design for immediate implementation during Phase 5-6
- ✅ Right: Research should focus on architecture options and data contracts, not implementation
- Why: Players don't enter until post-Snap; pattern learning is relevant when AI Manager operates across multiple worlds/systems

⚠️ **GOTCHA 3**: Worldhouse Learning Integration (`phase6+/`) may already cover this
- ❌ Wrong: Assume no existing pattern learning work exists
- ✅ Right: Audit `WorldhouseLearning` service to determine if it generalizes beyond worldhouses
- Why: If Worldhouse Learning covers the general case, this research task may be redundant

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

```
## STATUS SYNTHESIS REPORT

**Task**: Research Pattern-Based Settlement Decision Logic Architecture
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Audit current AI Manager architecture (StrategySelector, StateAnalyzer, tasks_v2, WorldhouseLearning)
to map existing pattern learning capabilities. Identify gaps between current state and the need for
cross-world settlement pattern capture/application. Recommend architecture options.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/ai_manager/strategy_selector.rb` | Audit scoring mechanism | not started |
| `app/services/ai_manager/state_analyzer.rb` | Audit state data available | pending |
| `data/json-data/missions/tasks_v2/` | Check for pattern metadata support | pending |
| `app/services/ai_manager/worldhouse_learning.rb` | Check if generalizes beyond worldhouses | pending |
| `app/models/structures/worldhouse.rb` | Understand existing structure model | pending |

### Prerequisites Completed
- ✅ Read README.md RESEARCHER section
- ✅ Read project guide
- ✅ Read phase structure (Act 2+ concept, not MVP-scoped)
- ✅ Understand architecture gotchas above

### Expected Outcomes
- Clear map of existing pattern learning in current architecture
- Identified gaps between current state and cross-world pattern needs
- Recommended architecture option with justification
- Data contract proposal for pattern capture → storage → application flow

### Critical Gotchas I Will Avoid
- ❌ Designing standalone SettlementPattern model — instead ✅ Evaluate within existing AI Manager services
- ❌ Designing for MVP implementation — instead ✅ Focus on architecture options for Phase 14+
- ❌ Assuming Worldhouse Learning is narrow — instead ✅ Audit it first for generalization

---

**SYNTHESIS COMPLETE.** Ready to proceed with audit.
```

---

## Research Output

Save findings to: `/Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/summaries/`

**Create**: `2026-XX-XX-RESEARCH-PATTERN-BASED-SETTLEMENT-DECISION-LOGIC.md`

---

## Acceptance Criteria
- [ ] Audit of StrategySelector scoring mechanism documented
- [ ] Audit of StateAnalyzer state data documented
- [ ] Assessment of tasks_v2 pattern metadata support
- [ ] Assessment of Worldhouse Learning generalization scope
- [ ] Architecture recommendation with justification
- [ ] Data contract proposal for pattern flow
- [ ] Phase alignment assessment (MVP vs Phase 6+ vs Phase 14+)

---

## Stop Conditions — escalate to user immediately if:
- Current architecture already fully covers the need (research task becomes redundant)
- Any architectural decision is required before research can continue
- Worldhouse Learning is confirmed to cover the general case (task may be superseded)

---

## Dependencies

**Blocked by**: None — this task is ready to dispatch immediately
**Blocks**: Future implementation of pattern-based settlement logic (Phase 14+)
**Related tasks**: `deprecated/2026-02-15-HIGH-FEATURE-IMPLEMENT-SETTLEMENT-PATTERN-LOGIC.md` (superseded original), `phase6+/2026-06-22-MEDIUM-FEATURE-WORLDHOUSE-AI-LEARNING-INTEGRATION.md` (Worldhouse Learning)

---

## Completion Report
*Filled in by the implementing agent after completion*
