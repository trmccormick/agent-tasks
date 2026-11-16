---
status: backlog
priority: MEDIUM
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)
You are Implementation Agent.
Project: galaxy_game
Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-08-04-MEDIUM-DOCUMENTATION-BIO-ETHICS-SPECTRUM-COMPANION-DOC.md
STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
git mv projects/galaxy_game/tasks/backlog/current/2026-08-04-MEDIUM-DOCUMENTATION-BIO-ETHICS-SPECTRUM-COMPANION-DOC.md 
projects/galaxy_game/tasks/active/2026-08-04-MEDIUM-DOCUMENTATION-BIO-ETHICS-SPECTRUM-COMPANION-DOC.md
Then open the moved file and change: status: backlog → status: active
Paste the output of both commands in chat before proceeding.
Do NOT read the task file content, run any commands, or start synthesis until this is done.
LIFECYCLE: backlog → active → completed
Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-08-04-MEDIUM-DOCUMENTATION-BIO-ETHICS-SPECTRUM-COMPANION-DOC.md"
Only ONE result should exist. Paste this output before committing.
READ FIRST (after Step 0): Task file contains all prerequisites, gotchas, and verification steps.
CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/summaries/
Filename pattern: YYYY-MM-DD-DOCUMENTATION-BIO-ETHICS-SPECTRUM-COMPANION-DOC.md
Chat is for questions only — never paste synthesis into chat.

**That's it.** Everything else should be IN this task file, not duplicated in handoff.

---

# TASK: Create Bio-Ethics Spectrum companion doc
**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-08-04

---

## Prerequisites — READ FIRST (Sequential Order)

1. Workflow README for the executor role.
2. Project guide for galaxy_game.
3. This task file.

---

## Context
Surfaced from the same Gemini design session (2026-08-04) as the Titan Conservation Mandate task. Two new concepts were introduced that don't fit into the existing `PLANETARY_TERRAFORMING_SOP.md` structure and warrant a dedicated companion doc: `docs/gameplay/bio_ethics.md`.

**Relevant Architecture Docs** — read before starting:
- `PLANETARY_TERRAFORMING_SOP.md` — for cross-referencing where biosphere seeding (Bio-Nursery Domes, Terraforming Seeds) is already documented, since this new doc extends that domain rather than replacing it.

---

## Critical Information for This Task

### Gotchas

⚠️ **GOTCHA 1: This is a new companion doc, not an SOP rewrite.**
- Do not edit `PLANETARY_TERRAFORMING_SOP.md` as part of this task — only add a cross-reference link from it to the new doc if a natural anchor point exists (e.g. near Bio-Nursery Domes / Terraforming Seeds).

⚠️ **GOTCHA 2: Don't invent game-balance numbers.**
- CapEx/risk tradeoffs between the three strain types should be described qualitatively (what's more expensive, what's riskier) unless the source Gemini chat specified actual numbers. If specific costs/values weren't given, mark them `[FILL IN — needs game-balance pass]` rather than inventing figures.

---

## Problem Statement
**Current state**: No documentation exists for biological containment risk mechanics or invasive-species economics, despite these being discussed as a design direction.

**Expected state**: A new `docs/gameplay/bio_ethics.md` doc covering:
1. **Bio-Ethics Spectrum** — forward contamination (introducing Earth/human-origin life to a world) vs backward contamination (bringing indigenous/extraterrestrial biology back), and genetic containment mechanisms (auxotrophy — engineered dependency on a nutrient not naturally present, preventing uncontrolled spread; kill-switches — engineered lethality triggers).
2. **Three strain types** with their CapEx vs risk tradeoffs: Wild (unmodified, cheapest, highest containment risk), Auxotrophic (engineered dependency, moderate cost, moderate risk), Kill-Switch (engineered lethality trigger, highest cost, lowest risk).
3. **Invasive Vector Logistics** — biological leakage as an economic mechanic: bio-fouling (unwanted biological growth impacting equipment/settlements), quarantine costs, emergency contract mechanics for containment response.

---

## Files Involved

### Primary Files — you will create/edit these
| File | Purpose |
|---|---|
| `docs/gameplay/bio_ethics.md` | New companion doc (create) |
| `PLANETARY_TERRAFORMING_SOP.md` | Optional: add a single cross-reference link near Bio-Nursery Domes/Terraforming Seeds sections, pointing to the new doc |

### Migration
- [x] No migration needed — documentation only

---

## Implementation Steps

1. Create `docs/gameplay/bio_ethics.md`.
2. Write the Bio-Ethics Spectrum section (forward/backward contamination definitions, genetic containment mechanisms).
3. Write the three strain types section (Wild/Auxotrophic/Kill-Switch) with qualitative CapEx/risk tradeoffs — mark exact numbers `[FILL IN]` if not specified in source material.
4. Write the Invasive Vector Logistics section (bio-fouling, quarantine costs, emergency contracts as economic mechanics).
5. Add a single cross-reference line in `PLANETARY_TERRAFORMING_SOP.md` near the existing Bio-Nursery Domes/Terraforming Seeds content, pointing to the new doc.
6. Synthesis report noting any ambiguity found in the source material that needs a follow-up game-design decision.

---

## Acceptance Criteria
- [ ] `docs/gameplay/bio_ethics.md` created covering all three concepts (Bio-Ethics Spectrum, three strain types, Invasive Vector Logistics)
- [ ] No invented game-balance numbers — ambiguous costs/values marked `[FILL IN]`
- [ ] Single cross-reference added from `PLANETARY_TERRAFORMING_SOP.md`, no other edits to that file
- [ ] No code changes

---

## Stop Conditions
- Stop if the three strain types' relative CapEx/risk ordering is itself ambiguous in the source material (not just missing exact numbers) — that's a design decision, not a documentation gap, and needs escalation rather than a guess.

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related**: Titan Conservation Mandate task (same source Gemini session, filed separately)

---

## Completion Report
*Filled in by the implementing agent after completion*

---

## Handoff Summary
HANDOFF SUMMARY: [file created] | [sections covered] | [any FILL IN gaps flagged]