# Review & Planning Agent Guide

**Last Updated**: 2026-11-15  
**Applies To**: Any agent performing planning, research, implementation, or review sessions  
**Purpose**: Shared responsibilities, authority, and evidence standards

**Agent Selection Note**: Review agents are **web agents** (Claude, Grok, Gemini, ChatGPT, Perplexity). Local models (Qwen/Ollama) and GitHub Copilot are not used for review work. Review requires deep reasoning and judgment over codebase patterns; web agents have superior capabilities for this task.

---

## Session Activities, Not Permanent Identities

Planning, research, implementation, and review are authorized session activities, not permanent identities attached to particular agents. A session may perform any combination of these activities using its actual capabilities. Do not require one role per session.

---

## Reviewing Draft Tasks for Scope Approval (Review Agent — Web Agent Only)

**When assigned**: Human sends you to review draft task files that planning agents created and committed to git. Planning agent has recommended which review agent would best suit this task based on its needs; you were selected (or human chose from recommendations).

**Your role**: Assess whether the draft task is ready to move from `tasks/drafts/` to `tasks/backlog/`. You provide a GO/REVISE/STOP disposition that informs human approval decision.

### Review Package (Required Before Review)

Every draft review uses a **three-file package**. The three required artifacts must be attached or pasted into the review request, not supplied as URLs alone:

1. The complete draft task under review.
2. `TASK_TEMPLATE.md`.
3. This `REVIEW_AGENT_GUIDE.md`.

The request names the commit the copies came from, and the reviewer states that commit in the disposition. If any of these three artifacts is missing, ask for it. Do not review from memory or from a linked copy that cannot be confirmed.

**No fourth reviewer-template upload is required.** The former external reviewer template at `docs/TASK_REVIEWER_TEMPLATE.md` contains useful prompt and report guidance, but its requirements are incorporated into this guide. Reviewers must use this guide's current procedure and GO/REVISE/STOP dispositions; do not require the separate template as an attachment or treat its older PASS-style verdicts as authoritative.

#### Context to include in the review prompt (not additional uploads)

The person dispatching the review should put the following concise context directly in the prompt when applicable:

- **Task identification**: draft filename and path, agent-tasks repository, main application repository, and branch under review.
- **Review objective**: one or two sentences explaining what the task proposes to do and why it was created.
- **Established decisions**: relevant decisions from prior work that the draft must preserve. Clearly distinguish established decisions from hypotheses and questions still requiring verification.
- **Known evidence boundary**: identify any supplied logs, diffs, screenshots, handoffs, or command output, and say whether they are supplied evidence or independently verified.
- **Special review constraints**: task-specific scope limits, preservation requirements, environment cautions, or other gotchas that materially affect the review.

Do not upload separate project README, status, or handoff files by default. A reviewer with GitHub access may inspect the relevant project README, `status.md`, handoffs, and referenced repository files to cross-check context and current paths. These are optional repository checks, not additions to the three-file upload package. If the reviewer cannot access them, it must state that limitation and avoid claiming independent verification. Information available only on the project machine (command output, uncommitted files, container state) must be supplied as raw evidence by Qwen at the human's request; the reviewer asks for it when necessary and does not guess.

#### Review restrictions

This is a **task-specification review only**. The reviewer must not execute the task, modify the draft or project files, dispatch an implementation agent, commit, or push. It must not invent missing facts or assume the draft is correct because it reflects previous handoffs.

For each concern, explain why it matters and propose a precise correction. Separate:
- **Mandatory corrections**: defects that must be fixed before backlog approval.
- **Optional improvements**: useful refinements that do not block approval.
- **Human decisions**: unresolved scope, authority, architecture, or workflow choices that the reviewer must not decide unilaterally.

### Phase A — Template Conformance (Mandatory for GO)

Read TASK_TEMPLATE.md and compare every required section against the draft task file. **A template FAIL means the disposition cannot be GO. Record it, continue into Phase B in the same pass, and issue REVISE with one punch-list covering both phases.**

- **YAML frontmatter**: Includes `resume`, `system_domain`, `mvp_alignment`, `local_worker_safe` (and correct `status: draft`); when `status: draft`:
  - DRAFT STATUS NOTE section present
  - DRAFT REVIEW CHECKLIST section present
- **Agent Dispatch Interface**: Exact template block — not a custom/abbreviated substitute. Verify:
  - Step 0 `git mv` instruction present
  - Lifecycle rules (backlog → active → completed) documented
  - Summaries path specified
  - Synthesis contract included
  - No bracketed placeholders remain in the block. Run `grep -nE '\[(project_name|project|SUBFOLDER|FILENAME|FILL IN)\]' <task file>` and paste the raw output. Expected: exactly one line, the "One allowed substitution" sentence at the head of the block. Any other line is a FAIL (a leftover `[FILL IN]` means the fill pass was skipped). Only `<agent-tasks>` may remain until dispatch.
  - READ FIRST lists `rules/GUARDRAILS.md`, the project's `README.md` and `SESSION_GUIDANCE.md` (if the project has one), then the task file. A task drafted before 2026-11-05 that still has the earlier one-line READ FIRST passes this item; a new draft must use the current block
- **Task Readiness Checklist** section present

### Phase B — Content Assessment (always assessed, in the same pass as Phase A)

Assess all applicable areas and cite evidence for each PASS or positive conclusion:

- **Acceptance Criteria**: Are they clear, measurable, specific, and verifiable without ambiguity?
- **Task Scope**: Is the task appropriately bounded? Does it overlap with active/backlog tasks or imply unapproved scope?
- **Dependencies**: Are prerequisites and blocked/blocks relationships listed and, where possible, checked against `status.md` and active/backlog tasks?
- **Architecture Alignment**: Does the approach match current codebase patterns? Separate verified implementation facts from assumptions that must be checked locally.
- **No Contradictions**: Do task instructions, gotchas, acceptance criteria, file permissions, test requirements, and closeout steps agree?
- **Files Referenced**: Are paths accurate against repository content where accessible? Clearly label paths that could not be checked.
- **Testing Methodology**: Are the proposed tests appropriate to the behavior and risk? Are baselines, controls, repeatability, and environment constraints adequate where applicable?
- **Gotcha Completeness**: Are critical warnings, data-preservation rules, scope boundaries, lifecycle constraints, prerequisites, and environment/version precautions explicit?
- **Evidence Preservation**: Are required commands and evidence-capture instructions complete, reproducible, and copy/paste ready? Can the reviewer distinguish actual results from planned checks?
- **Review Gates and Authority**: Does the task require reviewer GO plus separate human authorization for commit, push, and completion? Are the restrictions clear?
- **Stop Conditions and Closeout**: Are escalation conditions, completion movement, documentation updates, and handoff requirements actionable and consistent?

**Required report format:**

Return a substantive, evidence-based report using this structure. A reviewer may add task-specific detail, but must not omit the disposition, findings, strengths, acceptance-criteria assessment, scope/workflow assessment, or mandatory/optional distinction.

```markdown
# DRAFT TASK REVIEW

## Task Identification
- **Task**: [filename]
- **Task path**: [repository path]
- **Project repository**: [repository]
- **Branch / revision reviewed**: [branch or commit, if known]
- **Review date**: YYYY-MM-DD
- **Reviewer**: [agent]
- **Package commit**: [commit the three attached artifacts came from]
- **Review type**: Task-specification review only

## 1. Disposition
**GO** | **REVISE** | **STOP**

[Short rationale. GO means ready for human approval; it does not approve backlog movement or authorize dispatch.]

## 2. Evidence Boundary
State what was directly inspected, what was supplied in the package or prompt, what was checked in GitHub, and what could not be verified. Do not describe supplied evidence as independent verification.

## 3. Phase A — Template Conformance
- **Result**: PASS | FAIL
- [Assess YAML/frontmatter and draft-only sections.]
- [Assess every required TASK_TEMPLATE.md section, exact dispatch block, placeholders, lifecycle, synthesis, readiness, review gate, commit instructions, completion report, and other applicable conventions.]
- [Quote the relevant text or provide command output for each PASS. A PASS without evidence is not checked.]

## 4. Phase B — Content Assessment
Assess acceptance criteria, scope, dependencies, architecture, contradictions, file paths, testing, gotchas, evidence preservation, review gates, stop conditions, documentation, and closeout. For each area state PASS / NEEDS CLARIFICATION / FAIL and give evidence.

## 5. Findings
Number every finding. For each include:
- **Severity**: Critical / Major / Minor / Informational
- **Task section**
- **Evidence / applicable convention**
- **Why it matters**
- **Specific recommended correction**

## 6. Strengths
Identify what the draft already handles correctly.

## 7. Acceptance Criteria Assessment
Assess each criterion for measurability, evidence, and completeness. Identify gaps or ambiguity.

## 8. Scope and Workflow Assessment
Assess boundaries, dependencies, investigation/implementation sequence, evidence handling, approval gates, stop conditions, and closeout authority.

## 9. Recommended Changes
Separate:
- **Mandatory corrections before approval**
- **Optional improvements that can be deferred**
- **Human decisions required**, if any

## 10. Final Recommendation
State whether the task should be approved for human consideration to move to backlog, revised and re-reviewed, or held for human guidance. Do not move the task or dispatch work.
```

**Outcomes:**
- `GO`: Task is ready for human approval to move to backlog/ and proceed to implementation dispatch
- `REVISE`: the planning agent fixes every item on the list in place in `drafts/`, and the reviewer re-checks only the listed items. If the re-check fails any listed item, issue REVISE again for the remaining items. A second failed re-check stops the task: Tracy decides, and may take it to the direct lane instead of a third round.
- `STOP`: Task has critical issues; recommend human guidance before proceeding

---

## Human: Include in Every Draft Review Dispatch

Copy the block below into every draft-review handoff prompt (human, planning agent, or automated dispatch). It ensures template compliance is always reviewed.

```
5. Template compliance (mandatory): Read TASK_TEMPLATE.md and compare every required section against the draft. Verify: all YAML fields (resume, system_domain, mvp_alignment, local_worker_safe), DRAFT STATUS NOTE, DRAFT REVIEW CHECKLIST, full Agent Dispatch Interface block (not abbreviated), Task Readiness Checklist. Flag any missing sections as REVISE requirements — do not GO if template is non-compliant.
6. Review package (mandatory): The task file, TASK_TEMPLATE.md and REVIEW_AGENT_GUIDE.md are attached or pasted below, with the commit they came from. If any is missing, ask for it. Do not review from memory or from a link.
```

**Where to use**: Include this in every draft-review dispatch prompt sent to a web review agent. Place it alongside your content/architecture review instructions so both are assessed together. If you already have a standard handoff template that ships review prompts (e.g., in PLANNING_AGENT_SESSION_START.md or any handoff template), add the fragment there as well and cross-link from this section.

---

## Authority

Tracy (human) authorizes priorities, assignments, dispatch, and scope changes. 

**Planning agents may recommend review agents** based on task needs and agent capabilities. Provide a brief rationale explaining why a particular web agent (Claude, Grok, Gemini, ChatGPT, Perplexity, Office 365 Copilot) would be best suited for assessing a specific draft task. The human can accept the recommendation, select a different agent, or override based on availability/context.

**Do not assume availability or auto-dispatch** newly drafted tasks — the human makes the dispatch decision.

### Reviewer Authority Over Commits

Reviewers have the following authority regarding commits:

- **GO/REVISE/STOP Dispositions** — reviewers may issue one of three dispositions on implementation work:
  - `GO` = implementation is ready for commit; human authorization still required before any git operations
  - `REVISE` = implementation has issues that must be fixed before review can approve
  - `STOP` = implementation has critical issues; no further work should proceed without human guidance
- **Assess readiness** — reviewers evaluate whether the implementation meets acceptance criteria and is ready for commit
- **Block commits** — by issuing REVISE or STOP, reviewers effectively block the path to commit until issues are resolved

Reviewers do NOT:

- **Commit directly** — reviewers never run `git commit` or `git push` on implementation work
- **Override human authorization** — even with a GO disposition, only Tracy (the human) may authorize commits and pushes
- **Bypass the review gate** — no commit may proceed without first satisfying the reviewer's disposition requirement

### Commit Authorization Chain

The correct flow for authorizing a commit is:

1. Implementation agent completes work → creates evidence artifacts
2. Agent requests reviewer assessment
3. Reviewer assesses and issues disposition (GO/REVISE/STOP)
4. If GO → human reviews and explicitly authorizes commit/push
5. Human runs git operations (or directs agent to run them)

If STOP or REVISE: steps 4-5 do not occur until issues are resolved.

### Scope of Review

Reviewers assess only what the task file specifies. They do not:

- Create new tasks (use TASK_TEMPLATE.md via planning agent)
- Implement code changes during review
- Run implementation tests (implementation agents verify their own work)

### Reviewer Authority

Reviewers exercise the following authorities during their review sessions:

- **Assess implementation work** — reviewers evaluate evidence artifacts, test results, and synthesis reports against task acceptance criteria
- **Issue dispositions** — reviewers may issue exactly one of three dispositions: `GO`, `REVISE`, or `STOP`
  - `GO` = work meets acceptance criteria; proceed to next step (still requires human authorization)
  - `REVISE` = work has issues that must be addressed before proceeding; implementation agent returns with fixes
  - `STOP` = work has critical issues; implementation stops and awaits human guidance
- **Request additional evidence** — reviewers may require more test runs, logs, or analysis before issuing a disposition
- **Reject synthesis reports** — if synthesis does not demonstrate understanding of the task or gotchas

Reviewers do NOT:

- Write code, run tests, or implement fixes during review
- Authorize commits, pushes, or completion (only humans may)
- Override another reviewer's disposition without human involvement

### Commit Authority

Commit authority follows a two-step chain that reviewers cannot bypass:

1. **Reviewer GO** — the reviewer assesses work and issues `GO`. This means "the work is ready for commit," NOT "commit it."
2. **Human Authorization** — Tracy (the human) explicitly authorizes the commit/push/completion. Even after reviewer GO, agents may not proceed without this second step.

| Action | Reviewer Can Do? | Human Can Do? | Implementation Agent Can Do? |
|---|---|---|---|
| Assess work for quality | ✅ Yes | ✅ Yes | ❌ No (not during review) |
| Issue GO/REVISE/STOP disposition | ✅ Yes | ✅ Yes | ❌ No |
| Authorize commit to repo | ❌ No | ✅ Yes | ❌ No (without human auth) |
| Authorize push to remote | ❌ No | ✅ Yes | ❌ No (without human auth) |
| Override reviewer GO | ✅ Not alone — requires human | ✅ Yes | ❌ No |
| Mark task as complete in status.md | ❌ No | ✅ Yes | ❌ No (without human auth after GO) |

### Scope of Review

Reviewers assess only what the task file specifies. They do not:

- Review unrelated parts of the codebase
- Issue dispositions on work that was not part of the assigned task
- Expand or contract the task scope without human involvement

## Evidence Standards

Distinguish three evidence types in all reports:
- **Direct verification** — what you checked yourself this session
- **Supplied evidence** — pasted logs, diffs, handoffs, or other content provided to you; describe conclusions as "review of supplied evidence," not independent live-repository verification
- **Dated claims** — last-known information from status.md or prior handoffs; clearly label as dated and note when it was last confirmed

## Output

Save substantive output (reports, findings, reviews, handoffs) to files when the agent has an authorized writable workspace. **External web reviewers without filesystem access return the complete report in their response** using the required report format above; they must not claim to have saved a file. The human or planning agent may then preserve the returned report in the appropriate summaries/review location. For agents that can save files, report briefly in chat with the outcome, artifact location/name, and any decision needed. Do not paste full artifacts into chat unless the review was performed by an external reviewer without file access or the human requests the full report.

## Escalation

Escalate consequential risks and decisions: architectural conflicts, material scope changes, ownership conflicts, unresolved safety risks, or choices that require Tracy's judgment. Do not escalate ordinary details, stale metadata, or wording differences.

## Referenced Procedures

- **Startup**: `<agent-tasks>/PLANNING_AGENT_SESSION_START.md` — state reconciliation, test-log check, opening briefing
- **Review workflow**: `<agent-tasks>/REVIEW_AGENT_WORKFLOW.md` — assignment → evidence → targeted review → disposition
- **Closeout**: `<agent-tasks>/SESSION_CLOSEOUT.md` — status.md maintenance, task lifecycle reconciliation, artifact cleanup

Do not duplicate these procedures in this guide.

---

## Model/Tool Configuration Notes (Reference Only)

These notes document local agent setup for context. They are nonbinding reference material and do not determine workflow authority.

**Copilot Agent Setup** (for local executor agents):
- Custom agent file at `~/.../globalStorage/github.copilot-chat/`
- Tool use requires: `chat.permissions.default: bypassApprovals`
- Model selection: qwen3.6:27b (primary) — do not switch mid-session
- Tool use verification: working shows `Ran ls...` as UI element; broken shows JSON/XML text

**Continue Setup** (fallback for local executors):
- Config: `~/.continue/config.yaml`
- Endpoints: M4 (`http://10.6.186.161:11434`), Ryzen 7 (`http://10.6.186.50:11434`)
- Limitation: Manual approval per terminal command; use VS Code editor for file edits

**Known Failure Modes**:
- Model switching mid-session: causes context accumulation, token limit errors
- `sed` for file edits: dangerous, causes corruption — use Continue's apply feature
- No custom `.agent.md`: new Copilot windows print tool calls as text instead of executing

---

## Quick Reference — Generic Role Definition

| Role | You Are | You Do | You Don't |
|---|---|---|---|
| **REVIEWER** | Quality gate before execution | Read synthesis, flag risks, approve/reject | Write code, run tests, commit |
| **PLANNING** | Strategy coordinator | Draft task files, sequence work, create handoffs | Self-assign implementation, make unilateral decisions |
| **STRATEGIST** | Session architect | Triage blockers, update status.md, assign work | Implement code, skip reading project guide |

All agents follow these shared patterns.

---

## When to Escalate

**Do NOT attempt**:
- Implementing code fixes (use EXECUTOR agents)
- Running tests to validate work (use EXECUTOR agents)
- Debugging test failures deeply (use EXECUTOR agents — they have terminal access)

**DO escalate if**:
- Task file seems ambiguous or self-contradictory
- Synthesis report shows fundamental misunderstanding of architecture
- Previous session's work doesn't match completion notes
- More than 2 synthesis reports rejected for same task

---

## Quick Tips

- **Always start with "Read these files first"** — don't assume shared context
- **Create a STATUS REPORT after reading files** — confirms alignment before proceeding
- **Session handoff documents are continuity** — future agents depend on them
- **Task files are the source of truth for executor** — don't repeat in chat, link instead
- **Gotchas in task file are critical** — review agents should flag if executor misses them
