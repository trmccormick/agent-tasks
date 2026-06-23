# Multi-Project Agent Workflow System
**Purpose**: Repeatable, generic coordination framework for agent-driven development across all projects

**Origin**: Started for Galaxy Game, evolved into generic system now used for:
- Galaxy Game (Luna Phase)
- Samvera Hyku (multi-tenant platform)
- WVU Knapsack (similar to Hyku)
- Samvera Hyrax (core framework)
- WVU Libraries projects (ACDA Portal, Authentication)

---

## What This System Does

**Problem Solved**:
- Managing multiple agents across multiple projects
- Clear role separation (planning, review, implementation)
- No context loss across sessions
- Preventing fabrication via synthesis gates
- Reproducible workflows

**Solution**:
- **Generic guides** that work for any project
- **Project-specific context** isolated per project
- **Role clarity**: Reviewer/Planning agents vs Implementation agents
- **Handoff chaining**: Each session builds on previous, no old history needed
- **Synthesis gates**: Agents prove understanding before executing

---

## The Repeatable Pattern

### For Any New Project

1. **Create project folder**: `projects/[PROJECT]/`
2. **Add files**:
   - `README.md` — Project context (copy from REVIEW_AGENT_GUIDE template)
   - `status.md` — Progress tracking
   - `tasks/active/` — Active work
   - `tasks/completed/` — Finished work
   - `handoffs/` — Session handoffs (session_handoff_YYYY-MM-DD_[TOPIC].md)

3. **Use standard workflow**:
   - Planning session: Send generic guide + project README + status + previous handoff
   - Implementation session: Send minimal 2-line handoff + task file
   - Spot-check session: Review results + plan next

4. **Reuse templates**:
   - REVIEW_AGENT_GUIDE.md (generic)
   - TASK_TEMPLATE.md (for creating tasks)
   - REVIEW_AGENT_WORKFLOW.md (for understanding workflow)

---

## What Gets Remembered

**Persists Across Sessions**:
- `status.md` — What's done, what's active, what's blocked
- Session handoffs — What each session accomplished + next steps
- Task files — Acceptance criteria, implementation notes, gotchas
- Project README — Architecture, setup, critical info

**Doesn't Need to Repeat**:
- Old session details — Current handoff contains everything needed
- Project context — Stored in README, pasted to agents once
- Workflow rules — Defined in generic guide, same for all projects

---

## Current Implementation (M4)

**Root** (6 files — generic, project-agnostic):
- README.md
- REVIEW_AGENT_GUIDE.md
- REVIEW_AGENT_WORKFLOW.md
- ROUTING_LOGIC.md
- TASK_TEMPLATE.md
- QUICK_START_PLANNING_SESSION.md

**Projects** (6 projects — each has own workspace):
- galaxy_game/ (README + tasks + handoffs)
- samvera_hyku/ (README + tasks + handoffs)
- samvera_hyrax/ (README + tasks + handoffs)
- wvulibraries_* (4 projects, same structure)

**Supporting Folders**:
- docs/ — Reference & housekeeping
- rules/ — Decision log & guardrails
- notes/ — Incidents & history
- archive/ — Files pending review

---

## How to Use It

### Start a Planning Session
```
You are PLANNING Agent for [PROJECT].

Read in order:
1. /Users/tam0013/Documents/git/agent-tasks/REVIEW_AGENT_GUIDE.md
2. /Users/tam0013/Documents/git/agent-tasks/projects/[PROJECT]/README.md
3. [Paste status.md]
4. [Paste previous handoff]

Assignment: [Specific task]

Create STATUS REPORT in chat first.
```

### Dispatch Implementation
```
You are IMPLEMENTATION Agent for [PROJECT].

Task file: [FULL PATH]

Read prerequisites, create synthesis, wait for approval.
```

### Track Progress
- Update `status.md` in each session
- Create session handoff (saved to `projects/[PROJECT]/handoffs/session_handoff_YYYY-MM-DD_[TOPIC].md`)
- Next session reads only current handoff + project context

---

## Why It Works

✅ **Generic**: Same structure, guides, templates for any project
✅ **Scalable**: New projects just follow existing pattern
✅ **Contextual**: Project-specific info stays isolated in project folder
✅ **Continuous**: Handoffs preserve context, no starting from scratch each session
✅ **Safe**: Synthesis gates prevent bad code from being written
✅ **Reproducible**: Same workflow = predictable outcomes across all projects
✅ **Manageable**: Clear roles, clear handoffs, clear documentation

---

## Files That Enable This

**Core Documentation**:
- `README.md` — How to use the system
- `REVIEW_AGENT_GUIDE.md` — Role definition for review agents
- `REVIEW_AGENT_WORKFLOW.md` — Complete workflow documentation
- `QUICK_START_PLANNING_SESSION.md` — Cheat sheet for dispatch

**Templates**:
- `TASK_TEMPLATE.md` — Blueprint for creating tasks
- `projects/[PROJECT]/README.md` — Project context template

**Tracking**:
- `projects/[PROJECT]/status.md` — Per-project progress
- `projects/[PROJECT]/handoffs/` — Session continuity
- `projects/[PROJECT]/tasks/` — Work queue (active/completed)

---

## Next Projects / Expansion

This system can be added to any project by:
1. Creating `projects/[NEW_PROJECT]/` folder
2. Copying structure from existing project
3. Creating project-specific README
4. Starting with planning session using generic guide

Same workflow. Same templates. Different project.

---

## Memory/Intent Tracking

This note documents the **intent** of the entire system:
- Repeatable workflow (started Galaxy Game, now generic)
- Coordination across multiple agents
- Remembering what's being worked on (status.md, handoffs)
- Preventing context loss and fabrication

If you expand to new projects or add new agents, reference this document to confirm alignment with core intent.
