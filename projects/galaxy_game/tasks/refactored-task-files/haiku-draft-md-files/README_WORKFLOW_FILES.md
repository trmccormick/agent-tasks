# Workflow Files Ready for Use — Planning Agent Research Cycle

**Location**: `proposed new tasks/` folder  
**Date Created**: 2026-07-01  
**Purpose**: Complete toolkit for web agents to research stale tasks and draft refined task files

---

## Files in This Folder (What You Have Now)

### Workflow & Process Documents

1. **PLANNING_AGENT_WORKFLOW_FOR_WEB_AGENTS.md**
   - Complete workflow guide for web agents (Gemini, Claude, Perplexity)
   - Explains: 3 phases (intake → research → draft), mandatory reads, deliverables
   - Tells agent what they CAN and CANNOT do
   - Copy this into chat with web agent

2. **RESEARCH_ASSIGNMENT_TEMPLATE_STALE_TASK_REVIEW.md**
   - Research assignment template (can be customized per stale task)
   - Includes: mandatory reads, assignment scope, deliverables checklist
   - Used with the workflow document above
   - Copy this into chat along with the workflow

3. **DISPATCH_EXAMPLE_PLANNING_AGENT.md**
   - Example of what you actually copy-paste into Gemini's chat
   - Shows: dispatch message, context files, expected outputs
   - Reference for how to structure your dispatch message
   - Shows what agent's response looks like

### Existing Task-Related Documents (from Gemini's work)

4. **2026-06-30-TASK-UPDATE-1/** (folder)
   - 8 proposed tasks created by Gemini
   - Gap analysis document
   - Task refactor summary
   - These are what you'll ask the planning agent to review/refine

5. **2026-07-01-TASK-UPDATE-2/** (folder)
   - Where refined tasks will be saved by the planning agent
   - Will contain: synthesis report + draft task files (after agent completes)

---

## How to Use This Toolkit

### Step 1: Prepare Your Dispatch Message

Use **DISPATCH_EXAMPLE_PLANNING_AGENT.md** as a template. Customize it:

- Change project name if not galaxy_game
- Update file paths to point to YOUR stale task(s)
- Paste or link the original task files you want reviewed
- Specify the output folder (e.g., `proposed new tasks/2026-07-01-TASK-UPDATE-2/`)

### Step 2: Copy Into Chat with Web Agent

1. Open a chat with Gemini (or Claude/Perplexity)
2. Copy the dispatch message from your customized example
3. Also paste/link:
   - **PLANNING_AGENT_WORKFLOW_FOR_WEB_AGENTS.md** (full text)
   - **RESEARCH_ASSIGNMENT_TEMPLATE_STALE_TASK_REVIEW.md** (full text)
   - Any stale task files you want reviewed

### Step 3: Agent Confirms Intake

Agent answers checklist in chat:
- "I understand I'm doing PLANNING/RESEARCH (not implementation)"
- "I understand outputs go to proposed new tasks/[DATE]/"
- "I will NOT run code, tests, or make commits"

### Step 4: Agent Researches & Drafts

Agent:
1. Reads your stale task(s)
2. Audits relevant architecture docs/code
3. Creates synthesis report (current state vs. original intent)
4. Drafts refined task file(s) using TASK_TEMPLATE.md
5. Saves to the output folder you specified

### Step 5: You Review Output Folder

Navigate to `proposed new tasks/2026-07-01-TASK-UPDATE-2/` (or your date folder)

You'll find:
- `[DATE]-RESEARCH-[TOPIC].md` — Synthesis report with findings
- `[DATE]-[PRIORITY]-[TYPE]-[NAME].md` — Refined task files (1+ files)
- Optional: `TASK_REFACTOR_SUMMARY.md` — Overview if multiple tasks

### Step 6: Claude Reviews (Next Step)

Pass the refined task files to Claude:
- "Please review these 5 draft task files for architecture completeness"
- "Check for risks, dependencies, feasibility"
- Claude approves or requests changes

### Step 7: Move to Active (After Approval)

Once Claude approves:
1. Copy task files from `proposed new tasks/[DATE]/` to `tasks/active/`
2. Update YAML header: `status: backlog` → `status: active`
3. Git add + commit
4. Implementation agents pick up

---

## Customization Guide

### For Different Stale Tasks

**Stale Task Type #1: Incomplete feature**
```
Research focus:
- Is this still needed?
- What code changes since task was created?
- What's blocking it?
- How to split/scope it?

Synthesis output:
- INCOMPLETE classification
- Updated scope
- Refined task file(s)
```

**Stale Task Type #2: Already solved**
```
Research focus:
- Was this work done incidentally?
- Which other task completed this?
- When was it finished?
- Should we archive the original task?

Synthesis output:
- STALE_SOLVED classification
- Archive recommendation
- No new task files (or minimal housekeeping task)
```

**Stale Task Type #3: Obsolete approach**
```
Research focus:
- Did the project pivot?
- Is the new approach documented?
- Can the old task be closed?
- Is new work needed?

Synthesis output:
- STALE_OBSOLETE classification
- Documentation of the pivot
- Optional: new task file for updated approach
```

### File Naming Convention

When creating new dispatch example (for different stale task):

```
DISPATCH_EXAMPLE_[TOPIC]_[DATE].md

Example:
DISPATCH_EXAMPLE_LUNA_PRECURSOR_VERIFICATION_2026-07-01.md
DISPATCH_EXAMPLE_GUARDRAILS_REFACTORING_2026-07-01.md
```

---

## Key Differences: Planning vs. Implementation Agent

When you dispatch to different agents:

| Agent Type | Dispatch File | Purpose | Output | Duration |
|---|---|---|---|---|
| **Planning (Web)** | PLANNING_AGENT_WORKFLOW_FOR_WEB_AGENTS.md | Research stale tasks | Synthesis reports + draft tasks | 1-2 hours |
| **Implementation (Local Qwen)** | EXECUTOR role guide (in agent-tasks README.md) | Build code + tests | Working features + passing specs | 4-8 hours |
| **Review (Claude)** | REVIEW_AGENT_GUIDE.md (in new_agent docs) | Approve/refine | Approved tasks or feedback | 30 min |

Planning agent → creates task files  
Implementation agent → implements the task files  
Review agent → approves before implementation

---

## Workflow Cycle Visualization

```
┌─────────────────────────────────────┐
│  Stale Task in review/ Folder       │  ← Original problem
└──────────────┬──────────────────────┘
               │
               ↓
┌─────────────────────────────────────┐
│  PLANNING AGENT (Web)               │  ← Use workflow files
│  • Research stale task              │     from this toolkit
│  • Create synthesis report          │
│  • Draft refined task files         │
└──────────────┬──────────────────────┘
               │
               ↓ (saves to proposed new tasks/[DATE]/)
               │
┌─────────────────────────────────────┐
│  You Review Locally                 │
│  Check synthesis + draft tasks      │
└──────────────┬──────────────────────┘
               │
               ↓
┌─────────────────────────────────────┐
│  CLAUDE REVIEWS (Web)               │  ← Final approval
│  • Architecture check               │
│  • Feasibility assessment           │
│  • Approve or request changes       │
└──────────────┬──────────────────────┘
               │
               ↓ (if approved)
               │
┌─────────────────────────────────────┐
│  Move to active/ Folder             │
│  Update YAML: status: active        │
│  Git add + commit                   │
└──────────────┬──────────────────────┘
               │
               ↓
┌─────────────────────────────────────┐
│  IMPLEMENTATION AGENT (Local Qwen)  │
│  • Execute task file                │
│  • Write code + tests               │
│  • Get passing specs                │
└─────────────────────────────────────┘
```

---

## Summary: What You Do Now

1. ✅ **You have the workflow files** (this folder has everything you need)
2. 📝 **Customize the dispatch example** for your stale task
3. 💬 **Copy into chat with web agent** (Gemini/Claude/Perplexity)
4. ⏳ **Agent researches** (1-2 hours, produces synthesis + draft tasks)
5. 🔍 **You review output** in `proposed new tasks/[DATE]/`
6. ✅ **Claude approves** refined task files
7. 🚀 **Move to active/** for implementation

---

## Questions?

Refer back to:
- **PLANNING_AGENT_WORKFLOW_FOR_WEB_AGENTS.md** — How the process works
- **RESEARCH_ASSIGNMENT_TEMPLATE_STALE_TASK_REVIEW.md** — What agent will deliver
- **DISPATCH_EXAMPLE_PLANNING_AGENT.md** — What to copy-paste into chat

All files are in this folder, ready to use.

