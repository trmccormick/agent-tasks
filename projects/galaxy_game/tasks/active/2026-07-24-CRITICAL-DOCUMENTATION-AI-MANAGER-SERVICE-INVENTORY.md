---
title: "AI Manager service inventory — architecture doc + contributor guide"
priority: CRITICAL
status: active
owner: Implementation Agent (Qwen)
type: documentation
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
created: 2026-07-24
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/drafts/2026-07-24/2026-07-24-CRITICAL-DOCUMENTATION-AI-MANAGER-SERVICE-INVENTORY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/drafts/2026-07-24/2026-07-24-CRITICAL-DOCUMENTATION-AI-MANAGER-SERVICE-INVENTORY.md \
         projects/galaxy_game/tasks/active/2026-07-24-CRITICAL-DOCUMENTATION-AI-MANAGER-SERVICE-INVENTORY.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-24-CRITICAL-DOCUMENTATION-AI-MANAGER-SERVICE-INVENTORY.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-DOCUMENTATION-AI-MANAGER-SERVICE-INVENTORY.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: AI Manager service inventory — architecture doc + contributor guide

**Status**: BACKLOG
**Priority**: CRITICAL
**Type**: documentation
**Created**: 2026-07-24
**Last Updated**: 2026-07-24

---

## Context

The AI Manager layer is the largest subsystem in galaxy_game, containing **80+ services**. However, the architecture documentation only references ~8 core files. This creates a massive gap between what the docs say exists and what actually exists in code. New contributors (and agents) have no reliable way to discover, understand, or navigate AI Manager services.

This task addresses a critical contributor onboarding blocker: the architecture doc is misleadingly sparse, and there's no service inventory page or contributor guide for adding new services.

---

## Problem Statement

- Architecture docs imply only ~8 core AI Manager files exist
- Actual codebase has 80+ services in `app/services/ai_manager/` (and subdirectories)
- No service inventory page listing all services with descriptions
- No contributor guide for adding new services consistently
- New contributors cannot navigate the subsystem without reading every file

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: AI Manager services span multiple directories**
- ❌ Wrong: Only look at `app/services/ai_manager/` root
- ✅ Right: Services are in `app/services/ai_manager/`, `app/services/ai_manager/super_mars/`, `app/services/ai_manager/luna/`, etc. — subdirectories too
- Why: The namespace is hierarchical; services live at multiple depth levels

⚠️ **GOTCHA 2: Some "AI Manager" services are in other namespaces**
- ❌ Wrong: Only search under `AiManager` or `ai_manager` paths
- ✅ Right: Also check `app/services/terraforming/`, `app/services/npc_economy/`, `app/services/manufacturing/` for services that conceptually belong to AI Manager domain
- Why: Domain boundaries in code don't always match conceptual boundaries

### Files to Audit (Read-Only)

| File/Directory | Purpose |
|---|---|
| `app/services/ai_manager/` | Primary AI Manager services directory |
| `app/services/ai_manager/super_mars/` | Super Mars settlement subdomain services |
| `app/services/ai_manager/luna/` | Luna settlement subdomain services |
| `app/services/terraforming/` | Terraforming services (AI Manager adjacent) |
| `app/services/npc_economy/` | NPC economy services (AI Manager adjacent) |
| `app/services/manufacturing/` | Manufacturing services (AI Manager adjacent) |
| `docs/new_agent/rules/DECISIONS.md` | Existing architectural decisions |
| `docs/new_agent/rules/GUARDRAILS.md` | Execution rules |

---

## Implementation Steps

### Step 1 — Audit all AI Manager services

Run a comprehensive audit of all service files:

```bash
find /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/services -name "*_service.rb" -o -name "*_manager.rb" | grep -i "ai_manager\|terraforming\|npc_economy\|manufacturing" | sort
```

For each file, extract:
- Service name (class name)
- Primary responsibility (one sentence)
- Key public methods
- Dependencies on other services/models
- Whether it's MVP-relevant or phase-deferred

### Step 2 — Create service inventory page

Create a new documentation file at:
`docs/new_agent/projects/galaxy_game/services/ai_manager_service_inventory.md`

Structure:
```markdown
# AI Manager Service Inventory

## Overview
[1-2 paragraph summary of the AI Manager subsystem]

## Core Services (MVP-relevant)
| Service | File | Responsibility | Key Methods | MVP Phase |
|---|---|---|---|---|
| ... | ... | ... | ... | ... |

## Supporting Services
[Same table format, marked as phase-deferred]

## Subdomain Breakdown
### Super Mars Settlement
[Services specific to Super Mars]

### Luna Settlement
[Services specific to Luna]

### Terraforming Pipeline
[Services in the terraforming chain]

### NPC Economy
[Services managing NPC agents and economy]

### Manufacturing Chain
[Services for ISRU production]

## Service Dependency Graph
[Text-based diagram of how services interact]

## How to Add a New Service
[See Step 3 — contributor guide content]
```

### Step 3 — Create contributor guide

Create: `docs/new_agent/projects/galaxy_game/contributors/adding-ai-manager-service.md`

Content should cover:
- Where to place new service files (naming conventions, directory structure)
- Required module inclusions (e.g., `ServiceBase`, logging concerns)
- How to wire up the service (initializers, dependency injection patterns)
- Test placement conventions (`spec/services/ai_manager/...`)
- Documentation requirements (what goes in the service inventory vs. inline comments)
- Common pitfalls (naming conflicts, circular dependencies, factory usage)

### Step 4 — Update architecture doc

Update the existing AI Manager architecture doc to:
- Replace the sparse ~8-file reference with a link to the new service inventory
- Add a high-level subsystem overview that matches reality
- Cross-reference the contributor guide

### Step 5 — Verify

- [ ] Service inventory lists ALL services (count should match `find` output from Step 1)
- [ ] Each service has at least a one-line description
- [ ] Contributor guide covers placement, wiring, testing, docs
- [ ] Architecture doc updated with correct scope
- [ ] No stale references to old file counts

---

## Acceptance Criteria
- [ ] Service inventory page exists and lists all 80+ AI Manager services
- [ ] Each service has: name, file path, responsibility, key methods, MVP phase
- [ ] Contributor guide exists with actionable instructions for adding new services
- [ ] Architecture doc updated to reflect actual scope
- [ ] All new docs pass markdown lint (if applicable)

---

## Stop Conditions — escalate to user immediately if:
- Service count discrepancy > 5 files between audit and inventory
- Cannot determine responsibility for a service after reading its code
- Existing architecture doc uses patterns that conflict with current conventions
- A migration is needed (unlikely for docs-only task)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add docs/new_agent/projects/galaxy_game/services/ai_manager_service_inventory.md
git add docs/new_agent/projects/galaxy_game/contributors/adding-ai-manager-service.md
git add [any updated architecture doc paths]
git commit -m "docs: AI Manager service inventory + contributor guide — documents 80+ services, adds onboarding guide"
```

---

## Documentation
- [x] New docs created (service inventory + contributor guide)
- [ ] Update existing architecture doc — [path TBD after audit]
- [ ] Flag doc gap: [description if needed]

---

## Dependencies
**Blocked by**: none
**Blocks**: NPC economy lifecycle docs, Manufacturing chain overview (these can reference the service inventory)
**Related tasks**: 2026-07-24-HIGH-DOCUMENTATION-NPC-ECONOMY-LIFECYCLE.md, 2026-07-24-HIGH-DOCUMENTATION-MANUFACTURING-CHAIN-OVERVIEW.md

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final service count**: [number]

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
