# Review Report Template — Copy and Customize

Use this template for review artifacts per `REVIEW_AGENT_WORKFLOW.md` §4. Save as `projects/[PROJECT]/summaries/YYYY-MM-DD-REVIEW-[TOPIC].md`. Keep to 1–2 pages.

---

```markdown
### Review: [TOPIC]

**Assignment**: [One sentence: what was reviewed, what decision requested.]

**Evidence Basis**:
- [File/commit/command checked] — (VERIFIED) or (REPORTED)

**Findings**:
1. [Finding 1 with evidence reference]
2. [Finding 2 with evidence reference]
3. [Finding 3 with evidence reference, if applicable]

**Open Concerns** (optional; omit if none):
- [Unresolved question or edge case]

**Disposition**: PROCEED | PROCEED WITH CAUTION | STOP (choose one)

**Rationale**: [One paragraph explaining why.]

**Recommendations** (optional; omit if none):
- [Future work or decision for human/planning agent]
```

---

**Example (filled):**

```markdown
### Review: Task File Drafting Standards

**Assignment**: Review draft TASK_TEMPLATE.md; approve for commit.

**Evidence Basis**:
- TASK_TEMPLATE.md text — (REPORTED)
- `grep -r "PLACEHOLDER" projects/agent-tasks/tasks/` — (VERIFIED): no placeholders found

**Findings**:
1. Acceptance Criteria section uses `[ ]` checkboxes — not finalized (from file, REPORTED).
2. Findings lack evidence labels — inconsistent with REVIEW_AGENT_GUIDE (grep verified, VERIFIED).

**Disposition**: PROCEED WITH CAUTION

**Rationale**: Structure is correct; needs minor fixes before final approval.

**Recommendations**: Author should add evidence labels to all findings.
```

---

**Notes:**
- Sections 1–6 are mandatory; 4 (Open Concerns) and 7 (Recommendations) are optional if none apply.
- Every claim must be labeled `(VERIFIED)` or `(REPORTED)`. Never state unverified facts as truth.
- For full guidance, see `REVIEW_AGENT_WORKFLOW.md` § "Standard Review Artifact Format".
