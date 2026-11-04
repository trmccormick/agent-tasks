# Galaxy Game UI Prototype — Draft Work Proposal

**Updated:** 2026-11-04  
**Purpose:** A small, ordered set of feature tasks for the Galaxy Game UI prototype.

This folder contains proposed work only. A task remains in `drafts/` until Tracy reviews and explicitly authorizes its dispatch. Qwen should receive one small task at a time. Do not combine unrelated screens, components, data work, or research into one task.

## What this project is

This is a **visual and interaction prototype** for Galaxy Game, developed separately from the main game. It is not a second implementation of the game backend. Its purpose is to explore how Galaxy Game information and workflows should be presented, using real Galaxy Game JSON/data shapes where they are available and clearly identified mock data only where the prototype needs it.

The prototype should follow Galaxy Game's established design intent and terminology. It must not invent game rules, data, or feature behavior merely to fill a screen. When intent or data is unknown, show that it is unknown and raise the question for Tracy.

## Design research to preserve

The research into other applications is part of the design foundation, not incidental inspiration:

- **EVE Dashboard** — primary reference for dashboard composition, information density, navigation, panels, tables, status indicators, visual hierarchy, and interaction patterns. Its waypoint/autopilot JavaScript is a specific reuse candidate; inspect the original and first-attempt copies before rebuilding anything.
- **EVE Tycoon** — reference for market and industry workflow organization. Use it to study how related information and actions are grouped; do not copy EVE gameplay concepts or assume Galaxy Game has identical mechanics.
- **EVEpraisal** — reference for bulk item valuation and presenting valuation results. Treat this as a workflow/presentation reference, not a source of Galaxy Game prices or economic rules.
- **Other researched applications** — retain useful interaction and information-architecture patterns where they solve a Galaxy Game prototype problem. Record the source and why a pattern is relevant when it is brought into a task.

These applications inform the prototype's presentation and workflow design. Galaxy Game's own wiki, code, and JSON remain authoritative for Galaxy Game meaning and data. Do not reproduce another game's branding, domain logic, or mechanics.

## First-attempt material: preserve and harvest selectively

The first attempt is a **read-only reference**. It is not a second working tree and must not be modified, staged, committed, reset, or deleted as part of these tasks.

Known sources:

- UI repository `main`: first-attempt work preserved separately from `review/qwen-ui-prototype`. Commit `8ce1250` contains `eve_dashboard.scss` and `dashboard.scss`; inspect commit contents and later history before deciding what remains useful.
- Agent-tasks archive: `projects/galaxy-game-ui-first-attempt/`, including `CSS-INTEGRATION-STRATEGY.md`, `EVE-DASHBOARD-CSS-REFERENCE.md`, `GALAXY-GAME-UI-EXTRACTION-ANALYSIS.md`, `EVE-DASHBOARD-FOUNDATION-PLAN.md`, `DESIGN_SESSION_BRIEF.md`, `MAIN-PROJECT-UI-RESEARCH.md`, and its original task files.
- Local ignored backup: `/Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui-first-attempt`. Qwen must inspect this local tree for files and work not present in GitHub, especially the EVE Dashboard waypoint JavaScript and any already-adapted version.

Harvest rules:

1. Inspect the current prototype and Git history before deciding something is absent.
2. Inspect the local ignored backup read-only. Do not run cleanup or broad copy commands against it.
3. Copy only the specific reusable file or code needed into the current prototype, preserving a note of its original path and commit/source.
4. Adapt to the current app's Rails/Tailwind setup and current design tokens; do not import an obsolete CSS foundation wholesale.
5. Verify the harvested feature in the current app. Do not treat existence in a backup or old commit as proof it works in the current prototype.
6. Never recreate a component until the audit establishes that a suitable version does not already exist.

## Existing task disposition

The older task records are source material, not automatic dispatch instructions:

- `2026-10-23-CRITICAL-LANDING-PAGE-FOR-PUBLIC-ACCESS.md`: the landing page was subsequently ported. Reassess remaining branding, logo, navigation state, and placeholder-content issues as separate small tasks; do not rerun the original population task.
- `2026-10-30-MEDIUM-REFACTOR-SIGNIN-VIEW-GALAXY-GAME-STYLE.md`: retain as historical context. Sign-in work was pushed, but remaining visual and interaction verification needs a focused current-state task.
- `2026-10-23-HIGH-JSON-FIXTURE-LOADER-SYSTEMS.md` and `2026-10-23-HIGH-DASHBOARD-OVERVIEW-FROM-FIXTURES.md`: do not dispatch unchanged. Reconcile them against current code, fixtures, the prototype's purpose, and the main game's actual JSON before creating any replacement data or dashboard tasks.
- `INCREMENTAL-TASK-PLAN.md` Tasks 4–8 and the first-attempt foundation plan: historical planning only. They predate the clean prototype and contain approaches that were superseded (including the old MockDataHelper and wholesale EVE CSS import). Extract still-useful feature intent; do not execute those plans as written.
- First-attempt sign-in/dashboard styling task: retain its visual observations as reference, but split its combined scope into one-screen/one-feature tasks.

## Proposed sequence

Only one implementation task should be dispatched at a time. The audit tasks come first where the current state or salvageable work is uncertain.

| Order | Draft task | Purpose | Dependency / gate |
|---:|---|---|---|
| 1 | `2026-11-04-HIGH-AUDIT-WAYPOINT-JS-RECOVERY.md` | Read-only audit of waypoint code in the current branch, first-attempt Git history, and local ignored backup | First; no implementation |
| 2 | `2026-11-04-MEDIUM-FINALIZE-SIGNIN-BRANDING.md` | Finish and verify Galaxy Game branding on the sign-in screen without changing authentication behavior | Current sign-in state checked before edits |
| 3 | `2026-11-04-MEDIUM-REPLACE-LANDING-LOGO.md` | Replace/align the landing page logo treatment using the approved Galaxy Game asset | Confirm actual logo asset and intended variant before editing |
| 4 | `2026-11-04-MEDIUM-PORT-WAYPOINT-NAVIGATION.md` | Adapt the existing waypoint JavaScript into the current prototype only if the audit confirms a reusable source | Blocked by task 1 and Tracy's review of its findings |

The sequence is deliberately conservative. The sign-in and landing tasks are independent of waypoint recovery, but should still be run one at a time and visually reviewed before moving on.

## Later work — do not dispatch from this proposal alone

After the initial sequence, review the current dashboard and the research materials to define separate tasks for individual prototype features. Likely areas include:

- Dashboard information layout and navigation.
- System/route wayfinding and waypoint interactions.
- Player assets and inventory presentation.
- Market overview and order workflows, informed by EVE Tycoon.
- Bulk valuation presentation, informed by EVEpraisal.
- Industry/manufacturing workflow presentation, informed by EVE Tycoon and Galaxy Game's own design/data.
- Additional spacecraft, location, and operations views where the prototype benefits from them.

Each should become its own small task only after checking what is already implemented and grounding the feature in Galaxy Game's wiki/code/data. Do not create one mega-dashboard task or one task that builds several panels.

## Rules for every draft task

- One task = one small feature or one bounded verification/audit.
- State whether the task is read-only or may modify the current prototype.
- Name the exact current-repo paths to inspect or change; verify paths before dispatch.
- Include relevant first-attempt source paths and commit references when applicable.
- Preserve existing Rails authentication and working behavior unless a task explicitly and separately authorizes a change.
- Use the current project guidance and current design tokens; old task examples are not authoritative.
- Include measurable acceptance criteria and a clear out-of-scope list.
- Do not commit, push, merge, or modify the first-attempt backup without Tracy's explicit approval.
- Do not dispatch multiple tasks in parallel.

## Startup references

Before implementation, Qwen should follow the current session-start instructions and read:

1. `rules/GUARDRAILS.md`
2. `projects/galaxy-game-ui/README.md`
3. `projects/galaxy-game-ui/NEEDS_REVIEW.md`
4. `projects/galaxy-game-ui/SESSION_GUIDANCE.md`
5. The latest relevant handoff.
6. This README and the single task being dispatched.

Where a task references the main game's design intent, check the current `galaxyGame/docs/wiki/` first. If code, wiki, and old planning notes disagree, report the conflict rather than resolving it silently.
