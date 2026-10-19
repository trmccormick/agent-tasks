## SYNTHESIS REPORT: Evidence Classification and Reviewer Routing

**Task**: 2026-10-19-HIGH-DOCUMENTATION-EVIDENCE-AND-ROUTING-FRAMEWORK.md
**Status**: SYNTHESIS COMPLETE — READY FOR APPROVAL
**Date**: 2026-10-19

---

### What I'm About to Do

Add one new subsection to `PLANNING_AGENT_SESSION_START.md` containing two parts:

1. **Evidence Classification in Synthesis Reports** (~30 lines): Guidance on labeling every claim as VERIFIED, REPORTED, or INFERRED with concrete definitions and an example synthesis report structure
2. **Reviewer Routing Recommendation** (~10 lines): Optional metadata guidance (YES/NO + reason) for planning outcomes that may need review

The goal is to improve planning synthesis traceability and clarify when planning outcomes need review.

---

### Files I'll Examine

| File | Purpose | Status |
|---|---|---|
| `PLANNING_AGENT_SESSION_START.md` | Target file — add new section after Step 1, before "Qwen — Three Modes" | ✅ Read |
| `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` § "Change Set 1 & 2" | Detailed spec for both changes | ✅ Read |
| `REVIEW_AGENT_GUIDE.md` § "Evidence Standards" | Existing principle defining VERIFIED/REPORTED/INFERRED (called Direct verification/Supplied evidence/Dated claims) | ✅ Read |

---

### Prerequisites Completed

- ✅ Step 0: Task file moved to active/ with git mv
- ✅ YAML status updated from backlog → active
- ✅ Verified only ONE copy exists via find
- ✅ Read WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md Change Sets 1 & 2 (LOW complexity, additive changes)
- ✅ Read PLANNING_AGENT_SESSION_START.md current structure (Step 1 ends at line ~30; "Qwen — Three Modes" starts after)
- ✅ Read REVIEW_AGENT_GUIDE.md Evidence Standards section (three types: Direct verification / Supplied evidence / Dated claims)
- ✅ Read SYSTEM_INTENT.md principles (evidence is foundational to the workflow)
- ✅ Understand this task file and all acceptance criteria

---

### Key Findings

#### Evidence Classification Mapping

REVIEW_AGENT_GUIDE uses slightly different terminology than the workflow spec:

| Workflow Spec | REVIEW_AGENT_GUIDE Equivalent | My Synthesis |
|---|---|---|
| VERIFIED | Direct verification | What you checked yourself this session |
| REPORTED | Supplied evidence / Dated claims | Pasted logs, handoffs, or status.md entries (with date) |
| INFERRED | (implied by distinction) | Conclusion based on evidence; flag for verification if material |

**Decision**: Use the WORKFLOW spec terminology (VERIFIED/REPORTED/INFERRED) since this is PLANNING_AGENT_SESSION_START.md — it's simpler, more consistent with Change Set 1, and REVIEW_AGENT_GUIDE can be referenced for deeper detail.

#### Insertion Point Verified

```
Line ~30: Step 1 closing text ends ("Do not invent a resolution.")
Line ~32: --- (horizontal rule)
Line ~34: ## Qwen — Three Modes  (section header to insert BEFORE)
```

New section will go between the horizontal rule after Step 1 and "## Qwen — Three Modes".

#### Scope Confirmed

- **Change Sets 1 & 2 only** (this task covers only evidence classification + reviewer routing)
- Total addition: ~40 lines (30 for evidence framework + 10 for routing)
- All additive — no restructuring of existing sections
- Only one file modified: `PLANNING_AGENT_SESSION_START.md`

---

### Expected Outcomes

- PLANNING_AGENT_SESSION_START.md has new subsection titled something like `## Synthesis Report Structure — Evidence Framework`
- Subsection includes:
  - Evidence labeling guidance (VERIFIED/REPORTED/INFERRED with 1-2 line definitions)
  - Concrete example synthesis report structure showing proper labeling (at least 2 of 3 types used)
  - Reviewer Routing metadata format (YES/NO + reason + suggested capability)
  - Note that REVIEW_AGENT_GUIDE defines evidence categories in full (cross-reference)
  - Routing recommendation explicitly notes: planning can recommend, NOT auto-dispatch
- Total addition: ~40 lines (30 for evidence + 10 for routing)
- All new content is additive (no restructuring existing sections)
- No changes to other files

---

### Critical Gotchas I Will Avoid

- ❌ Repeating full REVIEW_AGENT_GUIDE § "Evidence Standards" — instead ✅ summarize for planning context and reference original
- ❌ Suggesting auto-dispatch based on routing recommendation — instead ✅ make clear this is input to human decision only
- ❌ Making evidence classification mandatory for all planning tasks — instead ✅ frame it as part of synthesis reporting guidance
- ❌ Recommending specific agent names — instead ✅ name required capabilities (human decides who provides them)
- ❌ Modifying terminology from the workflow spec (VERIFIED/REPORTED/INFERRED) to match REVIEW_AGENT_GUIDE — keep planning-facing terminology simple, reference original for nuance
- ❌ Adding content to "Qwen — Three Modes" section — only add new section between Step 1 and that heading

---

### Acceptance Criteria Cross-Check

| # | Criterion | How I'll Meet It |
|---|---|---|
| 1 | PLANNING_AGENT_SESSION_START.md contains new subsection on synthesis report structure | New `## Synthesis Report Structure — Evidence Framework` section inserted at verified location |
| 2 | Evidence classification framework present (VERIFIED/REPORTED/INFERRED with definitions) | Three labeled lines with 1-sentence definitions each |
| 3 | Concrete example shows proper evidence labeling (at least 2 of 3 types) | Example synthesis report using VERIFIED and REPORTED labels in sample findings |
| 4 | Reviewer Routing Recommendation guidance present (optional, format, example) | Separate paragraph + example showing YES/NO + reason + capability |
| 5 | Routing recommendation explicitly notes: planning can recommend, not auto-dispatch | Explicit note at end of routing section |
| 6 | All references to existing docs (REVIEW_AGENT_GUIDE) accurate | Reference to `REVIEW_AGENT_GUIDE.md § "Evidence Standards"` — verified heading exists |
| 7 | New content is ~40 lines total (additive, no restructuring) | Estimated 38-42 lines; insert between Step 1 and Qwen section without modifying existing content |
| 8 | Changes staged, committed, and pushed | Step 4 commands in task file |
| 9 | Task file moved to completed/ and pushed | Step 5 commands in task file |

---

### Implementation Plan

```markdown
## Synthesis Report Structure — Evidence Framework

*(~30 lines total)*

A synthesis report proves you understand the project state before taking action. Every claim must be labeled by how it was established:

- **(VERIFIED)** — I checked this myself this session (command output, direct file inspection).
- **(REPORTED)** — From status.md, a handoff file, or human statement. Include the date: `[YYYY-MM-DD]`.
- **(INFERRED)** — My conclusion based on other evidence. If material to the plan, flag for verification.

**Required sections:** Assignment summary · Evidence basis (files read, labels) · Findings · Next steps

*Example:*
```markdown
### Findings
- Project `galaxy_game` has 3 active tasks (REPORTED [2026-10-18], from status.md)
- Task 2026-10-18-X is complete — verified via git log commit on main ✓
- Task 2026-10-17-Y has no handoff for 48h — INFERRED: may need follow-up, recommend verification next session
```

---

## Routing Recommendation (Optional)

If the planning outcome carries risk or affects other projects, add routing metadata:

```
Routing: YES
Reason: Architecture change affecting downstream API consumers.
Suggested capability: Review by agent with service-contract knowledge.
Note: Planning agents recommend review; they cannot auto-dispatch. Human decides.
```

*See `REVIEW_AGENT_GUIDE.md § "Evidence Standards"` for full evidence definitions.*
```

---

**SYNTHESIS COMPLETE.** Ready for approval to proceed with implementation.
