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

The current sequence is:

1. **JSON lookup/provider:** `tasks/drafts/2026-11-19-HIGH-PORT-JSON-LOOKUP-SERVICES.md`
2. **Landing page:** `tasks/hold_for_review/2026-10-23-CRITICAL-LANDING-PAGE-FOR-PUBLIC-ACCESS.md` (legacy task; revise/reconcile against the already-built landing page, current data, and logo-export issue before it can be considered ready).
3. **Player Planetary data integration:** `tasks/drafts/2026-11-19-MEDIUM-FEATURE-USE-LOADED-DATA-PLAYER-PLANETARY-VIEW.md`

The first task copies the actual minimal Sol JSON sample, resolves Sol/Earth, and exposes the focused controller payload. It does not port every lookup service, build an API, or supply terrain/live simulation data. The provider boundary must remain replaceable by a future API provider without changing frontend code. See the [Planetary data source and frontend boundary](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md).

The landing page is deliberately second, before Player Planetary integration. Its existing hold-for-review task is old and must be reconciled rather than dispatched as written: the page has already been built, and the task must reflect current implementation, actual system data, clearly labeled/non-live metrics, and the logo-export correction. The exact landing-page task revision remains a review item.

Player Planetary is third and consumes the focused Earth payload produced by the first task. It must not start before that dependency is complete.

These three tasks are sequential; do not dispatch them in parallel. Their ordering is a planning decision, not authorization to dispatch any task.

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
| 1 | `tasks/drafts/2026-11-19-HIGH-PORT-JSON-LOOKUP-SERVICES.md` — Sol JSON lookup/provider | **Revised; awaiting explicit dispatch approval** | First; resolve Sol and Earth and expose the focused controller payload |
| 2 | `tasks/hold_for_review/2026-10-23-CRITICAL-LANDING-PAGE-FOR-PUBLIC-ACCESS.md` — landing page | **Needs reconciliation/revision** | Second; update the legacy task to match the already-built page, actual system data, non-live metrics, and logo-export correction |
| 3 | `tasks/drafts/2026-11-19-MEDIUM-FEATURE-USE-LOADED-DATA-PLAYER-PLANETARY-VIEW.md` — Planetary data integration | **Revised; blocked** | Third; starts only after task 1 is complete and landing-page work is addressed |
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
