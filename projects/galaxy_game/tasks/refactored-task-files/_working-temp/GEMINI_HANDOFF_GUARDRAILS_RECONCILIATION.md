# Gemini Handoff — Guardrails Reconciliation Audit

**Role**: Research & Verification Agent  
**Task**: Reconcile stale task against your audit findings  
**Format**: Paste this entire text into Gemini chat

---

## CONTEXT: YOUR RECENT AUDIT (2026-07-01)

You completed a surgical audit of `docs/GUARDRAILS.md` producing two deliverables:

**1. GUARDRAILS_REF_PACKAGE.md** — Detailed audit containing:
- Section-to-folder mapping analysis (681 lines broken down by concern)
- Economic constants verification against GLOSSARY_SYSTEM_MECHANICS.md (SCC Surcharge, Broker Fee, Sales Tax all verified as ✅ Aligned)
- 6-phase refactoring plan (Audit & Verify → Extract Game Architecture → Extract Game Design → Trim to Agent Protocol → Create Migration Index → Update agent-t/rules sync)

**2. TASK_REF_GUARDRAILS_MIGRATION_V2.md** — Formal task spec with:
- 4 deep audit findings:
  - Issue 1: Duplicate section numbering; Section 4 missing; Section 13 duplicated
  - Issue 2: Mixed concern levels (operational vs. architecture vs. design)
  - Issue 3: Economic constants fragmented across three files; GLOSSARY must be sole source of truth
  - Issue 4: Stale implementation notes exist
- Execution workflow with planner-to-executor handoff structure
- Validation checklist

---

## STALE TASK (created 2026-06-21)

**Name**: TASK_GUARDRAILS_SPLIT.md  
**Problem Statement**: Same as yours — `docs/GUARDRAILS.md` mixes agent operating rules with game design/system intent

**Target Folders**:
- `docs/GUARDRAILS.md` (source to trim)
- `docs/architecture/*` (target for architecture sections)
- `docs/gameplay/mechanics.md` (target for gameplay boundaries)
- `docs/flavor/EASTER_EGGS.md` (target for easter eggs)
- `docs/systems/em_power_shield_technology.md` (NEW EM shield doc)

**Acceptance Criteria** (5 items):
- [ ] Design/system sections moved to correct target docs
- [ ] No duplicated content introduced across target docs
- [ ] GUARDRAILS.md retains only agent rules and environment/process constraints
- [ ] New EM shield technology doc created and linked
- [ ] Routing index remains consistent for future agent updates

**Implementation Steps** (5 generic steps):
1. Extract mapped sections from GUARDRAILS.md by documented section mapping
2. Merge into target docs without overwriting existing canonical content
3. Create `docs/systems/em_power_shield_technology.md` for missing target
4. Remove moved sections from GUARDRAILS.md while preserving required core rules
5. Verify cross-links and table-of-contents consistency

---

## YOUR ASSIGNMENT

Review both sources above and answer:

**Question 1 — Content Reconciliation**
- Does the stale task capture any requirements your audit missed?
- Does your audit reveal issues (section numbering, duplicate sections, constants fragmentation, stale notes) that make the stale task INCOMPLETE?

**Question 2 — Target Folder Validation**
- Stale task targets: `docs/gameplay/mechanics.md`, `docs/flavor/EASTER_EGGS.md`, `docs/systems/em_power_shield_technology.md`
- Your audit suggested granular mapping to `docs/mission_profiles/` and `docs/architecture/` subdirectories
- Are the stale task's folders correct, or does your mapping suggest they should go elsewhere?

**Question 3 — Task Supersession Decision**
- Should the stale task be **completely replaced** by your TASK_REF_GUARDRAILS_MIGRATION_V2.md?
- Or is the stale task a **simpler precursor** to your more detailed 6-phase plan?
- Which version should go to the executor?

---

## REQUIRED DELIVERABLE

**Produce ONE unified task file** with these sections:

1. **Reconciliation Summary** (2-3 sentences): What the stale task adds to your audit + what your audit adds to the stale task

2. **Refined Acceptance Criteria** (merged list): Combine all 5 from stale task + any new ones from your audit findings

3. **Refined Execution Plan** (3-5 consolidated steps): Merge the 5 generic stale task steps + your 6-phase plan into one clear workflow

4. **Issues Identified** (bulleted list): The 4 deep findings from your audit + any new ones from reconciliation

5. **Risk Flags** (bulleted list): Implementation order dependencies, gotchas, prerequisites

6. **Recommended Priority**: Should this be HIGH (due to fragmented constants issue) or MEDIUM? Justify.

7. **Next Step Recommendation** (1 sentence): Is this ready for executor, or does it need parallel review with Qwen first?

**Return as**: Markdown file with clear sections

---

## SUCCESS CRITERIA

✅ You've reconciled both task sources  
✅ Explained what each adds and what conflicts or gaps exist  
✅ One clear unified task (no conflicting versions)  
✅ Acceptance criteria complete and merged  
✅ Risk/issue flags identified  
✅ Executor can pick this up unambiguously after review  

---

**When done**: Paste your unified task back here and we'll move to next phase (Qwen parallel review or direct to executor).
