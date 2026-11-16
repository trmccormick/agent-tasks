# Asset/UI Roadmap — Current-State Audit

**Date**: 2026-10-06  
**Type**: AUDIT  
**Author**: Local audit (no agent dispatch)  
**Scope**: All asset-ui tasks in agent-tasks repo + VISUAL_CONTRACT.md canonical reference

---

## 1. Task Inventory by Location

### Completed
| Task | File Path | YAML Status | Notes |
|------|-----------|-------------|-------|
| **A1** | `completed/2026-08/...A1-asset-registry-reality-check.md` | `active` (see §7) | Has synthesis report at `summaries/2026-09-03-RESEARCH-ASSET-REGISTRY-REALITY-CHECK.md`. Task body says "Task A1 is closed." |

### Active
| Task | File Path | YAML Status | Notes |
|------|-----------|-------------|-------|
| **Asset-Generation Standalone** | `active/asset-ui/...ASSET-GENERATION-STANDALONE-EXECUTION.md` | `active` | Orphaned — no backlog source file exists. Dispatch interface references a non-existent backlog path. No dependency links to A/B/C/D chain. |

### Backlog (all in `backlog/asset-ui/`)
| Task | File Path | YAML Status | Type | Blocked By |
|------|-----------|-------------|------|------------|
| **A2** | `...A2-static-asset-storage-research.md` | `backlog` | research | — (prerequisite) |
| **A3** | `...A3-rh-400-asset-family-mapping.md` | `backlog` | research | — (prerequisite) |
| **A4** | `...A4-catalog-data-retrieval-research.md` | `backlog` | research | — (prerequisite) |
| **A5** | `...A5-surface-sprite-consumption-research.md` | `backlog` | research | — (prerequisite) |
| **A6** | `...A6-icon-bible-dependency-research.md` | `backlog` | research | — (prerequisite) |
| **B1** | `...B1-map-asset-registry-to-visual-definition.md` | `backlog` | architecture | A1 findings + A2-A4-A6 evidence |
| **B2** | `...B2-catalog-presentation-contract.md` | `backlog` | architecture | A2, A3, A4, A6 findings |
| **B3** | `...B3-surface-asset-representation-contract.md` | `backlog` | architecture | A3, A5 findings |
| **C1** | `...C1-implement-asset-registry-mapping.md` | `backlog` | feature | B1 approved design |
| **C2** | `...C2-implement-catalog-data-wiring.md` | `backlog` | feature | B2 approved design + C1 completed |
| **C3** | `...C3-implement-rh-400-catalog-vertical-slice.md` | `backlog` | feature | B2 approved design + C2 completed |
| **C4** | `...C4-implement-component-catalog-case.md` | `backlog` | feature | C3 completed + B2 approved design |
| **C5** | `...C5-implement-surface-asset-integration.md` | `backlog` | feature | B3 approved design |
| **D1** | `...D1-verify-asset-registry-mapping.md` | `backlog` | documentation | C1 completed + B1 design |
| **D2** | `...D2-verify-catalog-ui.md` | `backlog` | documentation | C3 + C4 completed + B2 design |
| **D3** | `...D3-verify-surface-asset-integration.md` | `backlog` | documentation | C5 completed + B3 design |

### Summary Counts
- **Completed**: 1 (A1)
- **Active**: 1 (Asset-Generation Standalone — orphaned, not part of A/B/C/D chain)
- **Backlog**: 15 (A2-A6, B1-B3, C1-C5, D1-D3)
- **Total canonical tasks**: 17

---

## 2. VISUAL_CONTRACT.md Status

**Location**: `galaxyGame/docs/reference/asset-generation/VISUAL_CONTRACT.md`  
**Version**: 1.0, Created 2026-09-11  
**Status**: EXISTS, CANONICAL — authoritative contract for asset generation visuals.  
**Not redispatched per requirements.**

Key settled decisions confirmed in VISUAL_CONTRACT.md:
- `asset_id` is the shared canonical identity across all artifact types.
- Blueprints do NOT carry `visual_profile` or `visual_definition` fields.
- PromptCompiler's public interface is fixed at 5 keyword arguments (no expansion).
- Visual Definition = appearance; Visual Profile = development-time composition guidance; Render Template = development-time output structure.
- Development-time Asset Registry/orchestration resolves artifact relationships.
- PromptCompiler does NOT repository-search by `asset_id`.

---

## 3. B1 Actual Status — Detailed Review

**Task file location**: `backlog/asset-ui/...B1-map-asset-registry-to-visual-definition.md`  
**YAML status**: `backlog`  
**Action Line in task**: "NEEDS A1 EVIDENCE BEFORE DISPATCH"

### Was B1 actually completed?
**NO.** The task file remains in `backlog/` with `status: backlog`. No synthesis report exists for B1. No output artifacts (mapping document, design spec) exist.

### What was prepared historically?
The `status.md` entry from 2026-09-29 states:
> "B1 Dispatch Handoff — PREPARED ✅"  
> "Handoff file: `handoffs/qwen(planning agent)/2026-09-28-b1-readiness-assessment-for-chatgpt.md`"

This was a **readiness assessment**, not actual completion. The handoff was prepared for ChatGPT review/decision but was never executed. B1 was never dispatched to an agent for implementation.

### B1 prerequisites check:
- ✅ A1 evidence exists (synthesis report at `summaries/2026-09-03-...`)
- ❌ A2 findings — **MISSING** (task still in backlog)
- ❌ A3 findings — **MISSING** (task still in backlog)
- ❌ A4 findings — **MISSING** (task still in backlog)
- ❌ A6 findings — **MISSING** (task still in backlog)

### B1's own prerequisite declaration:
The task file says "NEEDS A2-A4-A6 EVIDENCE BEFORE DISPATCH" — this is the correct dependency. The historical status.md entry claiming B1 was "READY FOR DISPATCH" was **incorrect** because it only checked A1, not the full A2-A4-A6 prerequisite set that B1's own task file declares.

---

## 4. A2-A6 Actual Status

### All five research tasks: `status: backlog`, location: `backlog/asset-ui/`

| Task | Title | Priority | Has Synthesis? |
|------|-------|----------|----------------|
| **A2** | Static Asset Storage Research | HIGH | ❌ No |
| **A3** | RH-400 Asset Family Mapping | HIGH | ❌ No |
| **A4** | Catalog Data Retrieval Research | HIGH | ❌ No |
| **A5** | Surface Sprite Consumption Research | HIGH | ❌ No |
| **A6** | Icon Bible Dependency Research | MEDIUM | ❌ No |

**None of A2-A6 have been executed.** No synthesis reports exist. No output artifacts. All remain in their original backlog state from 2026-08-31.

---

## 5. Recalculated Dependency Graph (From Actual State)

```
A1 ✅ (completed — synthesis exists)
 │
 ├─→ A2 ❌ (backlog, no deps) ─┐
 ├─→ A3 ❌ (backlog, no deps) ─┤──→ B1 ❌ (needs A1 + A2 + A4 + A6)
 ├─→ A4 ❌ (backlog, no deps) ─┤
 └─→ A6 ❌ (backlog, no deps) ─┘

 ├─→ A3 ✅(as input to B3) ───┐
 └─→ A5 ❌ (backlog, no deps) ─┤──→ B3 ❌ (needs A3 + A5)

B2 ❌ (needs A2 + A3 + A4 + A6)
 │
 ├─→ C1 ❌ (needs B1)
 │    │
 │    └─→ C2 ❌ (needs B2 + C1)
 │         │
 │         └─→ C3 ❌ (needs B2 + C2)
 │              │
 │              └─→ C4 ❌ (needs C3 + B2)
 │
 └─→ C5 ❌ (needs B3)

D1 ❌ (needs C1 + B1)
D2 ❌ (needs C3 + C4 + B2)
D3 ❌ (needs C5 + B3)
```

### Critical path to first implementation work:
```
A2 → B1 → C1 → C2 → C3 → C4
A3 → B1 → C1 → C2 → C3 → C4
A4 → B2 → C2 → C3 → C4
A6 → B2 → C2 → C3 → C4

A3 → B3 → C5
A5 → B3 → C5
```

### Surface branch (parallel to catalog):
```
A3 → B3 → C5 → D3
A5 → B3 → C5 → D3
```

---

## 6. Inconsistencies Found

### 6.1 A1 YAML Status Mismatch
**Issue**: A1 task file in `completed/2026-08/` has `status: active` in YAML frontmatter, but the task is clearly completed (synthesis report exists, body says "Task A1 is closed").  
**Severity**: Low — cosmetic. The file is in the correct `completed/` folder.

### 6.2 B1 Readiness Claim Was Premature
**Issue**: `status.md` from 2026-09-29 states B1 was "READY FOR DISPATCH" based only on A1 completion. However, B1's own task file declares it needs A2, A3, A4, AND A6 evidence — not just A1.  
**Severity**: Medium — this created a false signal that B1 could proceed when its actual prerequisites were unmet.

### 6.3 Asset-Generation Standalone Task Is Orphaned
**Issue**: The task at `active/asset-ui/...ASSET-GENERATION-STANDALONE-EXECUTION.md` has:
- YAML status `active` but no corresponding backlog source file (the dispatch interface references a non-existent path)
- No dependency links to the A/B/C/D chain
- No synthesis report
- Checklist shows "NOT DONE" on path verification
**Severity**: Medium — this task is in limbo. It may be related to the broader asset work but has no defined place in the current roadmap.

### 6.4 No Completed/asset-ui/ Directory
**Issue**: There is no `completed/asset-ui/` directory. A1 was placed in `completed/2026-08/` (a flat date-based folder). This is inconsistent with how other completed tasks are organized and makes it harder to audit the asset-ui workstream specifically.  
**Severity**: Low — organizational, not functional.

### 6.5 No Summaries for Any Research Task
**Issue**: A2-A6 have zero summaries. B1-B3 have zero summaries. C1-C5 have zero summaries. D1-D3 have zero summaries. Only A1 has a synthesis report (`summaries/2026-09-03-RESEARCH-ASSET-REGISTRY-REALITY-CHECK.md`).  
**Severity**: Medium — indicates the entire research phase was never executed, not just deferred.

---

## 7. Recommended Next Dispatch

### **A2 — Static Asset Storage Research** is the correct next task.

#### Why A2 first:
1. **No dependencies** — A2 can start immediately without waiting for any other task.
2. **Unlocks B1 and B2** — B1 needs A2 evidence; B2 needs A2 evidence. Both are blocked on this research.
3. **Parallelizable with A3, A4, A5, A6** — All five research tasks (A2-A6) are independent of each other. They could theoretically be dispatched in parallel. However:
   - A2 is the most foundational: it establishes where assets live and how they're referenced, which informs the scope of all other research.
   - Starting with A2 gives the next researcher context for what "static asset storage" means before they investigate specific domains (RH-400 mapping, catalog retrieval, sprite consumption, icon Bible).

#### Why not dispatch A2-A6 in parallel:
While technically possible, parallel dispatch of all five research tasks would be wasteful because:
- The findings from each task will inform the scope and focus of adjacent tasks.
- A sequential approach (A2 → A3 → A4 → A5 → A6) allows each researcher to reference prior findings in their synthesis, producing tighter, more coherent output.
- If resources allow parallel execution, A2-A4 could go first (they feed B1 and B2), followed by A5-A6.

#### Why not B1:
B1's own task file explicitly requires A2, A3, A4, AND A6 evidence. Dispatching B1 now would produce a design based on incomplete research — the opposite of what "smallest mapping" means when half the data is missing.

#### Why not the Asset-Generation Standalone task:
This task is orthogonal to the A/B/C/D chain. It addresses tooling infrastructure (running specs, CLI entry point) rather than architectural contracts. It could be dispatched independently but should not block or be blocked by the A/B/C/D roadmap. Its orphaned state (active without a backlog source) should be resolved first — either connect it to the roadmap or move it back to backlog with proper dependency documentation.

---

## 8. Recommended Action Sequence

1. **Resolve Asset-Generation Standalone** — Move to backlog with proper dependency docs, or close if superseded.
2. **Dispatch A2** (or A2-A4 in parallel if resources allow)
3. **Upon A2-A6 completion** → Dispatch B1 and B3 in parallel (they have independent prerequisite sets)
4. **Upon B2 completion** → Dispatch B2 (independent of B1/B3)
5. **Upon B-series completion** → Dispatch C-series (C1+C2+C3+C4 sequential; C5 parallel)
6. **Upon C-series completion** → Dispatch D-series in parallel

---

## 9. Files Referenced in This Audit

| File | Purpose |
|------|---------|
| `agent-tasks/.../completed/2026-08/...A1-asset-registry-reality-check.md` | A1 task file (completed) |
| `agent-tasks/.../summaries/2026-09-03-RESEARCH-ASSET-REGISTRY-REALITY-CHECK.md` | A1 synthesis report |
| `agent-tasks/.../backlog/asset-ui/...B1-B3*.md` | B-series task files (all backlog) |
| `agent-tasks/.../backlog/asset-ui/...A2-A6*.md` | A-research task files (all backlog) |
| `agent-tasks/.../backlog/asset-ui/...C1-C5*.md` | C-series task files (all backlog) |
| `agent-tasks/.../backlog/asset-ui/...D1-D3*.md` | D-series task files (all backlog) |
| `agent-tasks/.../active/asset-ui/...ASSET-GENERATION-STANDALONE*.md` | Orphaned active task |
| `galaxyGame/docs/reference/asset-generation/VISUAL_CONTRACT.md` | Canonical visual contract (v1.0, authoritative) |
| `agent-tasks/.../status.md` | Project status file (contains stale B1 readiness claim) |
