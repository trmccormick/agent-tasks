# Workflow Governance Implementation Plan
**Date:** 2026-10-19  
**Agent:** Haiku (Governance Review)  
**Status:** Analysis Complete — Ready for Implementation Planning

---

## Overview

This document decomposes four approved workflow recommendations into concrete, delegable change sets. **No changes are made here—this is a specification for implementation by lower-cost agents (Qwen, Planning Agent) with review oversight.**

**Approved Recommendations:**
1. ✅ VERIFIED / REPORTED / ASSUMED evidence classification
2. ✅ Reviewer Routing Recommendation  
3. ✅ Review Report Template
4. ♻️ Risk Register replacement via stronger "Stop Condition" guidance

---

## Change Set 1: Evidence Classification in Planning Synthesis

### Title
Add VERIFIED/REPORTED/ASSUMED evidence framework to planning synthesis reporting

### Purpose
Formalize existing principle (already in REVIEW_AGENT_GUIDE § "Evidence Standards") by making it an explicit requirement in planning synthesis reports. This improves traceability and prevents confusion between live facts and dated claims.

### Target Files
- **Primary:** `PLANNING_AGENT_SESSION_START.md`
  - Add new section after "Step 1 — Confirm your role and project" (before "Qwen — Three Modes")
  - Provide guidance on creating synthesis reports with evidence labeling
  - Reference existing REVIEW_AGENT_GUIDE framework
- **Supporting reference:** REVIEW_AGENT_GUIDE.md (no changes needed; already contains "Evidence Standards" section)

### Expected Modifications

**Location in PLANNING_AGENT_SESSION_START.md:**  
Insert new subsection after Step 1, before "Qwen — Three Modes" section.

**Content should cover:**
- What a synthesis report is (agent proves understanding of project state before acting)
- Required sections: Assignment summary, Evidence basis, Findings, Next steps
- Evidence labeling requirement: Every claim must use one of three labels:
  - `(VERIFIED)` — checked by command/inspection this session
  - `(REPORTED)` — from status.md, handoffs, or human statement (with date)
  - `(INFERRED)` — deduction based on evidence; if material, flag for verification
- Example synthesis report showing proper labeling
- Note that REVIEW_AGENT_GUIDE § "Evidence Standards" defines these categories in full

**Content should NOT include:**
- Full template (that goes to separate template file if created)
- Implementation examples from other projects
- Deep workflow theory

### Complexity
**LOW**

Reason: Formalization of existing principle; 8–12 lines of guidance + 15–20 line example. No structural changes to existing sections, no cascading impacts.

### Suitable For
**Qwen (Planning Mode)** or **Human**

Reason: Documentation writing; no codebase knowledge needed. Qwen can read existing REVIEW_AGENT_GUIDE § "Evidence Standards" and restate principle for planning context.

### Requires Review
**YES**

Review scope:
- Wording clarity (does guidance match intent?)
- Alignment with existing REVIEW_AGENT_GUIDE framework
- Example synthesis report is realistic and shows proper labeling

---

## Change Set 2: Reviewer Routing Recommendation

### Title
Add formal routing recommendation metadata to planning synthesis reports

### Purpose
Formalize current best practice (allowed in REVIEW_AGENT_GUIDE § "Authority" but not mandatory in planning output). Planning agents should explicitly recommend review with reasoned YES/NO and capability rationale, helping human understand scope/risk assessment.

### Target Files
- **Primary:** `PLANNING_AGENT_SESSION_START.md`
  - Add new subsection to the synthesis report guidance (created in Change Set 1)
  - This becomes part of the same section, not a separate change

### Expected Modifications

**Location in PLANNING_AGENT_SESSION_START.md:**  
In the synthesis report guidance section (created in Change Set 1), add optional subsection immediately after evidence framework.

**Content should cover:**
- Optional metadata field: "Routing Recommendation"
- Format: YES | NO (structured)
- When to include: Provide reason whenever recommendation is non-obvious
- Example: 
  ```
  Routing Recommendation: YES
  Reason: Architecture change affecting service contracts; risk of breaking downstream integrations.
  Suggested Reviewer Capability: Architecture review with microservice/contract knowledge.
  ```
- Note: Don't name specific agents; name required capability (human decides who has it)
- Note: Planning agents cannot auto-dispatch; this is recommendation only

**Content should NOT include:**
- Agent-specific routing logic
- Auto-dispatch mechanism
- Override of human authority

### Complexity
**LOW**

Reason: 3–5 lines of guidance + 1 example. No structural changes; simple metadata addition to synthesis report format.

### Suitable For
**Qwen (Planning Mode)** or **Human**

Reason: Documentation writing; can be done in same session as Change Set 1. No codebase knowledge required.

### Requires Review
**YES**

Review scope:
- Wording clarity
- Ensures guidance aligns with REVIEW_AGENT_GUIDE § "Authority" (planning can recommend, not auto-dispatch)
- Example shows realistic routing use case

---

## Change Set 3: Review Report Template and Artifact Clarification

### Title
Standardize review session artifact structure and clarify handoff file requirements for review sessions

### Purpose
Close documentation gap on review artifact format. REVIEW_AGENT_WORKFLOW.md requires "one concise review artifact" but doesn't specify structure. SESSION_CLOSEOUT.md is unclear on whether review sessions must create handoff files. Standardization helps review agents produce consistent output and helps planning agents understand review findings.

### Target Files
- **Primary:** `REVIEW_AGENT_WORKFLOW.md`
  - Update § "4. Save one concise review artifact"
  - Add structured template after current guidance
- **Secondary:** `SESSION_CLOSEOUT.md`
  - Clarify in § "Handoff Files (required)" that review sessions also must create handoff files (currently says "research, implementation, and web agents")
  - Clarify relationship between review artifact and handoff file
- **Optional new file:** `REVIEW_REPORT_TEMPLATE.md`
  - Copy-paste template for review agents (if separation from workflow doc is preferred)
  - Keep it short: 6–8 sections, 40–50 lines with example

### Expected Modifications

#### In REVIEW_AGENT_WORKFLOW.md § "4. Save one concise review artifact":

Insert structured format guidance after current text ("For sessions without filesystem access..."):

**Content should specify:**
- Where to save review artifacts: `projects/[PROJECT]/summaries/YYYY-MM-DD-REVIEW-[TOPIC].md`
- Mandatory sections:
  1. **Assignment** (one sentence: what is being reviewed, what decision is requested)
  2. **Evidence Basis** (which files reviewed, which commands/searches run, evidence labeled as VERIFIED/REPORTED)
  3. **Findings** (2–5 key issues, each with evidence reference)
  4. **Open Concerns** (unresolved questions or edge cases not covered by findings; optional if none)
  5. **Disposition** (PROCEED | PROCEED WITH CAUTION | STOP — must choose one)
  6. **Rationale** (1 paragraph explaining why)
  7. **Recommendations** (optional; future work or decisions for human/planning agent)

- Length: 1–2 pages (concise, not comprehensive audit)
- Tone: Factual, linked to evidence, no general commentary
- Format: Markdown, plain text acceptable

#### In SESSION_CLOSEOUT.md § "Handoff Files (required)":

Modify first sentence:

Current: "Every research, implementation, and web agent session MUST create a formal handoff file..."

Revised: "Every research, implementation, web, and review agent session MUST create a formal handoff file..."

Add one line after "File location:":

```
(Also applies to review sessions. Save as: handoffs/<agent>/<date>-REVIEW-[TOPIC]-SESSION.md)
```

#### Optional: Create `REVIEW_REPORT_TEMPLATE.md`

Standalone file with copy-paste template (if review agents prefer a separate reference):
- Same sections as specified above
- Example with realistic content
- Notes on when to omit optional sections
- Keep <50 lines total

### Complexity
**MEDIUM**

Reason: 
- REVIEW_AGENT_WORKFLOW.md change is straightforward (add 8–12 lines of guidance + 15–20 line template)
- SESSION_CLOSEOUT.md change is minimal (add 1 sentence + 1 clarifying note)
- Optional template file is standalone, no dependencies
- No structural changes to existing workflow; clarifies what was always intended but underdocumented

### Suitable For
**Qwen (Planning Mode)** or **Human**

Reason: Documentation writing. Can read current REVIEW_AGENT_WORKFLOW.md and SESSION_CLOSEOUT.md, understand the gap, and fill it without codebase knowledge.

### Requires Review
**YES**

Review scope:
- Mandatory sections are appropriate (not too many, not too few)
- Disposition choices (PROCEED | PROCEED WITH CAUTION | STOP) align with current review practice
- Template example is realistic and uses proper evidence labeling
- Changes to SESSION_CLOSEOUT.md don't contradict existing guidance
- Optional template file adds value without creating redundancy

---

## Change Set 4: Enhanced Stop Condition Guidance for Risk Surfacing

### Title
Strengthen "Stop condition" field in status.md session entries to surface risks and blockers explicitly

### Purpose
Replace the proposed "Risk Register" artifact with enhanced guidance on the existing "Stop condition" field. This surfaces risks without requiring new per-project maintenance overhead. Keeps the system lightweight while preserving risk visibility.

### Target Files
- **Primary:** `SESSION_CLOSEOUT.md`
  - Modify § "1b. Session agent: append one entry"
  - Expand guidance for the "Stop condition" bullet
- **Secondary reference:** PLANNING_AGENT_CLOSEOUT.md (no changes needed; already handles status.md curation)

### Expected Modifications

**Location in SESSION_CLOSEOUT.md § "1b":**

After step 3 (describing the four bullets), expand the "Stop condition" bullet guidance (currently minimal):

**Current text (approx):**
```
- **Stop condition:** why you stopped, or "none" if the work is finished. Include the next action and who owns it.
```

**Should be expanded to include:**

Additional subsection immediately after step 3, titled something like "Guidance: Expressing Stop Conditions and Risks":

- **Normal stop:** "Task paused at [specific point]. Resume from: [exact file + line/method name]. Next action: [specific action]. Owner: [role]."
- **Blocked stop:** "Blocked on [dependency name/task]. Blocker evidence: [what shows it's blocked, e.g. git log, error message]. Cannot proceed without: [specific action from owner]."
- **Risk stop:** "Task paused. [Known risk]. If [risk event], then [impact]. Risk level: [LOW/MEDIUM/HIGH]. Mitigation: [what prevents risk or what to do if it occurs]. Review recommended before: [condition]."

Examples (brief, realistic):
1. Normal: "Task paused at schema migration step. Resume from `db/migrate/202610XX_add_column.rb` line 5. Next: test migration locally. Owner: next implementation session."
2. Blocked: "Blocked on release of gem X v2.0. Blocker: `Gemfile` currently locks v1.9, v2.0 hasn't been released (checked rubygems.org 2026-10-19). Cannot proceed without X v2.0 becoming available and security audit (human approves)."
3. Risk: "Task paused. Known risk: endpoint refactor affects 12 API consumers. If consumers not notified before release, will cause breaking changes for downstream. Risk level: HIGH. Mitigation: create task for consumer notification before merge. Review recommended before: production deployment."

- Note: Risk fields are optional. Use only if there's a real risk to surface.
- Note: This is not a risk *register*; it's context within each session's stop condition. Planning agent reads status.md entries to understand risks from recent work.
- Note: Risks here are *session-specific* (what one session discovered). Broader architectural risks belong in handoff files or task notes, not status.md.

### Complexity
**LOW**

Reason: 
- No structural changes to SESSION_CLOSEOUT.md
- Guidance expansion is 3–4 subsections with 4–5 examples
- No new files; no impact on other documents
- Fits naturally into existing "1b" section

### Suitable For
**Qwen (Planning Mode)** or **Human**

Reason: Documentation writing. Can review current SESSION_CLOSEOUT.md, understand the principle, and provide clear guidance examples.

### Requires Review
**YES**

Review scope:
- Guidance examples are realistic (not overly verbose)
- Examples show distinction between normal stop, blocked stop, and risk stop
- Guidance doesn't encourage over-documentation (risks should be actual, not speculative)
- Risk fields are clearly optional (doesn't create new overhead)
- Aligns with existing SESSION_CLOSEOUT.md principles (bounded scope, evidence-based)

---

## Change Set Summary

| # | Title | Target Files | Size | Complexity | Suitable For | Review? |
|---|-------|--------------|------|-----------|------------|---------|
| 1 | Evidence Classification | PLANNING_AGENT_SESSION_START.md | ~30 lines | LOW | Qwen / Human | YES |
| 2 | Reviewer Routing | PLANNING_AGENT_SESSION_START.md (same section) | ~10 lines | LOW | Qwen / Human | YES |
| 3 | Review Report Template | REVIEW_AGENT_WORKFLOW.md, SESSION_CLOSEOUT.md, (optional) new file | ~40 lines total | MEDIUM | Qwen / Human | YES |
| 4 | Stop Condition Enhancement | SESSION_CLOSEOUT.md | ~25 lines | LOW | Qwen / Human | YES |

**Total effort estimate:** 60–70 lines of documentation changes across 2–3 files + optional 1 new template file.

---

## Recommended Execution Order

### Phase 1: Planning Foundation (Change Sets 1 & 2)
**Combined task:** Update PLANNING_AGENT_SESSION_START.md with synthesis report guidance

- **Owner:** Qwen (Planning Mode) or Human
- **Changes:** Add new section covering both evidence classification and routing recommendation
- **Rationale:** These are closely related (routing uses evidence framework); combine to minimize context switching
- **Requires review:** YES (by Haiku, Plan Agent, or Human per strategy below)

### Phase 2: Review Clarification (Change Set 3)
**Tasks:** Update REVIEW_AGENT_WORKFLOW.md, SESSION_CLOSEOUT.md, optionally create template

- **Owner:** Qwen (Planning Mode) or Human
- **Changes:** Add artifact structure to REVIEW_AGENT_WORKFLOW.md; clarify handoff requirement in SESSION_CLOSEOUT.md
- **Rationale:** Independent of planning changes; provides clear artifact standard
- **Requires review:** YES (by Haiku or Plan Agent per strategy below)
- **Note:** Optional template file can be created in same session or deferred to Phase 3 if time is limited

### Phase 3: Stop Condition Guidance (Change Set 4)
**Task:** Enhance SESSION_CLOSEOUT.md § "1b"

- **Owner:** Qwen (Planning Mode) or Human
- **Changes:** Add risk/blocker surfacing guidance to stop condition section
- **Rationale:** Can be done independently; references guidance from earlier changes (helpful after Phase 1 complete)
- **Requires review:** YES (by Haiku or Plan Agent per strategy below)

---

## Review Strategy

### Who Reviews What

**Haiku (Claude) — Final Review Gate**
- Reviews all change proposals from implementation agents before merge
- Reviews: Final wording, alignment with system principles, unintended side effects
- Authority: Recommends GO/REVISE/STOP per change set
- **Change sets that should come to Haiku:**
  - All four (Haiku sees all documentation changes to ensure they preserve system coherence)

**Planning Agent — Intermediate Review (Optional)**
- Can review Phase 1 & 2 changes if available; helps catch clarity issues before Haiku
- Authority: Recommends improvements to guidance, catches contradictions with existing docs
- **Useful for:** Change Sets 1, 2, 3 (documentation clarification)
- **Not responsible for:** Final approval (Haiku does that)

**Human (Tracy) — Approval Authority**
- Approves merged documentation changes after Haiku's review
- Authority: Accepts or rejects Haiku's recommendations
- **Timing:** After Haiku completes review of all change sets in a phase

### Review Quality Gates

For each change set, Haiku should evaluate:

| Gate | Check | Guidance |
|------|-------|----------|
| **Principle alignment** | Does change preserve system design (generic, scalable, minimal overhead)? | STOP if adds per-project overhead or breaks existing patterns |
| **Clarity** | Is guidance clear enough for agents to follow without confusion? | Ask for examples if guidance is abstract; verify with realistic scenario |
| **Integration** | Does change contradict existing docs? Does it assume knowledge from other docs? | Check cross-references; ensure no circular dependencies |
| **Overhead** | Does change add maintenance burden on planning agents or implementation agents? | REVISE if adds new mandatory fields or artifact types without justifying necessity |
| **Completeness** | Are there unaddressed edge cases in the guidance? | Flag if guidance would confuse agents in realistic scenarios |

### Delegation Authority

**Qwen can implement Change Sets 1–4 without human pre-approval** (review comes after).  
**Rationale:** All changes are documentation improvements, not code or workflow logic changes. Review happens before merge, not before drafting.

**However:** If implementation agent (Qwen) is uncertain about intent or scope, pause and ask for clarification before drafting. Examples:
- Uncertainty about whether evidence framework should use different labels
- Uncertainty about which sections of SESSION_CLOSEOUT.md to modify
- Uncertainty about whether optional template file should be created

---

## Implementation Dependencies

**No hard blockers between change sets.**

Optimal ordering:
1. Phase 1 (Change Sets 1 & 2) — Foundation for later guidance
2. Phase 2 (Change Set 3) — Uses evidence framework from Phase 1
3. Phase 3 (Change Set 4) — Can reference all prior changes if needed

**Could be parallelized if multiple agents available:**
- Agent A: Phase 1 (Planning foundation)
- Agent B: Phase 3 (Stop condition) — runs independently
- Agent A/B: Phase 2 (Review template) — waits for Phase 1 if evidence clarification is helpful

---

## Implementation Checklist (for implementing agent)

### Before Starting
- [ ] Read this implementation plan end-to-end
- [ ] Read PLANNING_AGENT_SESSION_START.md to understand current structure
- [ ] Read REVIEW_AGENT_WORKFLOW.md to understand current guidance
- [ ] Read SESSION_CLOSEOUT.md § "1b" to understand current stop condition guidance
- [ ] Confirm which phase(s) you're implementing
- [ ] If unclear on any scope, ask before drafting

### During Implementation
- [ ] Create draft changes in a local editor (not yet in files)
- [ ] Read each draft against the "Expected Modifications" section above
- [ ] Verify draft doesn't contradict existing guidance in the file
- [ ] Verify examples are realistic (runnable, not hypothetical)
- [ ] **Do not commit or push changes**

### After Drafting
- [ ] Report to human/review agent with:
  - Draft text for each change
  - Location in file where change belongs
  - Reasoning if you deviated from plan
  - Any uncertainties or edge cases you discovered
- [ ] Wait for Haiku review before finalizing

---

## Known Considerations

### Optional Artifacts
- REVIEW_REPORT_TEMPLATE.md is optional; could be created now or deferred
- Rationale for creating: Separate template file helps review agents (copy-paste ready)
- Rationale for deferring: Keeps file count low; guidance in REVIEW_AGENT_WORKFLOW.md may be sufficient

Recommend: **Create it now** (low effort, high utility for agents who prefer standalone templates).

### Future Enhancements
These recommendations do NOT require now:
- Dedicated Risk Register in status.md — addressed by enhanced stop condition
- Synthesis Report Template file — not required; guidance in PLANNING_AGENT_SESSION_START.md sufficient
- Changes to REVIEW_AGENT_GUIDE.md — already complete; just referenced in planning guidance

---

## Next Steps

1. **Implement Agent** (Qwen or Planning Agent):
   - Reads this plan
   - Drafts changes for Phase 1 or assigned phase
   - Reports drafts to human/review agent

2. **Review Agent** (Haiku, Claude, or Planning Agent):
   - Reviews drafts against quality gates
   - Recommends GO/REVISE/STOP for each change set
   - Reports to human

3. **Human** (Tracy):
   - Reviews Haiku's recommendations
   - Approves or requests revisions
   - Authorizes merge to main branch
   - Commits and pushes changes

---

## Questions for Implementation Agent (if applicable)

Before starting, confirm:
1. Which phase(s) am I implementing?
2. If Phase 1: Should I combine Change Sets 1 & 2 into one section or keep them separate?
3. If Phase 2: Should I create the optional REVIEW_REPORT_TEMPLATE.md file now?
4. If Phase 3: Are there risk examples from current projects I should reference, or should I use generic examples?
5. Any guidance in this plan that seems unclear or conflicts with your reading of the current docs?

---

**End of Implementation Plan**

*This document is ready for transfer to implementation agent (Qwen, Planning Agent, or Human) for execution.*
