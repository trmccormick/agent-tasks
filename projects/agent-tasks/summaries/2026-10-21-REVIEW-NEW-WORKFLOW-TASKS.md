### Review: two workflow tasks created 2026-10-21 (planning-agent diagnostic workflow; closeout review-gate enforcement)

**Assignment**: Review the two backlog tasks `2026-10-21-MEDIUM-PLANNING-AGENT-DIAGNOSTIC-WORKFLOW.md` and `2026-10-21-MEDIUM-DOCUMENTATION-SESSION-CLOSEOUT-REVIEW-GATE-ENFORCEMENT.md` before dispatch, against Tracy's 2026-10-19 and 2026-10-20 decisions (handoff name `<date>-<type>-CLOSEOUT.md`, folder `claude-free-web`, GO / REVISE / STOP, agents commit when Tracy says so). Decision requested: dispatch as written, or revise first?

**Evidence Basis**: read-only clone of origin at `cbfd092`, 2026-10-21. Both task files read in full; insertion points checked with `grep -n` against `PLANNING_AGENT_SESSION_START.md`, `rules/GUARDRAILS.md`, `TASK_TEMPLATE.md`, `SESSION_CLOSEOUT.md`, `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md`; word counts by script (all VERIFIED). Not reviewed: the galaxy-game-ui files, the Dispatch Agent proposal in that project's NEEDS_REVIEW, the planning closeout beyond its header. The cause I give for the planner overstep is INFERRED.

## Review 1: Planning-agent diagnostic workflow

**Findings**:
1. No Agent Dispatch Interface block and no readiness checklist (`grep -c` returns 0); `TASK_TEMPLATE.md` says such a task is not ready for dispatch (VERIFIED).
2. Two of the three insertion points do not exist. `TASK_TEMPLATE.md` has no "When to Create a Task File" section, and `GUARDRAILS.md` has no "Multi-Agent Task Governance" heading; its rules are MAG-1 to MAG-6, so "MAG-P" fits no numbering. Only "Step 1 — Confirm your role and project" exists (`PLANNING_AGENT_SESSION_START.md:19`) (VERIFIED).
3. The same rule would be written three times (session start, GUARDRAILS, TASK_TEMPLATE). A planner-boundary rule already has a home: `docs/PLANNER_ROLE_BOUNDARIES.md` (kept in the 2026-10-20 decisions), and no planning document links to it (VERIFIED by grep), which is the likelier cause of the overstep (INFERRED). `PLANNING_AGENT_SESSION_START.md:27` already says "research and preparation, not implementation".
4. The proposed MAG-P text lets a planner "commit documentation-only changes" with no approval. Under Rule 26 and the 2026-10-20 decision, an agent commits when Tracy tells it to (VERIFIED against `GUARDRAILS.md:519-534`). It also lets a planner put a task in `active/` when "urgent", but moving to `active/` is the executor's Step 0.
5. Its edits collide with other work: `GUARDRAILS.md` (the lifecycle and Rule 26 task), `TASK_TEMPLATE.md` (the unsent contradictions draft) and `PLANNING_AGENT_SESSION_START.md` (the link to the role doc).

**Disposition**: REVISE

**Rationale**: The problem is real and the checkpoint wording ("diagnose, task file, human GO, execute") is good. But the task is not dispatchable, two edits have no target, and it duplicates a rule that has a home.

**Recommendations**: Put the diagnose-then-stop rule once in `docs/PLANNER_ROLE_BOUNDARIES.md`; add one pointer line to `PLANNING_AGENT_SESSION_START.md`; drop the GUARDRAILS and TASK_TEMPLATE inserts or reduce each to a one-line pointer. Add the dispatch block and checklist. Say "commits only when Tracy tells it to". Sequence after the lifecycle and guides tasks.

## Review 2: Session-closeout review-gate enforcement

**Findings**:
1. Location errors. It says insert at "line ~130" of `SESSION_CLOSEOUT.md`, but the gate section is at lines 46-69 and line 130 is inside "1. Status.md Maintenance". It names a "Section 0a" and "0b" in `IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md`; the file has only "0. Scope" and "1. Task file" (VERIFIED).
2. It duplicates the existing gate. `SESSION_CLOSEOUT.md:46-68` already lists the six steps and a CANNOT / MAY table. The task's own notes say to keep each insert under 200 words, while its inserts are 365 and 238 words (VERIFIED by script).
3. It contradicts that table: the table says an agent MAY update `status.md` with an "awaiting human authorization" note and save evidence artifacts, while the new text forbids updating `status.md`, creating artifacts, and moving or renaming any file (VERIFIED).
4. Old and inconsistent names. The SESSION_CLOSEOUT insert uses `<date>-TYPE-SESSION.md`, a third place for the pattern Tracy replaced. The implementation insert uses `handoffs/implementation/YYYY-MM-DD-IMPLEMENTATION-[TASK-NAME].md`, another pattern and folder. The synthesis path is written two ways, and the dispatch block uses a third (VERIFIED).
5. Wrong role. The dispatch block says "You are **Review Agent**" for a documentation-edit task. `REVIEW_AGENT_GUIDE.md` says reviewers do not implement; the 2026-10-19 tasks used "Documentation Agent" (VERIFIED).
6. "Reviewer disposition (from reviewer or human)" blurs the guide's rule that the reviewer issues the disposition and the human authorizes.

**Disposition**: REVISE

**Rationale**: The mental-model point ("your work is done, stop") is a useful finding, and GO / REVISE / STOP matches the decision. But the edits as written contradict the existing gate text and the naming decision, and the targets are misplaced.

**Recommendations**: Rewrite as a short tightening of the existing gate section (under 200 words), using the existing table and `-CLOSEOUT.md` names. Keep the `status.md` "awaiting authorization" allowance. Use a Documentation Agent. Put the reviewer-issues / human-authorizes wording exactly as the guide has it. Sequence after the review-docs task.

**Cross-task**: dispatch neither until revised. Both predate the guides and lifecycle tasks that touch the same files, so order the work: dispositions and evidence labels, then lifecycle and GUARDRAILS, then guides and Continue, then these two revised tasks.
