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

## First dispatch priority: JSON data lookup/loading

The first task to review and prepare for dispatch is:

`tasks/drafts/2026-11-19-HIGH-PORT-JSON-LOOKUP-SERVICES.md`

The old fixture-loader specification has been retired. The current task is narrowly scoped to the minimal Sol test JSON and the Planetary data path.

The agreed first slice is specific: copy the main application's minimal `sol.json` test system into the prototype's corresponding `app/data/star_systems/sol.json` location, adapt lookup sufficiently to resolve Sol and Earth, and make that data available through the existing Rails controller as `@planetary_data`. The provider boundary must be replaceable by a future API provider without changing frontend code. See the [Planetary data source and frontend boundary](https://github.com/trmccormick/galaxyGame/blob/main/docs/wiki/ui/02_player_view_architecture.md) section in the authoritative wiki.

This task does not port every lookup service, build an API, or supply terrain/live simulation data. Its position as first priority does not itself authorize implementation.

## Subsequent view-by-view review

**The Sol JSON lookup/provider task must be completed before Planetary View work resumes.** The Planetary task consumes its `@planetary_data` controller payload. Do not dispatch the two tasks in parallel. After that dependency is satisfied, continue reviewing other prototype view tasks individually. Do not assume the old folder order is the final dispatch order.

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
| 1 | `tasks/drafts/2026-11-19-HIGH-PORT-JSON-LOOKUP-SERVICES.md` — Sol JSON lookup/provider | **Revised; next to dispatch after Tracy's explicit approval** | First; must resolve Sol and Earth and expose `@planetary_data` |
| 2 | `tasks/drafts/2026-11-19-MEDIUM-FEATURE-REVIEW-PLAYER-PLANETARY-VIEW.md` — Planetary data integration | **Revised; blocked** | Starts only after task 1 is completed |
| 3+ | Remaining individual view tasks | **Pending individual review** | Order to be decided from verified dependencies and Tracy's direction |

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
