# Review Agent Dispatch Workflow
**Last Updated**: 2026-06-23
**Applies To**: Claude (free), Gemini (web), Perplexity (free), or any review/planning agent
**Purpose**: Standardized workflow for multi-agent review, planning, and handoff

---

## The Complete Cycle (Overview)

```
SESSION N-1: Executor completes work
   ↓
   Creates SESSION HANDOFF document
   
SESSION N: Review Agent Dispatched
   ↓
   Receives: Generic guide + Project context + Previous handoff + Task files
   ↓
   Creates STATUS REPORT (confirms understanding)
   ↓
   Reviews synthesis reports / Drafts task files / Flags risks
   ↓
   Creates NEW SESSION HANDOFF document (what to do next)
   
SESSION N+1: Next agent (review or executor) picks up
   ↓
   Receives: Generic guide + Project context + Previous handoff (N)
   ↓
   No need to read session N-1 — N contains everything needed
```

---

## What a Review Agent Receives (Dispatch Package)

When you dispatch a review/planning agent, you provide:

```
[DISPATCH MESSAGE]

You are REVIEWER Agent for [PROJECT].

Read these files (in order):

1. /Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md
   → Generic setup, role definition, session workflow

2. /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md
   → Project-specific context (can be pasted or file read)

3. /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/status.md
   → Current state: completed work, active focus, blockers (pasted)

4. /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md
   → Previous session work: what was done, what's next (pasted)

5. Task files / Synthesis reports / Code for review (as needed)

Your job TODAY:
[SPECIFIC ASSIGNMENT]

Start by reading all files in order, then create a STATUS REPORT in chat.
```

**All context arrives in chat via paste** — review agents have no file system access.

---

## What a Review Agent Does (Typical Session)

### Phase 1: Intake & Alignment
1. Read generic guide (understand role and workflow)
2. Read project context (README + status.md)
3. Read previous session handoff (understand continuity)
4. Read task files or synthesis reports (understand work to review)
5. Create **STATUS REPORT** in chat:
   - What I understand the project state to be
   - What executor is currently working on
   - What risks or gaps I see
   - What I'm about to do

### Phase 2: Review & Planning
- Review executor's synthesis reports for completeness, risks, edge cases
- Draft new task files for upcoming work (if needed)
- Flag architectural concerns, gotchas, or ambiguities
- Recommend task sequencing or priority adjustments
- Update project status.md with progress notes

### Phase 3: Synthesis Review (Gate Approval)
- **Tier 1 Review**: User approves or rejects executor's synthesis
- **Tier 2 Review** (if free time): Deeper architecture review
- Document approvals / rejections in synthesis
- Flag issues that executor should fix before implementing

### Phase 4: Documentation
- Create **SESSION HANDOFF** document
- Record: what was accomplished, what's next, open questions, risks
- Save at: `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`
- Include: recommendation for next executor and top priorities

---

## Session Handoff Document (Template)

**Location**: `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`

```markdown
# Session Handoff — [PROJECT] — [DATE]

**Role**: REVIEWER / PLANNING
**Assigned To**: [Claude / Gemini / Perplexity]
**Previous Session**: [reference]
**Next Session Should Focus On**: [recommendation]

---

## Summary
[2-3 sentences: what was accomplished this session]

---

## Completed Work

- ✅ [Task]: [What was done]
- ✅ [Task]: [What was done]

---

## Synthesis Reviews

| Task | Status | Notes |
|---|---|---|
| Issue #XXXX | Approved ✅ | Ready for implementation |
| Issue #YYYY | Flagged ⚠️ | Executor missed gotcha X |

---

## Gotchas & Risks Identified

- ❌ [Risk]: [What to watch]
- ⚠️ [Edge case]: [What to handle]

---

## Open Questions

- ? [Question]: [Needs clarification]

---

## Files Modified

- `projects/[PROJECT]/tasks/active/[FILE].md` — Created/updated
- `projects/[PROJECT]/status.md` — Updated progress

---

## Recommendations for Next Session

**PRIORITY 1**: [What to do first]
**PRIORITY 2**: [What comes next]
**BLOCKER**: [If any]

---

## Executive Summary for Next Agent

**If next session is an Executor**:

```
You are Implementation Agent for [PROJECT].

Previous session review: [reference this handoff]

TODAY:
1. Read generic guide: /Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md
2. Read project README
3. Read task file: [FULL PATH]
4. Create synthesis report (template in task file)
5. Wait for approval before implementing

Key gotchas from review:
- [Gotcha 1]
- [Gotcha 2]
```

**If next session is another Reviewer**:

```
You are REVIEWER Agent for [PROJECT].

Previous session review: [reference this handoff]

TODAY:
1. Read this handoff (above)
2. Read project context (status.md + README)
3. Review [TASK] synthesis report
4. Flag risks / approve or reject
5. Create new handoff for next session
```
```

---

## Multi-Session Continuity (Chain of Handoffs)

The handoff chain ensures no context is lost:

```
Session 1 (Review):
  Input: Generic guide + Project context + Previous work summary
  Output: Synthesis review + Task file + Session handoff

Session 2 (Executor):
  Input: Generic guide + Project context + Session handoff from Session 1 (only!)
  Output: Implementation + Test results + Completion notes

Session 3 (Review):
  Input: Generic guide + Project context + Session handoff from Session 2 (only!)
  Output: Synthesis review + Updates + Session handoff

Session 4 (Executor):
  Input: Generic guide + Project context + Session handoff from Session 3 (only!)
  Output: Implementation + Tests + Completion
```

**Key rule**: Each session reads only the most recent handoff. No need to read Session 1 in Session 3 — Session 2's handoff contains everything needed.

---

## Dispatch Instructions (For You, The User)

When dispatching a review agent:

1. **Prepare the package**:
   - Generic guide: `/Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md`
   - Project README: `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md`
   - Project status.md: `/Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/status.md`
   - Previous handoff: `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`
   - Task files or synthesis reports (if applicable)

2. **Send the message**:
   ```
   You are REVIEWER Agent for [PROJECT].
   
   [Assignment and instructions]
   
   Read these files first (pasted below):
   [Paste: generic guide excerpt + paths]
   
   [Paste: project README]
   [Paste: status.md]
   [Paste: previous handoff]
   [Paste: synthesis reports / task files as needed]
   
   Start by creating a STATUS REPORT in chat.
   ```

3. **Review agent creates STATUS REPORT** in chat before proceeding

4. **You approve or adjust** the agent's understanding

5. **Agent proceeds** with review / planning / drafting

6. **Agent creates SESSION HANDOFF** document at end of session

7. **You save the handoff** to: `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`

8. **Next session starts** by reading that handoff

---

## Common Mistakes to Avoid

| ❌ Mistake | ✅ Correct Approach |
|---|---|
| Not reading files in order | Always: generic → project → status → handoff → task files |
| Pasting 10 old handoffs | Only paste most recent handoff — it contains everything needed |
| Skipping STATUS REPORT | Always create STATUS REPORT before proceeding — confirms alignment |
| Not creating SESSION HANDOFF | Every session must produce a handoff for continuity |
| Mixing roles (review + implement) | One role per session — don't review and code in same session |
| Ambiguous handoff recommendations | Be specific: "Priority 1: Implement X because Y" not "keep working" |
| Forgetting to update status.md | Update status.md in each session — it's the single source of truth |
| Saving handoff to wrong location | Always: `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md` |
| Restating full handoff/commit detail in status.md | Reference the commit hash or handoff filename instead — don't duplicate narrative |

---

## Quick Reference — Review Agent Checklist

- [ ] Read generic guide (REVIEW_AGENT_GUIDE.md)
- [ ] Read project README
- [ ] Read project status.md
- [ ] Read previous session handoff
- [ ] Read task files / synthesis reports
- [ ] Create STATUS REPORT in chat
- [ ] Wait for user approval on understanding
- [ ] Complete assigned review / planning work
- [ ] Create SESSION HANDOFF document
- [ ] Include recommendations for next session
- [ ] Handoff saved at: `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`

---

## Why This Workflow Scales

✅ **Generic guide is reusable** across all projects (Galaxy Game, Samvera, WVU, etc.)
✅ **Project context is isolated** — project README, status.md, architecture docs live in project
✅ **Session handoffs chain** — no need to read old sessions, only most recent
✅ **Single source of truth** — status.md is the canonical state
✅ **Roles are clear** — review agents don't implement, executors don't plan
✅ **Synthesis gates work** — review agents catch issues before executor codes
✅ **Context is explicit** — all info is pasted/provided, no guessing or assumptions
✅ **Continuity is automatic** — handoffs preserve context across sessions

---

## Example: Full Three-Session Cycle

**SESSION 1 — Review Agent (Claude)**
```
Dispatch: "Review synthesis reports for Issue #2990"
Receives: Generic guide + project context + previous work
Creates: STATUS REPORT → Reviews synthesis → Flags gotchas → Approves task
Produces: session_handoff_2026-06-23_BATCH_EDIT_REVIEW.md
```

**SESSION 2 — Executor (qwen27b)**
```
Dispatch: "Implement batch edit fix"
Receives: Generic guide + project context + session_handoff_2026-06-23_BATCH_EDIT_REVIEW.md
Creates: Synthesis → Implements → Tests → Moves task to completed
Produces: session_handoff_2026-06-24_BATCH_EDIT_IMPLEMENTATION.md
```

**SESSION 3 — Review Agent (Gemini)**
```
Dispatch: "Spot-check implementation and plan next work"
Receives: Generic guide + project context + session_handoff_2026-06-24_BATCH_EDIT_IMPLEMENTATION.md
Creates: STATUS REPORT → Verifies work → Plans Phase 2
Produces: session_handoff_2026-06-24_SPOT_CHECK_AND_PLANNING.md
```

Each session builds on the previous one via the handoff chain.
