---
title: "Worldhouse design clarification — structures over terrain vs terraforming"
priority: MEDIUM
status: backlog
phase: phase1
owner: Implementation Agent (Qwen)
type: documentation
system_domain: TERRA_SIM
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
created: 2026-07-24
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/drafts/2026-07-24/2026-07-24-MEDIUM-DOCUMENTATION-WORLDHOUSE-DESIGN-CLARIFICATION.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/drafts/2026-07-24/2026-07-24-MEDIUM-DOCUMENTATION-WORLDHOUSE-DESIGN-CLARIFICATION.md \
         projects/galaxy_game/tasks/active/2026-07-24-MEDIUM-DOCUMENTATION-WORLDHOUSE-DESIGN-CLARIFICATION.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-07-24-MEDIUM-DOCUMENTATION-WORLDHOUSE-DESIGN-CLARIFICATION.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-DOCUMENTATION-WORLDHOUSE-DESIGN-CLARIFICATION.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Worldhouse design clarification documentation

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-07-24
**Last Updated**: 2026-07-24

---

## Context

Worldhouses are frequently confused with terraforming because they can contain engineered biomes. However, worldhouses are **structures over terrain**, not terraforming itself. This distinction is critical for contributors to understand: terraforming modifies planetary conditions (atmosphere, temperature, hydrosphere), while worldhouses create local habitats within existing planetary conditions. The documentation gap causes confusion about what constitutes planetary-scale modification vs. local habitat creation.

---

## Problem Statement

- Worldhouses are documented/understood as terraforming when they are actually structures
- No clear distinction between local habitat creation (worldhouse) and planetary modification (terraforming)
- Contributors confuse worldhouse biome engineering with atmospheric/temperature terraforming
- No documentation clarifies the boundary between these two concepts

---

## Critical Information for This Task

### Architecture Gotchas

⚠️ **GOTCHA 1: Worldhouses can contain biomes that look like terraforming**
- ❌ Wrong: Assume worldhouse biome creation = planetary terraforming
- ✅ Right: Worldhouse biomes are local/contained; terraforming affects the entire celestial body
- Why: Both use similar biome models, but at different scales and with different side effects

⚠️ **GOTCHA 2: Worldhouses may interact with terraforming indirectly**
- ❌ Wrong: Assume worldhouses have zero relationship to terraforming
- ✅ Right: Worldhouses can be placed on terraformed terrain and may use terraforming data for placement validation
- Why: The systems are related but distinct — documentation should clarify the relationship, not deny it

### Files to Audit (Read-Only)

| File/Directory | Purpose |
|---|---|
| `app/models/` or `app/services/` — search for "worldhouse" | Worldhouse models and services |
| `app/models/celestial_bodies/spheres/atmosphere.rb` | Terraforming affects atmosphere |
| `app/models/celestial_bodies/spheres/biosphere.rb` | Terraforming affects biosphere |
| Existing architecture docs mentioning worldhouses | Current documentation state |
| `docs/new_agent/rules/DECISIONS.md` | Any locked decisions about worldhouses |

---

## Implementation Steps

### Step 1 — Audit worldhouse implementation

```bash
grep -ri "worldhouse" /Users/tam0013/Documents/git/galaxyGame/galaxy_game/app/ --include="*.rb" -l | sort
grep -ri "worldhouse" /Users/tam0013/Documents/git/galaxyGame/galaxy_game/spec/ --include="*.rb" -l | sort
```

For each file found, note:
- What the worldhouse model/service does
- How it creates/manages biomes
- Whether it modifies planetary conditions (atmosphere, temperature) or just local structure
- Any interaction with terraforming services/models

### Step 2 — Create worldhouse design doc

Create: `docs/new_agent/projects/galaxy_game/structures/worldhouse_design.md`

Structure:
```markdown
# Worldhouse Design

## Overview
[1-2 paragraph summary: what a worldhouse is, what it does]

## Key Distinction: Worldhouse vs. Terraforming
### Worldhouse (Local Habitat)
- Scope: Single structure / contained area
- What it modifies: Internal biome conditions only
- What it does NOT modify: Planetary atmosphere, temperature, hydrosphere
- Analogy: A greenhouse on Earth

### Terraforming (Planetary Modification)
- Scope: Entire celestial body
- What it modifies: Atmosphere composition, surface temperature, hydrosphere state
- What it does NOT modify: Internal structure of habitats
- Analogy: Making Earth-like conditions across an entire planet

## Worldhouse Architecture
### Model Structure
[Fields, associations, key methods]

### Biome Engineering in Worldhouses
[How biomes are created inside worldhouses — what's different from planetary biomes]

### Placement Rules
[Where worldhouses can be placed, what terrain/planetary conditions are required]

## Interaction with Terraforming
### Indirect Relationships
- Worldhouses can be placed on terraformed terrain
- Worldhouse placement may validate against planetary conditions
- Worldhouse biomes do NOT feed back into planetary biosphere calculations

### What Worldhouses Do NOT Affect
- Planetary atmosphere composition
- Planetary surface temperature
- Planetary hydrosphere state
- Planetary biodiversity index

## Data Flow Diagram
[text-based diagram showing worldhouse placement and biome flow]

## Common Misconceptions
- "Worldhouses terraform planets" — NO: They create local habitats within existing conditions
- "Worldhouse biomes affect planetary biosphere" — NO: They are isolated from planetary calculations
```

### Step 3 — Update any existing architecture docs that conflate worldhouse with terraforming

If existing docs describe worldhouses as terraforming, update them to clarify the distinction.

### Step 4 — Verify

- [ ] Worldhouse design doc clearly distinguishes local habitat from planetary modification
- [ ] Common misconceptions are explicitly addressed
- [ ] Data flow diagram accurately reflects code behavior
- [ ] Any conflating docs updated with correct distinction
- [ ] No speculative claims — every statement backed by code evidence

---

## Acceptance Criteria
- [ ] Worldhouse design doc exists and clearly distinguishes worldhouse from terraforming
- [ ] Common misconceptions explicitly addressed
- [ ] Data flow diagram matches actual implementation
- [ ] Any conflating docs updated with correct distinction

---

## Stop Conditions — escalate to user immediately if:
- Worldhouse implementation is so scattered it can't be coherently documented
- Code behavior contradicts the "local vs planetary" distinction (implementation gap?)
- A migration is needed (unlikely for docs-only task)

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add docs/new_agent/projects/galaxy_game/structures/worldhouse_design.md
git add [any updated architecture doc paths]
git commit -m "docs: Worldhouse design clarification — structures over terrain, not terraforming"
```

---

## Documentation
- [x] New doc created (worldhouse design)
- [ ] Update existing architecture doc — [path TBD after audit]
- [ ] Flag doc gap: [description if needed]

---

## Dependencies
**Blocked by**: none
**Blocks**: none directly
**Related tasks**: 2026-07-24-MEDIUM-DOCUMENTATION-GAMEPLAY-LOOPS-OVERVIEW.md (worldhouses are part of settlement loop)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation]

### Follow-up tasks needed
[Any new backlog items identified]

### Lessons learned
[What worked, what didn't]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
