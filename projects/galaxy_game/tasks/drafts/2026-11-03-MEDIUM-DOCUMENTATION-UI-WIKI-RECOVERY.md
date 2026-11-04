---
status: draft
resume: false
priority: MEDIUM
type: documentation
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🟡 DRAFT STATUS NOTE

This task is a draft awaiting human review. It has NOT been approved for backlog or dispatch.

**Current State**: `projects/galaxy_game/tasks/drafts/`  
**Next Step**: Human reviews and approves, then moves the task to backlog using the prescribed task lifecycle.  
**Do NOT dispatch** while this file remains in `tasks/drafts/`.

## 🔴 Agent Dispatch Interface

**Status**: DRAFT — NOT READY FOR DISPATCH. This section will be completed by the human when the task is approved and ready for dispatch. Do not execute this task while it remains in `tasks/drafts/`.

```
You are **Implementation Agent**.

Project: galaxy_game
Task: <agent-tasks>/projects/galaxy_game/tasks/backlog/[SUBFOLDER]/2026-11-03-MEDIUM-DOCUMENTATION-UI-WIKI-RECOVERY.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/[SUBFOLDER]/2026-11-03-MEDIUM-DOCUMENTATION-UI-WIKI-RECOVERY.md \
         projects/galaxy_game/tasks/active/2026-11-03-MEDIUM-DOCUMENTATION-UI-WIKI-RECOVERY.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy_game/tasks -name "2026-11-03-MEDIUM-DOCUMENTATION-UI-WIKI-RECOVERY.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy_game/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**Not yet resolved at draft stage**:
- `[SUBFOLDER]` — human fills this when moving the task to backlog.
- `<agent-tasks>` — substituted with the absolute local checkout path at dispatch time.
- The human must complete any remaining dispatch-specific details and approve the task before execution.

------

# TASK: GalaxyGame UI Wiki Recovery and Consolidation

**Status**: DRAFT  
**Priority**: MEDIUM  
**Type**: documentation  
**Created**: 2026-11-03  
**Author**: ChatGPT (draft for human review)

## Context

GalaxyGame's UI prototype is maintained in the separate `galaxy-game-ui` repository, while the authoritative game design and UI intent documentation belongs in the main `galaxyGame` repository.

The UI prototype guidance explicitly directs UI decisions to the live wiki in the main game repository (`docs/wiki/`) first, with deeper documentation consulted when the wiki is silent. Conflicts between the wiki, deeper documentation and code must be surfaced rather than silently resolved.

A planning summary for this effort is available at:

- `projects/galaxy-game-ui/summaries/2026-11-03-ui-planning-summary.md`

The session handoff is also relevant:

- `projects/galaxy_game/handoffs/HANDOFF-2026-11-03-GALAXY-GAME-UI-SESSION.md`

This task builds out the UI documentation **in the main GalaxyGame wiki**. The separate UI prototype repository is an investigation source and implementation reference, not the destination for the wiki.

## Purpose

Recover, verify, organize and consolidate the existing GalaxyGame UI design intent into a coherent set of wiki pages in `galaxyGame/docs/wiki/`.

This is a source-first documentation recovery task, not a blank-slate design exercise. The agent must investigate existing wiki content, deeper documentation, frozen/older material, task history and the actual UI implementation before proposing or writing reconciliations.

The resulting wiki should provide the durable design reference for subsequent UI prototype and implementation tasks. Those tasks should link to the wiki rather than duplicate design intent in task files.

## Repositories and authority

- **Main game repository — `galaxyGame`:** destination for the authoritative UI wiki pages under `docs/wiki/`. Its models, JSON and code provide evidence of actual data structures and implementation.
- **UI prototype repository — `galaxy-game-ui`:** source for current and prior UI implementation, prototype structure, and UI-specific guidance. It is not the destination for the authoritative wiki.
- **Agent tasks repository — `agent-tasks`:** contains this task, project guidance, session summaries, handoffs and historical tasks.

Use the current checkout paths; do not assume machine-specific absolute paths from another environment. Verify the live repository branches and current contents before relying on reported implementation status.

## Required reading

Read the applicable current versions before beginning:

1. `rules/GUARDRAILS.md`
2. `projects/galaxy_game/README.md`
3. `projects/galaxy-game-ui/README.md`
4. `projects/galaxy-game-ui/SESSION_GUIDANCE.md`
5. `projects/galaxy-game-ui/summaries/2026-11-03-ui-planning-summary.md`
6. `projects/galaxy_game/handoffs/HANDOFF-2026-11-03-GALAXY-GAME-UI-SESSION.md`
7. The main game's current `docs/wiki/README.md` and any wiki trust/status index (including `status-and-gaps.md`, if present).

Also read `projects/galaxy-game-ui/NEEDS_REVIEW.md`, `projects/galaxy-game-ui/status.md` and `projects/galaxy-game-ui/BRANCHES.md` before relying on older prototype tasks or reported implementation state. Follow the current session startup and closeout guidance.

## Review scope

Investigate and recover the following related UI subject areas. These are documentation topics, not a requirement to create exactly seven screens or force the wiki into seven pages. Follow the existing wiki organization where appropriate and propose a clearer structure only when supported by the material.

1. **UI architecture and navigation**
   - Overall application structure and navigation between game environments.
   - Shared UI conventions and relationships between major views.
   - How the UI prototype relates to the main game.

2. **Landing page and Command Center**
   - Intended purpose and player-facing role.
   - Existing or planned player overview, account information, activity and operational information.
   - Distinguish established intent from placeholder or unfinished content.

3. **Planetary View / Planetary Monitor**
   - Existing interactive planetary map and selectable/combinable visualization layers.
   - Terrain and GeoTIFF-derived data handling, layer rendering and data connections.
   - Relationship between planetary visualization and the Atmosphere, Hydrosphere, Geosphere and Biosphere data/simulation domains.
   - Identify which layers and data are implemented, partial, calculated, generated or placeholders.

4. **Surface View**
   - Regional/local terrain presentation, tile rendering, zoom, layer controls and location-level interaction.
   - Existing implementation versus documented future direction.
   - Relationship to the Planetary Monitor and settlement-level views.

5. **Settlement View / TerrainForge**
   - Settlement layout, structures, buildings, infrastructure, utilities, terrain and expansion.
   - Recover documented intent and distinguish implemented capabilities from planned or proposed ones.

6. **Digital Twin**
   - Relationship to TerraSim and the planetary sphere services.
   - Isolated scenario/projection purpose and boundaries.
   - Intended support for analysis and AI Manager decisions.
   - Compare documentation with the actual implementation; do not treat placeholders or mock behavior as completed simulation capability.

7. **UI design references**
   - Recover how references such as EVE Dashboard, EVE Online, Civilization, SimEarth, SimCity and other documented examples informed presentation or interaction.
   - Explain the specific inspiration supported by the sources.
   - Clearly distinguish inspiration from GalaxyGame requirements or authoritative design decisions.

## Source investigation requirements

Use a broad but relevant source-first review. Do not rely only on the newest wiki pages or current prototype code.

Inspect:

- Existing main-game wiki pages and indexes.
- Relevant architecture, design, gameplay and developer documentation.
- Older, frozen, archived or superseded documents that may preserve original intent.
- Relevant task drafts, backlog, completed task records, planning notes, handoffs and synthesis reports in the agent-tasks repository.
- The actual main-game UI implementation and its relevant controllers, views, JavaScript, routes, services and data interfaces.
- The current UI prototype implementation and relevant preserved first-attempt examples, where accessible and relevant.

Search by concepts and feature names as well as known page names. Follow source links far enough to understand the surrounding design context. Do not perform an indiscriminate repository-wide rewrite.

For each material design statement, preserve provenance with a source path and, where practical, a section heading or line reference. Link related source documents from the wiki so future readers can trace the recovered intent.

## Required distinctions and decisions to preserve

The following decisions were explicitly discussed during the November 3 planning session. Treat them as session-established direction, but still record source evidence and any conflicts discovered. Do not silently rewrite conflicting historical sources.

### Planetary Monitor reuse

The existing Planetary Monitor already has an interactive map with selectable layers and terrain data handling. The agreed initial approach is to copy/port the existing Planetary Monitor and its current layer implementation into the new UI as-is, making only the integration changes required to run it there. Compare and test it against the original, then refine data sources, calculations, accuracy and presentation afterward.

This reuse decision does not certify that every existing data source, calculation or visualization is correct. Document what is actually present and what still needs evaluation.

### Spheres versus visualization layers

Atmosphere, Hydrosphere, Geosphere and Biosphere are distinct planetary data/simulation domains. Visualization layers are presentation choices and do not map one-to-one to those spheres. For example, elevation is a geosphere property, while hydrosphere and biosphere information may be rendered as layers over the same map. Preserve this distinction.

### Digital Twin boundaries

The intended Digital Twin role is an isolated scenario/projection environment for examining possible developments and supporting AI Manager decision-making. Do not redefine it as a live-world editor, god-mode interface or the player's central dashboard. Record conflicts between older and newer descriptions as unresolved unless the source history clearly documents an approved change.

### UI references

External products and games are inspiration, not specifications. Do not copy their mechanics or assume their information architecture is a GalaxyGame requirement unless GalaxyGame sources explicitly establish that requirement.

## Provenance and status labels

**Use the main wiki's mandatory vocabulary, not a new task-specific label system.** Wiki page banners and section labels must follow rule 6 in `docs/wiki/README.md`: page banners are `VERIFIED AGAINST CODE`, `PARTIALLY IMPLEMENTED` or `DESIGN INTENT`; section labels are `ESTABLISHED INTENT` (owner-confirmed and dated), `DIRECTION`, `OPEN` or `RETIRED / DO NOT USE`. The categories below are a mapping aid for investigation and the recovery report; do not introduce them as competing wiki labels.

Do not label any material `ESTABLISHED INTENT` without explicit owner confirmation and a date. In particular, the November 3 session-supplied decisions below must not automatically be promoted to `ESTABLISHED INTENT`; present them as reported session direction unless Tracy confirms that status. The report's evidence labels (such as `VERIFIED`, `REPORTED` and `SESSION-DIRECTION`) apply only to report evidence and must not be used as wiki page or section labels.

The wiki must distinguish at least:

- **Established design intent** — explicitly supported by authoritative current documentation or an approved developer decision.
- **Implemented** — verified in the current code; cite the implementation location.
- **Partially implemented** — some behavior exists but the documented capability is incomplete.
- **Planned** — explicitly documented as future work.
- **Proposed** — a suggestion or unapproved direction, not an established decision.
- **Placeholder / mock** — temporary or simulated content, not a functioning game capability.
- **Unresolved / conflicting** — sources disagree or evidence is insufficient to determine the intended outcome.
- **Obsolete / superseded** — older material that should not be treated as current direction, with the reason and source recorded where known.

Do not infer status solely from a task's folder, a file's timestamp, or a previous agent's summary. Verify current state where possible. Separate developer-supplied/session decisions from repository evidence when their provenance differs.

## Deliverables

Create or update the following **in the main `galaxyGame` repository only**:

1. A coherent set of UI wiki pages under `docs/wiki/`, following and integrating with the existing wiki structure.
2. An appropriate UI wiki index or entry in the existing wiki navigation so readers can find the recovered material.
3. Cross-links to relevant source documentation and related wiki subjects.
4. A concise recovery report saved in `projects/galaxy_game/summaries/` in the agent-tasks repository. The report must list:
   - Sources examined.
   - Wiki pages created or updated.
   - Implementation status findings.
   - Contradictions and unresolved design questions.
   - Older or apparently obsolete sources that should be reviewed later.
   - Areas where evidence was insufficient.

Do not create duplicate wiki pages if an appropriate current page already exists. Improve or extend the existing page where that is the clearest fit. Do not overwrite established wiki content without understanding its existing scope and links.

## Explicitly out of scope

- No UI implementation, code changes, refactoring, styling or prototype modifications.
- No changes to main-game backend behavior, models, services, routes or data.
- No new game mechanics or UI decisions invented to fill documentation gaps.
- No silent resolution of contradictory source material.
- No deletion, movement, renaming or archival of original source documents or task files.
- No rewriting unrelated wiki areas.
- No implementation task creation or dispatch as part of this task.

If the investigation reveals a decision is needed, document the competing evidence and formulate a concise question for Tracy. Do not decide it on his behalf.

## Acceptance criteria

1. **Correct destination:** All authoritative UI wiki content and its index/navigation updates are made in `galaxyGame/docs/wiki/` in the main game repository. No authoritative UI wiki is created in the prototype repository.
2. **Source-first recovery:** The report demonstrates that current wiki material, relevant deeper/frozen documentation, task history and both repositories' actual UI implementation were investigated. Important claims link to evidence.
3. **Coverage:** All seven subject areas in Review Scope are addressed, either with recovered content or an explicit statement that the sources do not establish the relevant intent.
4. **Accurate status and provenance:** Implemented, partial, planned, proposed, placeholder/mock, unresolved and obsolete material are not conflated. Code claims are verified against the current implementation. Session-supplied decisions are not misrepresented as historical source documentation.
5. **Key distinctions preserved:** Planetary Monitor reuse-as-is-first, sphere-versus-layer distinction, Digital Twin boundaries and inspiration-versus-requirement distinction are represented accurately, with conflicts flagged.
6. **No invented decisions:** Contradictions and gaps remain visible and are listed in the report for human review. No unsupported behavior, data shape or design requirement is introduced.
7. **Wiki coherence:** New or updated pages fit the existing wiki organization, have useful cross-links and are reachable through the wiki's navigation/index.
8. **Scope respected:** No implementation, refactoring, source deletion/archive or unrelated documentation rewrite occurs.
9. **Reviewable report:** A concise Markdown recovery report is saved in the agent-tasks summaries directory and identifies deliverables, source evidence, unresolved questions and acceptance-criteria results.

## Ordered workflow

1. Complete required reading after the dispatch Step 0.
2. Create the recovery report Markdown file in `projects/galaxy_game/summaries/` before beginning source investigation or other task work, as required by the dispatch contract and guardrails. Start with the report headings/template and mark sections pending; update it as evidence is gathered and verified.
3. Before source inventory, verify the live state of both the `galaxyGame` and `galaxy-game-ui` checkouts. For each, record the current branch and working-tree state, and the last five commits. For the prototype, record the tips of both `main` (first-attempt salvage; read-only) and `review/qwen-ui-prototype` (clean prototype; intended current implementation), and identify which branch each code finding comes from. Record whether the local git-ignored first-attempt reference directory exists; if present, identify it as a read-only local reference, not a tracked branch. Do not assume handoff-reported implementation status is current.
4. Inspect the existing main-game wiki structure and identify relevant existing pages before drafting anything.
5. Build a source inventory for the seven subject areas. Read relevant source documents in context, including older/frozen documents and related task history.
6. Inspect the corresponding main-game and prototype code. Record concrete implementation evidence and distinguish it from design intent. Attribute every prototype code finding to its branch (and, where relevant, the local ignored reference tree).
7. Compare sources. Record contradictions, missing intent, stale statements and implementation/documentation divergence. Do not resolve disagreements silently.
8. Draft or update the UI wiki pages in the main `galaxyGame/docs/wiki/` repository, preserving existing organization and cross-links.
9. Update the appropriate wiki index/navigation.
10. Finalise the recovery report in `projects/galaxy_game/summaries/` in the agent-tasks repository, including the verified source inventory, branch attribution, findings and acceptance-criteria results.
11. Verify every acceptance criterion. Review the resulting diffs and ensure only the intended documentation files changed.
12. Stop and present the report, changed-file list and unresolved questions for Tracy's review. Do not commit or push without explicit human approval, consistent with the project guardrails.

## Stop and approval gates

- If a source repository or relevant documentation area is inaccessible, report the limitation; do not claim it was reviewed.
- Stop and ask Tracy before proceeding if the `galaxyGame` checkout is on a branch not authorized by the current task/session instructions, or if `galaxyGame/docs/wiki/` has pre-existing uncommitted changes. Do not overwrite, reset, stash or otherwise alter those changes.
- If either checkout has other pre-existing uncommitted changes, record them and avoid touching them; stop if they overlap the intended files or make the requested work unsafe.
- For prototype inspection, treat `review/qwen-ui-prototype` as the clean implementation branch and `main` as read-only first-attempt salvage, consistent with `BRANCHES.md`. If the available checkout/branch layout does not permit that distinction, stop and ask rather than attributing findings to the wrong source.
- If the wiki structure is unclear or a proposed change would substantially reorganize unrelated pages, stop and ask before proceeding.
- If sources conflict on a material design decision, document both sides and leave the decision open.
- Do not implement UI changes or create follow-on implementation tasks.
- Do not commit or push without explicit human approval.
- Follow the applicable project closeout instructions after the human reviews and authorizes completion.

## Recovery report outline

The report should be concise and include:

1. **Scope and repositories inspected** — include current branch, working-tree state and last five commits for both code repositories; include both prototype branch tips and branch attribution for code findings.
2. **Evidence labels used** — use `VERIFIED` for directly inspected repository/code evidence, `REPORTED` for status stated in a handoff or other report but not independently verified, and `SESSION-DIRECTION` for direction supplied by Tracy in the planning session. These labels are report-only.
3. **Source inventory** — grouped by the seven UI subject areas.
4. **Wiki pages created or updated**
5. **Recovered design intent** — concise summary with source references.
6. **Implementation comparison** — implemented / partial / planned / placeholder / not found, with branch and commit attribution for code findings.
7. **Contradictions and unresolved questions** — source A, source B, why they differ, decision needed.
8. **Potentially obsolete material** — references only; no deletion or archival.
9. **Acceptance criteria** — pass/fail with brief evidence.
10. **Files changed**
11. **Recommended next discussion points** — no implementation plan unless explicitly requested.

---

**Draft note:** This task intentionally creates the authoritative UI wiki in the main `galaxyGame` repository. The task and its recovery report are tracked in `agent-tasks`. It is not an instruction to dispatch Qwen or begin implementation.
