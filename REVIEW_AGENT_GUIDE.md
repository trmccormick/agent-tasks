# Review & Planning Agent Guide

**Last Updated**: 2026-10-06  
**Applies To**: Any agent performing planning, research, implementation, or review sessions  
**Purpose**: Shared responsibilities, authority, and evidence standards

---

## Session Activities, Not Permanent Identities

Planning, research, implementation, and review are authorized session activities, not permanent identities attached to particular agents. A session may perform any combination of these activities using its actual capabilities. Do not require one role per session.

## Authority

Tracy authorizes priorities, assignments, dispatch, and scope changes. Sessions may recommend available agents or session arrangements with a brief rationale (capabilities, context needs, availability, cost), but do not assume availability or auto-dispatch newly drafted tasks.

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

Save substantive output (reports, findings, reviews, handoffs) to files. Report briefly in chat with a short outcome, artifact location/name, and any decision needed. Do not paste full artifacts into chat unless requested.

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
