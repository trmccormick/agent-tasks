# FINAL SUMMARY FOR CLAUDE — Guardrails Refactoring Task

**Created**: 2026-07-01  
**Purpose**: Condensed review for Claude (minimal session time)  
**Status**: Ready for approval/refinement

---

## EXECUTIVE SUMMARY

Unified task ready for final approval. Stale guardrails task (2026-06-21) has been reconciled with Gemini's 2026-07-01 audit findings. Result: HIGH-priority task to refactor `docs/GUARDRAILS.md` (681 lines) into domain-specific documentation across `docs/architecture/` and `docs/mission_profiles/`.

**Key Decision**: Replace stale task entirely with unified version (more detailed, lower risk).

---

## CLAUDE'S DECISIONS NEEDED

### 1. Approve Pre-Work Phase First

Two pre-work tasks now exist (created based on Qwen's validation):
- `2026-07-01-PRE-WORK-CREATE-DIRECTORIES.md` — Simple infrastructure (mkdir)
- `2026-07-01-PRE-WORK-RESOLVE-AMBIGUITIES.md` — Strategic decisions (needs your input)

**Claude decision**: 
- ✅ Approve pre-work execution (infrastructure + strategic decisions)
- ❌ Request changes to pre-work scope

### 2. Strategic Decisions on Pre-Work

Pre-work task #2 needs YOU to decide:

**Decision #1**: Where should `em_power_shield_tiers.md` go?
- [ ] Option A: `docs/systems/em_power_shield_tiers.md`
- [ ] Option B: `docs/architecture/stations/em_power_shield_tiers.md`
- [ ] Option C: `docs/architecture/structures/em_power_shield_tiers.md`

**Decision #2**: How to resolve agent-tasks GUARDRAILS.md drift (255 lines)?
- [ ] Option A: Update agent-tasks copy to match root before migration
- [ ] Option B: Migrate root first, resync agent-tasks after
- [ ] Option C: Audit both files first; reconcile before any work

**Decision #3**: How to handle content overlap in target directories?
- [ ] Option A: Conduct overlap audit (show what conflicts exist)
- [ ] Option B: Executor merges manually (risky)
- [ ] Option C: Create new consolidated docs instead of merging

**Decision #4**: Where should "Market vs. Build" game logic documentation live?
- [ ] Option A: `docs/architecture/economy/market_vs_build_logic.md` (economy context)
- [ ] Option B: `docs/architecture/structures/` (structure context)
- [ ] Option C: Create `docs/architecture/gameplay/` for this type of content

### 3. Then: Extraction Mapping (Gemini Phase 2)

Once pre-work is resolved, we'll get Gemini Phase 2 synthesis:
- File: `_working-temp/2026-07-01-GEMINI-GUARDRAILS-EXTRACTION-SYNTHESIS.md` (separate session)
- Purpose: Detailed section-by-section mapping for executor

**Claude decision when available**:
- [ ] Mapping is complete and unambiguous?
- [ ] All 4 pre-work decisions factored in?
- [ ] Ready for executor?

---

## PLACEMENT DECISION (Claude's Call — After Pre-Work Approved)

Once all 4 pre-work decisions are made:

**Option A: Keep in `/2026-07-01-TASK-UPDATE-2/`** (with other 2026-07-01 tasks)
- Unified task stays here
- Pre-work tasks stay here
- Ready for executor when pre-work complete
- Research files: Keep in temp for executor reference

**Option B: Move to `/agent-tasks/projects/galaxy_game/tasks/active/`** (formal task activation)
- Move unified + pre-work tasks to agent-tasks repo
- Update YAML header: `status: active`
- Formal task tracking across the project
- Research files: Archive or reference from formal task location

**Option C: Both locations** — Cross-references between repos

**Claude decision**: Which option makes sense for your workflow?

---

## RESEARCH FILES TO PRESERVE

Keep in `_working-temp/` until Claude decides:
- `GUARDRAILS_REF_PACKAGE.md` (Gemini's detailed audit mapping)
- `TASK_REF_GUARDRAILS_MIGRATION_V2.md` (original detailed spec)
- `2026-07-01-GEMINI-GUARDRAILS-EXTRACTION-SYNTHESIS.md` (extraction map, when ready)
- `2026-07-01-TASK-GUARDRAILS-VALIDATION-QWEN.md` (prerequisite validation, when ready)

**These become executor reference docs** — either stay in temp or move to final location based on task placement.

---

## CURRENT STATUS

✅ **READY FOR CLAUDE NOW**:
- `2026-07-01-TASK-GUARDRAILS-UNIFIED-RECONCILIATION.md` — Unified task (final location)
- `2026-07-01-PRE-WORK-CREATE-DIRECTORIES.md` — Infrastructure pre-work (final location)
- `2026-07-01-PRE-WORK-RESOLVE-AMBIGUITIES.md` — Strategic pre-work (final location)
- `_working-temp/2026-07-01-TASK-GUARDRAILS-VALIDATION-QWEN.md` — Qwen's findings (reference)

⏳ **BLOCKED UNTIL CLAUDE DECIDES**:
- 4 strategic decisions (see section above)
- Placement decision (Option A/B/C)
- Gemini Phase 2 extraction synthesis (waiting for go-ahead)

---

## CLAUDE'S SESSION TASKS

1. **Read unified task** — Confirm it's still solid (yes/no)
2. **Read this summary** — Understand blockers and decisions
3. **Make 4 strategic decisions** — em_power_shield path, drift strategy, overlap handling, market-vs-build home
4. **Read Qwen's validation** — Reference: `_working-temp/2026-07-01-TASK-GUARDRAILS-VALIDATION-QWEN.md`
5. **Make placement decision** — A/B/C?
6. **Go/no-go for Gemini Phase 2** — Should we do extraction synthesis, or refine pre-work first?

---

## WHAT HAPPENS NEXT (Post-Claude Decisions)

**If approved with decisions made**:
1. Pre-work phase activates (directory creation + strategic decisions applied)
2. Proceed to Gemini Phase 2 (extraction synthesis in separate session)
3. After Gemini synthesis: Ready for executor

**If changes requested**:
1. Qwen/Gemini address feedback
2. Updated reports to temp
3. Return to Claude for re-review

**If blockers found**:
1. Pause and address
2. Return to Claude for next steps

---

## CLAUDE: When You Review This

Keep your response **short and clear**:
- ✅ Approve with 4 decisions listed (1-4 answers)
- ⚠️ Request specific changes (list them)
- ❌ Blocker found (describe)
- 📋 Placement preference (A/B/C)
- 🟢 Gemini Phase 2: Go ahead? (yes/no)

We'll execute everything post-decision.
