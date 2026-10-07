# Review & Planning Agent Guide

**Last Updated**: 2026-10-06  
**Applies To**: Any agent performing planning, research, implementation, or review sessions  
**Purpose**: Shared responsibilities, authority, and evidence standards

---

## Session Activities, Not Permanent Identities

Planning, research, implementation, and review are authorized session activities, not permanent identities attached to particular agents. A session may perform any combination of these activities using its actual capabilities. Do not require one role per session.

## Authority

Tracy authorizes priorities, assignments, dispatch, and scope changes. Sessions may recommend available agents or session arrangements with a brief rationale (capabilities, context needs, availability, cost), but do not assume availability or auto-dispatch newly drafted tasks.

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

- **Startup**: `/Users/tam0013/Documents/git/agent-tasks/PLANNING_AGENT_SESSION_START.md` — state reconciliation, test-log check, opening briefing
- **Review workflow**: `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_WORKFLOW.md` — assignment → evidence → targeted review → disposition
- **Closeout**: `/Users/tam0013/Documents/git/agent-tasks/SESSION_CLOSEOUT.md` — status.md maintenance, task lifecycle reconciliation, artifact cleanup

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
