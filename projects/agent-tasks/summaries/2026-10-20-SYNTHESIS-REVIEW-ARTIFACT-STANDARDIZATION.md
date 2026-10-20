## SYNTHESIS REPORT: Review Artifact Standardization

**Task**: 2026-10-19-MEDIUM-DOCUMENTATION-REVIEW-ARTIFACT-STANDARDIZATION.md
**Status**: SYNTHESIS IN PROGRESS
**Date**: 2026-10-20

---

### What I'm About to Do

Standardize review agent output by: (1) adding a "Standard Review Artifact Format" subsection to REVIEW_AGENT_WORKFLOW.md §4 after the existing artifact paragraph; (2) explicitly including review sessions in SESSION_CLOSEOUT.md's "Handoff Files (required)" first sentence; (3) creating REVIEW_REPORT_TEMPLATE.md as a standalone copy-paste reference.

---

### Files I'll Examine/Modify

| File | Purpose | Status |
|---|---|---|
| `REVIEW_AGENT_WORKFLOW.md` | Primary target — add artifact structure guidance + example | not started |
| `SESSION_CLOSEOUT.md` | Secondary target — explicit review session inclusion in handoff section | not started |
| `REVIEW_REPORT_TEMPLATE.md` | Optional new file — standalone copy-paste template | not started |
| `WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md` § "Change Set 3" | Detailed spec (reference) | reviewed |

---

### Prerequisites Completed

- ✅ Step 0: Task file moved to active/ with git mv; status YAML updated backlog → active
- ✅ Verified: only one copy via `find` command
- ✅ Read WORKFLOW_GOVERNANCE_IMPLEMENTATION_PLAN.md Change Set 3
- ✅ Read REVIEW_AGENT_WORKFLOW.md (current §4 structure documented)
- ✅ Read SESSION_CLOSEOUT.md § "Handoff Files (required)" (confirmed review gap exists)
- ✅ Understand this task file

---

### Expected Outcomes

- REVIEW_AGENT_WORKFLOW.md §4 has new "Standard Review Artifact Format" subsection with:
  - Save location guidance (`projects/[PROJECT]/summaries/YYYY-MM-DD-REVIEW-[TOPIC].md`)
  - Mandatory sections (Assignment, Evidence Basis, Findings, Open Concerns, Disposition, Rationale, Recommendations)
  - Length/format constraints (1–2 pages, Markdown)
  - Concrete example (~15–20 lines) with evidence labeling shown
- SESSION_CLOSEOUT.md "Handoff Files (required)" first sentence explicitly includes "review" alongside research/implementation/web
- Optional REVIEW_REPORT_TEMPLATE.md created as standalone reference (<50 lines)
- Total additions: ~40–50 lines across files

---

### Critical Gotchas I Will Avoid

- ❌ Repeating full evidence standards from REVIEW_AGENT_GUIDE — instead ✅ reference and link to it
- ❌ Making review artifacts longer than "concise" (1–2 pages) — instead ✅ keep to 7 required sections + optional extras
- ❌ Contradicting existing REVIEW_AGENT_WORKFLOW.md guidance — instead ✅ expand what's already there (append after current §4 text)
- ❌ Creating duplicate information between artifact and handoff — instead ✅ clarify relationship (artifact = review output; handoff = session record for next agent)
- ❌ Over-modifying SESSION_CLOSEOUT.md — only the first sentence of the handoff section changes, keep it minimal

---

**SYNTHESIS COMPLETE.** Awaiting approval to proceed with implementation.
