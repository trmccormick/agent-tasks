# External Task Reviewer Template

**Use this template when requesting review from external LLM agents** (Grok, Claude Sonnet, ChatGPT Plus) that can access GitHub directly but do not have filesystem access.

**Reference documents:**
- `REVIEW_AGENT_GUIDE.md` — Review agent procedures, Phase A/B criteria, GO/REVISE/STOP dispositions
- `TASK_TEMPLATE.md` — Required task file structure and conventions

---

## Task Identification

**Draft task**: `[agent-tasks]/projects/[project]/tasks/drafts/[FILENAME].md`  
**Repository**: `[GitHub org/user]/agent-tasks` (e.g., `trmccormick/agent-tasks`)  
**Main application repository**: `[GitHub org/user]/[app-repo]` (e.g., `tam0013/wvu_knapsack`)  
**Branch under test**: `[branch-name]`

---

## 1. Review objective

Independently assess whether this task is sufficiently clear, complete, appropriately scoped, and consistent with the project's established task conventions to be approved for backlog.

**[INSERT ONE SENTENCE HERE: What does this task do? Why was it created?]**

Example:
> This task proposes a local investigation of a critical Hyku 7.2.0 search bug via a structured testing plan on Stack Car (disposable local Docker environment). The goal is to pinpoint the exact file/line causing a Solr collection mismatch.

---

## 2. Required review

Read the complete draft task and relevant project guidance. At minimum, inspect:

* `agent-tasks/TASK_TEMPLATE.md` — required structure, YAML frontmatter, Agent Dispatch Interface
* `agent-tasks/REVIEW_AGENT_GUIDE.md` — Phase A (template conformance), Phase B (content assessment), dispositions
* `projects/[project]/README.md` — project context
* `projects/[project]/status.md` — current progress tracking
* Any relevant handoff documents from the previous session
* The draft task file itself

**Verify against actual repository content.** Do not assume the draft is correct simply because it was created with reference to previous guidance.

---

## 3. Evaluation criteria

Check these areas when reviewing the draft:

1. **Template conformance**: Does the file start with proper YAML frontmatter (not text before `---`)? Are all required TASK_TEMPLATE.md sections present in correct order?
2. **Agent Dispatch Interface lifecycle compliance**: Does dispatch reference `tasks/backlog/` as source? Step 0 moves backlog→active via git mv? Closeout references SESSION_CLOSEOUT.md?
3. **Scope control**: Is the task appropriately bounded? Are acceptance criteria limited to verification/diagnostic outputs (not open-ended fixes)?
4. **Architecture accuracy**: Do described components, file paths, and workflows reflect actual codebase structure?
5. **Testing methodology** (if applicable): Does the plan provide adequate scientific controls (identical queries across versions, proper baselines)?
6. **Gotcha completeness**: Are critical warnings about disposable state, data persistence, scope boundaries clearly stated?
7. **Evidence preservation**: Are commands for saving diagnostic output complete and copy/paste ready?
8. **Acceptance criteria**: Are they measurable? Can a reviewer verify each criterion without ambiguity?
9. **Dependencies/gotchas**: Are lifecycle constraints, prerequisites, and version-switching precautions clearly stated?
10. **Task conventions compliance**: YAML metadata, draft status, dispatch-interface requirements, approval gates, Stop Conditions, Commit Instructions — all per TASK_TEMPLATE.md?
11. **Review gate clarity**: Does the task correctly require BOTH reviewer GO disposition AND separate human authorization for commit AND separate human authorization for push?

---

## 4. Important design decisions to preserve

**[LIST ANY ESTABLISHED DECISIONS FROM PREVIOUS SESSIONS THAT THE DRAFT MUST REPRESENT ACCURATELY]**

Example entries:
* The collection mismatch affects both v7.1.3 and v7.2.0 — not a v7.2.0-only regression.
* Stack Car is a safe, disposable environment — `docker compose down --volumes` before version switches.
* This task is investigation only — no code changes. The fix strategy comes AFTER testing findings.
* Valkyrie indexer writes to UUID-based Solr collection; Blacklight CatalogSearchBuilder queries hydra-production. Both use SolrEndpoint for resolution but may disagree on the collection name.

If no special design decisions apply, delete this section heading and content.

---

## 5. Review restrictions

This is a review of the **task specification only**.

* Do NOT execute the task.
* Do NOT create or modify any files in the project repository.
* Do NOT modify the task draft.
* Do NOT dispatch Qwen or another implementation agent.
* Do NOT commit or push changes.
* Do NOT invent missing facts, decisions, or architectural details.
* Do NOT assume the draft is correct simply because it was created with reference to previous handoff documents.

If a requirement is missing, explain **why it matters** and propose a precise correction. Distinguish between:

* Actual defects that should be corrected before approval (critical blockers).
* Minor improvements that would be helpful but should not block approval.
* Optional suggestions that can be deferred.

---

## 6. Required review disposition

Return one of these explicit verdicts:

* **PASS** — The task is suitable for backlog approval without changes.
* **PASS WITH MINOR CORRECTIONS** — Suitable for approval after small, clearly identified edits.
* **REVISE** — Material deficiencies must be corrected before approval.
* **STOP** — Fundamental scope, authority, or workflow problems prevent approval.

---

## 7. Required report format

### Verdict
One of the four explicit dispositions above.

### Findings
Number each finding. For every finding include:
* Severity: Critical / Major / Minor / Informational
* Relevant task section
* Evidence or applicable project convention (TASK_TEMPLATE.md line, REVIEW_AGENT_GUIDE.md rule, actual wvu_knapsack file)
* Why it matters
* Specific recommended correction

### Strengths
Identify what the task already handles correctly — accurate architecture descriptions, well-structured phases, appropriate scope boundaries, etc.

### Acceptance Criteria Assessment
Assess whether acceptance criteria are measurable and sufficient. Identify any gaps or ambiguities.

### Scope and Workflow Assessment
Confirm whether investigation-only constraints, version-switching precautions, data-preservation steps, review gates, and Stop Conditions are clear and adequate.

### Recommended Changes
Separate **mandatory corrections** from **optional improvements**.

### Final Recommendation
State whether the task should be approved for backlog, revised and re-reviewed, or held pending human clarification.

---

**Be independent and evidence-based.** Do not approve the task merely to be agreeable. Preserve the distinction between:

* Documented facts (previous handoff context)
* Hypotheses (theories that need testing/verification)
* Required verification (what testing will prove)
