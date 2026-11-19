# GalaxyGame UI Prototype — Task Review and Dispatch Plan

**Status:** Planning and task review only  
**Owner:** Tracy  
**Updated:** 2026-11-19

## Purpose

This document defines how the GalaxyGame UI prototype task queue will be reviewed, ordered, approved, and dispatched. The queue is being reconciled before implementation resumes. A task's presence in `drafts/`, or a previous GO/review disposition, is not permission to dispatch it.

Tracy directs the review and retains approval of task changes and dispatch.

## Current approach

**Keep task files thin.** The wiki holds design intent, architecture, domain behavior, and detailed requirements. Task files should point to the relevant wiki pages and source locations, then state only the specific objective, bounded execution scope, dependencies, exclusions, and measurable acceptance criteria. Do not copy wiki material into tasks or turn tasks into parallel design documents. If the right wiki reference is not known, identify it during review rather than inventing a path.


1. **Review every task file individually.** Work through the existing task files with Tracy, one file at a time. Do not batch-approve, rewrite, promote, archive, or discard tasks without direction.
2. **Use the actual task file as the review unit.** Read its full contents, compare it with current prototype code, current project guidance, and relevant authoritative GalaxyGame wiki decisions. Identify stale assumptions, completed work, conflicts, dependencies, and scope problems.
3. **Reconcile before ordering.** For each file, decide with Tracy whether it should be revised, retained for later, merged into another task, or left in `hold_for_review/`. Keep one task focused on one view or one clearly bounded enabling capability.
4. **Revise the task before dispatch.** Make its objective, implementation scope, exclusions, prerequisites, acceptance criteria, and verification steps specific to the current state. A placeholder is not implementation-ready.
5. **Build the dispatch order from dependencies.** Record the agreed order here as the review progresses. Do not infer approval from priority labels or folder placement.
6. **Dispatch only one task at a time.** Wait for Tracy's explicit selection/approval before moving a task to `active/` or handing it to an implementation agent. No parallel dispatch unless Tracy explicitly changes this rule.

## Agreed review and dispatch sequence

The first coordinated implementation slice is:

1. **Landing page + system JSON reader:** `tasks/drafts/2026-11-19-HIGH-IMPLEMENT-LANDING-PAGE-SYSTEM-OVERVIEW.md`
2. **Player Planetary data integration:** `tasks/drafts/2026-11-19-MEDIUM-FEATURE-USE-LOADED-DATA-PLAYER-PLANETARY-VIEW.md`

The landing page and its supporting Rails-side JSON reader are one task, not separate sequential tasks. The prototype wiki holds the detailed Sol fixture semantics, system-data boundaries, page layout and network-context decisions. The task file is intentionally thin and points back to those wiki pages.

The previous Earth-only lookup task has been marked superseded and merged into the combined landing-page task. The legacy 2026-10-23 landing-page task remains in `hold_for_review/` as historical reference; it is not a separate implementation step and must not be dispatched as written.

Player Planetary remains a later, separate view task. Reassess its data dependency after the landing-page reader is implemented and the landing page has been reviewed. Do not assume the landing-page payload is automatically the correct Planetary payload.

This sequence is a planning record, not authorization to dispatch. Dispatch only the single task Tracy explicitly approves.

## Subsequent view-by-view review

After these three items, continue reviewing other prototype view tasks individually. Do not assume the old folder order is the final dispatch order.

For each view task, establish:
- the intended audience and purpose of that view;
- what is already implemented and what remains;
- which values come from existing game JSON lookup data;
- which values, if any, are dynamic state and therefore need temporary mock state;
- the specific visual/interaction outcome and measurable acceptance checks;
- dependencies on the lookup task or another reviewed task.

Keep the established one-view-at-a-time approach. Do not bundle multiple pages into a broad redesign task merely for convenience.

## Implementation boundaries

- The main GalaxyGame application is Rails 7; the separate UI prototype is Rails 8.1. Keep their codebases and runtime assumptions distinct.
- The prototype work is primarily UI work. Do not add models, migrations, general controllers/APIs, persistence, or simulation behavior unless Tracy explicitly approves a separately bounded requirement.
- Reuse existing game JSON definitions and lookup behavior; do not duplicate static game definitions in prototype mock data.
- Mock only dynamic state not supplied by the existing definitions.
- Treat current code as evidence of what exists, not as authority to silently change design intent. Use the authoritative GalaxyGame wiki for design decisions and record unresolved conflicts rather than guessing.
- Do not redo completed work. Verify the current page before writing acceptance criteria.
- Keep tasks small, direct, and testable. Avoid speculative future scope and generic kickoff/compliance work.

## Review record and dispatch order

Maintain this section as Tracy reviews and decides each task. Only record a disposition after it has been discussed and agreed.

| Order | Task / view | Review disposition | Dependency / dispatch condition |
|---|---|---|---|
| 1 | `tasks/drafts/2026-11-19-HIGH-IMPLEMENT-LANDING-PAGE-SYSTEM-OVERVIEW.md` — landing page + JSON reader | **New combined draft; awaiting review** | First; implement the existing landing page using the copied five-system JSON data |
| 2 | `tasks/drafts/2026-11-19-MEDIUM-FEATURE-USE-LOADED-DATA-PLAYER-PLANETARY-VIEW.md` — Planetary data integration | **Revised; dependency to reassess** | Review after the landing page is implemented and reviewed |
| Superseded | `tasks/drafts/2026-11-19-HIGH-PORT-JSON-LOOKUP-SERVICES.md` — Earth-only lookup | **Merged into order 1** | Do not dispatch independently |
| Hold | `tasks/hold_for_review/2026-10-23-CRITICAL-LANDING-PAGE-FOR-PUBLIC-ACCESS.md` — legacy landing task | **Retained as historical reference** | Do not dispatch as written |
| Later | `tasks/drafts/2026-11-19-MEDIUM-FEATURE-ADAPT-CRAFT-OPERATIONS-INTERACTIONS.md` — craft operations interactions | **New draft; not approved** | Review after the initial sequence; choose a concrete Player or Admin workflow before dispatch |

For every reviewed file, record its exact path and one agreed outcome:
- **Revise in drafts** — update the existing task to reflect current decisions.
- **Keep on hold** — retain for later review; no dispatch.
- **Merge** — identify the surviving task and preserve any useful requirements without duplicate work.
- **Retire** — only when Tracy explicitly agrees it is obsolete or completed.

## Task lifecycle

- `drafts/`: working tasks being reviewed or prepared; not approved for implementation by folder location alone.
- `hold_for_review/`: older or unresolved tasks retained for reference; not dispatchable until brought forward and reviewed.
- `active/`: only the single task Tracy has explicitly approved for dispatch.
- `completed/`: tasks whose work and closeout have been verified.

When dispatch is approved, follow the task template and the repository's required agent dispatch/closeout instructions. Move the selected task using the prescribed Git lifecycle, update its status, verify there is only one copy, and report the exact task and commit. Do not change task status or promote files merely because a review is complete.

## Approval rule

**Tracy controls the order and the dispatch decision.** The assistant may inspect, compare, identify dependencies, and recommend an order. It must stop for Tracy's direction before changing a task's disposition in a way that promotes, retires, or dispatches it, and before asking an implementation agent to begin work.
