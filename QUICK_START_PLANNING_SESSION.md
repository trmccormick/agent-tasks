# Quick Start — Planning Session Dispatch

A short copy-paste dispatch prompt for planning/review sessions.

---

## Dispatch Prompt

```
You are a planning or review agent for [PROJECT] in this session.

Read /Users/tam0013/Documents/git/agent-tasks/PLANNING_AGENT_SESSION_START.md and follow it using your actual access capabilities.

YOUR ASSIGNMENT: [e.g., "Triage backlog issues" / "Review synthesis reports for #2990"]
or: "Establish current state and await assignment."

[Optional: Tracy's current agent availability/preferences — e.g., "Qwen available, Claude on hold"]

Provide a concise opening briefing before substantive assigned work.
```

---

## Usage Notes

- **Replace** `[PROJECT]` and `[YOUR ASSIGNMENT]` with your values.
- **Sessions without repository access**: Tracy supplies the referenced startup instructions (`PLANNING_AGENT_SESSION_START.md`) and relevant project evidence (README, status.md, handoffs) as pasted content.
- **Do not** make task creation or a fresh full-suite run automatic — Tracy decides.
- For detailed startup procedure, evidence standards, and closeout rules, follow the referenced startup file rather than this quick-start guide.

---

## Files Referenced

- `/Users/tam0013/Documents/git/agent-tasks/PLANNING_AGENT_SESSION_START.md` — Full startup procedure (state reconciliation, test-log check, closeout)
- `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md` — Responsibilities and evidence standards
- `/Users/tam0013/Documents/git/agent-tasks/SESSION_CLOSEOUT.md` — Closeout procedure
