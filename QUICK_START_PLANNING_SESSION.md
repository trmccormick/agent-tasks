# Quick Start — Planning Session Dispatch
**Your go-to tile for dispatching planning/review agents**

---

## Copy-Paste Dispatch Template

```
You are the PLANNING/REVIEW Agent for [PROJECT] in this session.

Read these files IN ORDER:
1. /Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md
2. /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md
3. [Paste status.md]
4. [Paste previous handoff]

YOUR ASSIGNMENT TODAY:
[e.g., "Triage 50 GitHub issues and prioritize top 5 for next sprint"]
[e.g., "Review synthesis reports for issues #2990 and #2991"]
[e.g., "Plan task queue for Luna Phase - Game AI implementation"]

Start by creating a STATUS REPORT in chat confirming your understanding.
```

Replace:
- `[PROJECT]` → galaxy_game, samvera_hyku, wvulibraries_knapsack, etc.
- `[YOUR ASSIGNMENT TODAY]` → specific work (triage, review, plan, etc.)

---

## What Planning Agent Does

1. **Reads** generic guide + project context + status + handoff
2. **Creates STATUS REPORT** in chat (confirms understanding)
3. **You approve** or clarify understanding
4. **Performs work**: Triages, reviews, plans, identifies risks
5. **Creates task files** for implementation (using TASK_TEMPLATE.md)
6. **Creates SESSION HANDOFF** document (saved to `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`)

---

## What You Get Back

- ✅ Synthesis reviews (with approval gates)
- ✅ New task files (ready for executor)
- ✅ Updated status.md (progress notes)
- ✅ SESSION HANDOFF document (continuity for next session)

---

## Task File Format (What Agent Generates)

Planning agents create minimal task files using [TASK_TEMPLATE.md](TASK_TEMPLATE.md):

```markdown
---
title: [PRIORITY-TYPE-BRIEF-TITLE]
status: backlog  # or active/completed
priority: HIGH | MEDIUM | LOW
type: BUGFIX | FEATURE | REFACTOR | RESEARCH
assigned_to: [qwen27b | tbd]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# [Title]

## Prerequisites
- Read: [File 1]
- Read: [File 2]
- Understand: [Architecture concept]

## Problem Statement
[2-3 sentences: what's broken or needed]

## Acceptance Criteria
- [ ] [Must do X]
- [ ] [Must verify Y]
- [ ] [Must test Z]

## Implementation Notes
[Any gotchas, architecture decisions, edge cases]

## STATUS SYNTHESIS REPORT
[Executor fills this in before implementing]

Executor must post this to chat and WAIT for approval before coding.
```

---

## Quick Checklist — Before You Dispatch

- [ ] Know what PROJECT you're working on
- [ ] Know what ASSIGNMENT (triage / review / plan / etc.)
- [ ] Have status.md ready to paste
- [ ] Have previous handoff ready to paste
- [ ] Generic guide available at: `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md`
- [ ] Project README available at: `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md`

---

## Session Output — What to Save

After planning session completes, save:

1. **Task files** agent created → `projects/[PROJECT]/tasks/backlog/YYYY-MM/YYYY-MM-DD-PRIORITY-TYPE-NAME.md`
2. **Updated status.md** → `projects/[PROJECT]/status.md` (agent provides updated version)
3. **Session handoff** → `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`

---

## Next: Dispatch Implementation

Once planning session is done:

```
You are IMPLEMENTATION Agent for [PROJECT].

Read these files IN ORDER:
1. /Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md
2. /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md
3. [Paste task file from planning session]

REQUIRED: Create STATUS SYNTHESIS REPORT before coding (template in task file).

Post your synthesis to chat and wait for approval before implementing.
```

---

## Common Projects

- `galaxy_game` — Luna Phase AI manager implementation
- `samvera_hyku` — Multi-tenant repository platform (Hyrax, Fedora, Solr)
- `wvulibraries_knapsack` — Similar to Hyku, WVU-specific
- `samvera_hyrax` — Core Hyrax framework work

---

## Files This References

- [REVIEW_AGENT_GUIDE.md](REVIEW_AGENT_GUIDE.md) — Generic guide planning agents read first
- [TASK_TEMPLATE.md](TASK_TEMPLATE.md) — Template for minimal task files
- [REVIEW_AGENT_WORKFLOW.md](REVIEW_AGENT_WORKFLOW.md) — Full workflow (for reference)
- `projects/[PROJECT]/README.md` — Project-specific context
- `projects/[PROJECT]/status.md` — Current state (progress tracking)
- `projects/[PROJECT]/handoffs/` — Session handoffs (continuity)
