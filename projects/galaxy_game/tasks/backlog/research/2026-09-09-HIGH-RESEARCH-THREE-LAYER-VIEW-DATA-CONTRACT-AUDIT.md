---
status: backlog
priority: HIGH
type: architecture
system_domain: CONTROLLERS
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Research Agent**.

Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/research/2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/research/2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md \
         projects/galaxy_game/tasks/active/2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
- Tracked file: git mv (never cp or plain mv)
- New/untracked file: mv then git add the final path
- Never leave stale copies in the source folder
- Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md"
Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
Chat is for questions only — never paste synthesis into chat (formatting breaks).

text

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Audit three-layer view data contracts (Planetary/Surface/TerrainForge) for wiki documentation
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-09-09
**Last Updated**: 2026-09-09

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: PASS — YAML frontmatter and Agent Dispatch Interface present; path and formatting issues corrected in this review.
- **Docker Wrapper Check**: N/A
- **MVP Alignment**: VALID — provides evidence-backed baseline for wiki documentation of Planetary/Surface/TerrainForge views and data contracts.
- **MVP Impact Note**: Prevents wiki from documenting planned architecture as current reality; clarifies implemented vs designed vs historical.
- **Action Line**: READY FOR LOCAL DISPATCH (after template fixes applied)

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Research-only task; no code changes; fits local planning/research workflow.
**Local attempts before cloud**: N/A
**Supervision Level**: standard

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

The Galaxy Game UI is designed around a three-layer view architecture:

1. **Planetary View** — SimEarth-style global overview (4K canvas, elevation/liquid/biomes, no zoom in Phase 1).
2. **Surface View** — Civ4/FreeCiv-style strategic regional map (sprite-based terrain/biomes, layers for resources/settlements/units/civilization, pan/zoom).
3. **TerrainForge** — SimCity-style settlement detail view (same rendering pipeline as Surface View, camera zoomed 10–100x on a single settlement tile).

Multiple design and implementation artifacts exist across 2026-03 to 2026-07, including:

- Planetary View Phase 1 intent doc (4K canvas, bathtub + biomes).
- RSpec tests for `planetary_admin_celestial_body_path` and `monitor-data` JSON contract.
- Surface View implementation plan (NASA GeoTIFFs, FreeCiv tilesets as training data, layer stack).
- Three-layer views architecture spec (`three_layer_views.md`, July 2026).
- Various older UI/terrain task drafts (GGMap scientific/strategic layers, regional view Phase 2, UI enhancement guides) that are now historical/experimental.

The goal of this task is to produce an **evidence-backed audit** of what is actually implemented today vs what is only designed or historical, so that future wiki documentation can accurately describe the current state without conflating it with planned or abandoned designs.

**Relevant Architecture Docs** — read as context, but do not treat as proof of implementation:
- `docs/architecture/three_layer_views.md` — three-layer architecture spec (July 2026).
- `docs/developer/SURFACE_VIEW_IMPLEMENTATION_PLAN.md` — historical implementation plan (Feb–Mar 2026).
- Any “Planetary View Intent” or “Monitor” docs you encounter — treat as context, verify against code.

> If a doc doesn’t exist for this area, do not create one during this task. Flag the gap in your completion report instead.

---

## Critical Information for This Task

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: This is research-only; do not modify any files.
- ❌ Wrong: Editing views, controllers, JS, or data files.
- ✅ Right: Produce a markdown research note under `summaries/` only.
- Why: We need a stable design basis before touching code or data.

⚠️ **GOTCHA 2**: Many UI/terrain docs are historical/experimental, not current truth.
- ❌ Wrong: Assuming old task drafts (e.g., GGMap scientific/strategic layers, UI enhancement guides, FreeCiv tileset plans) describe current behavior.
- ✅ Right: Treat them as hints about where to look; verify every claim against current code, data, and tests.
- Why: Design evolved; some ideas were absorbed, some abandoned.

⚠️ **GOTCHA 3**: Do not assume “file exists” means “feature is live and used”.
- ❌ Wrong: Concluding a feature is implemented just because a file exists.
- ✅ Right: Check whether the file is actually referenced from live views/controllers/tests.
- Why: Some modules (e.g., `ui_manager.js`, `system_renderer.js`, `game_interface_enhanced.js`) may be experimental or unused.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or writing any content, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):

```markdown
## STATUS SYNTHESIS REPORT

**Task**: 2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT
**Status**: backlog → active
**Date**: 2026-09-09

### What I'm About to Do
[2-3 sentences: the goal, the research method, the success criteria]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `docs/architecture/three_layer_views.md` | Three-layer architecture spec | pending |
| `docs/developer/SURFACE_VIEW_IMPLEMENTATION_PLAN.md` | Historical Surface View plan | pending |
| RSpec tests for planetary_admin_celestial_body_path | Planetary View data contract | pending |
| Existing view/controller/JS files | Verify current implementation | pending |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above

### Expected Outcomes
A markdown research note under `summaries/` that:
- Describes the actual current state of Planetary/Surface/TerrainForge views.
- Maps the real data contracts (e.g., monitor-data, surface-data, geosphere.terrain_map).
- Distinguishes implemented vs designed vs historical/experimental.
- Recommends how the wiki should document each area.

### Critical Gotchas I Will Avoid
- ❌ Modifying code or data — instead ✅ Research-only, output to `summaries/`.
- ❌ Treating old docs as current truth — instead ✅ Verify against code/tests.
- ❌ Assuming “file exists” = “feature live” — instead ✅ Check actual usage.

***

**SYNTHESIS COMPLETE.** Ready to proceed with research.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

Multiple design and implementation artifacts describe the Planetary/Surface/TerrainForge views and their data contracts, but they span different dates and design iterations. Some are current (e.g., three-layer views spec, Planetary View Phase 1 intent, RSpec tests), while others are historical or experimental (e.g., GGMap scientific/strategic layers, UI enhancement guides, old FreeCiv tileset plans).

Without a clear, evidence-backed audit, there is a risk that future wiki documentation will:

- Describe planned or abandoned designs as if they are current reality.
- Conflate historical prototypes with implemented features.
- Mislabel experimental modules as live systems.

**Current behavior**: Unclear which view features and data contracts are actually implemented vs designed vs historical.
**Expected behavior**: A research note that clearly distinguishes implemented, designed, and historical elements, so the wiki can document reality accurately.

---

## Files Involved

### Primary Files — you will create these
| File | Purpose | Key Section |
|---|---|---|
| `summaries/YYYY-MM-DD-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md` | Research note on actual view/data-contract state | Entire file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `docs/architecture/three_layer_views.md` | Three-layer architecture spec (July 2026) |
| `docs/developer/SURFACE_VIEW_IMPLEMENTATION_PLAN.md` | Historical Surface View plan (Feb–Mar 2026) |
| RSpec tests for `planetary_admin_celestial_body_path` | Planetary View data contract tests |
| Search for current ERB views, controllers, and JS modules by name patterns (e.g., *planetary*, *surface*, *monitor*) | Verify actual implementation paths — do not assume specific filenames exist |

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
# From inside agent-tasks repo root:
git mv projects/galaxy_game/tasks/backlog/research/2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md \
       projects/galaxy_game/tasks/active/2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md
```

Then open the moved file and change the YAML status field:
status: backlog → status: active

text

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks \
     -name "2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Inventory relevant docs and code

Search the codebase for:

- Three-layer views spec (`three_layer_views.md` or similar).
- Planetary View intent/monitor docs.
- Surface View implementation plan and any related “Monitor”, “Planetary”, “Surface”, “GGMap” docs.
- UI enhancement guides (UIManager/SystemRenderer/GameInterfaceEnhanced).
- RSpec tests for Planetary/Surface views.
- Current ERB views, controllers, and JS modules for Planetary/Surface/TerrainForge.

For each, note:

- Path and date (if available).
- Whether it appears current, historical, or experimental.
- Whether it is referenced from live code (views/controllers/tests).

Do not modify anything; just build a mental map of what exists and where.

### Step 2 — Inspect Planetary View data contract

Examine:

- The RSpec tests for `planetary_admin_celestial_body_path` (or equivalent).
- The `monitor-data` JSON structure (as injected in the view or tested in RSpec).
- The controller action that builds this JSON.
- How `geosphere.terrain_map`, spheres, and `available_layers` are used.

Record:

- Actual JSON keys and types.
- How missing data (e.g., no terrain_map) is handled.
- Whether the view degrades gracefully.

### Step 3 — Inspect Surface View data contract

Examine:

- `surface.html.erb` (or equivalent) and how it injects `surface-data`.
- `surface_view.js` (or equivalent) rendering pipeline and layer system.
- How `geosphere.terrain_map`, biomes, liquid, resources, settlements, units are used.
- Which layers are actually implemented vs gated vs planned.

Record:

- Actual layer stack and toggles.
- Sprite usage (biomes, terrain) vs color fallback.
- Units/civilization layer status (implemented, gated, planned).
- Any zoom/pan behavior and how it’s implemented.

### Step 4 — Check for TerrainForge implementation

Search for:

- Any “TerrainForge” controllers, views, or JS modules.
- Camera zoom transitions from Surface to a detail view.
- Building placement/configuration code.

Determine whether:

- TerrainForge is implemented, partial, or purely spec.
- How it relates to Surface View (same renderer, different camera state, or separate).

### Step 5 — Map historical vs current

For each major concept (e.g., GGMap scientific/strategic layers, UIManager/SystemRenderer, FreeCiv tileset plans, regional view Phase 2):

- Determine if it’s:
  - Implemented and live.
  - Partially implemented / gated.
  - Designed but not implemented.
  - Historical/experimental and no longer used.

Note any discrepancies between old docs and current code.

### Step 6 — Write the research note

Produce a markdown research note suitable for:

`summaries/YYYY-MM-DD-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md`

Include:

- Executive summary of actual current state.
- Planetary View data contract (monitor-data, spheres, terrain_map, available_layers).
- Surface View data contract (surface-data, layer stack, sprites, units/civilization status).
- TerrainForge status (implemented/partial/spec-only).
- Historical vs current mapping (which old docs are outdated and how).
- Recommendations for wiki documentation (what can be documented as implemented, what must be labeled designed/historical).

Use clear headings and bullet points; keep it readable for both designers and future implementers.

---

## Acceptance Criteria
- [ ] Research note saved as `summaries/YYYY-MM-DD-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md`.
- [ ] Covers Planetary/Surface/TerrainForge data contracts and implementation status.
- [ ] Clearly distinguishes implemented vs designed vs historical/experimental.
- [ ] Does not modify any code or data files.
- [ ] Provides actionable recommendations for wiki documentation.

---

## Stop Conditions — escalate to user immediately if:
- You find that the view/data-contract situation is so inconsistent that no coherent pattern can be recommended without design input.
- You determine that additional research (beyond code inspection) is required to proceed plausibly.
- Any architectural decision is required (e.g., “should we treat this experimental module as live?”).

---

## Commit Instructions

Run git commands on **host only** — never inside any container:

```bash
# Add only the new research note
git add summaries/YYYY-MM-DD-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md
git commit -m "research: add three-layer view data-contract audit for wiki documentation"
git push
```

**Task file move on completion:**

```bash
# Tracked file (already committed): use git mv
git mv projects/galaxy_game/tasks/active/2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md \
       projects/galaxy_game/tasks/completed/2026-09/2026-09-09-HIGH-RESEARCH-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md
git commit -m "chore: move three-layer view data-contract audit to completed/"
```

---

## Documentation
- [x] No doc changes needed
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none
**Blocks**: future wiki documentation for worlds/terrain/UI layers.
**Related tasks**: 
- `2026-09-09-MEDIUM-RESEARCH-MATERIAL-CHAIN-IRON-STEEL.md` (separate lane).
- Economic documentation audit and wiki completion (economy lane).

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final output**: `summaries/YYYY-MM-DD-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md`

### What was changed
- Created research note under `summaries/`.

### Issues discovered
[Any problems found during research that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: created `summaries/YYYY-MM-DD-THREE-LAYER-VIEW-DATA-CONTRACT-AUDIT.md` | research-only, no code/data changes | next: use audit to drive wiki documentation for Planetary/Surface/TerrainForge views