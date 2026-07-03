# Surgical Guardrails Audit & Documentation Refactoring
**Date**: 2026-07-01
**Researcher**: Planner Agent (Synthesis Report)
**Project**: galaxy_game
**Status**: AWAITING HUMAN APPROVAL

---

## STATUS SYNTHESIS REPORT

### 1. Scope Assessment — Section-to-Folder Mapping

The root `docs/GUARDRAILS.md` (681 lines) is a monolithic document mixing three distinct concerns:

| Concern | Lines | Category |
|---------|-------|----------|
| Agent role boundaries & golden rule | ~30 | **Agent Protocol** |
| Universal Docking & Chassis Integration | ~15 | **Game Architecture** |
| Material Loss Logic (Task 10) | ~8 | **Mission Profile / Game Design** |
| AI Manager Guardrails (sections 1-7) | ~90 | **Game Architecture + Agent Protocol** |
| Terrain Generation & Rendering (section 7.5) | ~120 | **Game Architecture** |
| Economic System Guardrails (section 8) | ~130 | **Game Architecture** |
| Sol as AI Training Data (section 9) | ~40 | **Game Architecture** |
| Player Experience Boundaries (section 10) | ~25 | **Game Design** |
| Sci-Fi Easter Eggs (section 11) | ~60 | **Game Design** |
| Environment & Container Management (sections 12-13) | ~140 | **Agent Protocol** |
| Monitor Interface & Layer System (section 14) | ~45 | **Game Architecture** |
| Sphere Creation Optimization (section 13 duplicate) | ~35 | **Game Architecture** |
| EM Power Transition & Shield Tech | ~35 | **Game Design** |
| Resource Allocation Engine Integration | ~8 | **Game Architecture** |

### 2. Implementation vs. Intent — Economic Constants Audit

#### Verified Against GLOSSARY_SYSTEM_MECHANICS.md:

| Constant | GUARDRAILS.md Value | GLOSSARY Value | Status |
|----------|---------------------|----------------|--------|
| SCC Surcharge | 0.5% | 0.5% | ✅ Aligned |
| Broker Fee | 0.3% | 0.3% | ✅ Aligned |
| Sales Tax | 3.37% | 3.37% | ✅ Aligned |

**Recommendation**: Add a cross-reference in GUARDRAILS.md pointing to GLOSSARY_SYSTEM_MECHANICS.md as the canonical fee/tax reference[cite: 5].

### 3. Proposed Action — Refactoring Plan

#### Phase 1: Audit & Verify (No Changes)
Cross-reference all game design percentages against GLOSSARY_SYSTEM_MECHANICS.md[cite: 5].

#### Phase 2: Extract Game Architecture Content
Move domain-specific content from `docs/GUARDRAILS.md` into existing architecture subdirectories (e.g., `docs/architecture/stations/`, `docs/architecture/logistics/`, etc.)[cite: 5].

#### Phase 3: Extract Game Design Content
Move player experience and world-building content to `docs/planning/` or `docs/architecture/glossary/`[cite: 5].

#### Phase 4: Trim GUARDRAILS.md to Agent Protocol Only
After extraction, the root `docs/GUARDRAILS.md` will contain ONLY agent operational rules. Expected final size: ~150 lines[cite: 5].

#### Phase 5: Create Migration Index
Add a section at the top of the trimmed `docs/GUARDRAILS.md` with links to all migrated content to prevent broken references[cite: 5].

#### Phase 6: Update agent-t/rules/GUARDRAILS.md Sync
Sync the operational rules with the `agent-tasks/rules/GUARDRAILS.md` file[cite: 5].

### 4. Deep Audit Findings
- **Issue 1**: Duplicate section numbering; Section 4 is missing; Section 13 is duplicated[cite: 5].
- **Issue 2**: Mixed concern levels (operational vs. architecture vs. design)[cite: 5].
- **Issue 3**: Economic constants are fragmented across three files; GLOSSARY_SYSTEM_MECHANICS.md must be the sole source of truth[cite: 5].
- **Issue 4**: Stale implementation notes exist that should be moved to commit history or architecture docs[cite: 5].

---

**File Destination**: /Users/tam0013/Documents/git/galaxyGame/docs/new_agent/projects/galaxy_game/tasks/proposed new tasks/2026-07-01-TASK-UPDATE-2/2026-07-01-RESEARCH-GUARDRAILS-AUDIT.md