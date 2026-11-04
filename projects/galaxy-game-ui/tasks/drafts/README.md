# Galaxy Game UI — Draft Tasks

**Updated:** 2026-11-04

This folder contains proposed, not-yet-authorized work for the Galaxy Game UI prototype. Tracy reviews each task before dispatch. Qwen should receive **one small task at a time**.

For project and domain context, use the existing [Galaxy Game project README](../../../galaxy_game/README.md), the current [Galaxy Game UI project guidance](../../README.md), and the main game's current wiki in `galaxyGame/docs/wiki/`. This drafts README is only a work proposal; it should not repeat the project description.

## Design references

The earlier research into other applications remains relevant to prototype presentation and workflows:

- **EVE Dashboard:** primary reference for dashboard layout, navigation, panels, information hierarchy, and waypoint JavaScript.
- **EVE Tycoon:** reference for organizing market and industry workflows.
- **EVEpraisal:** reference for bulk valuation input and results presentation.
- **Other researched apps:** use selectively when a specific interaction or information-architecture pattern solves a prototype need.

These are presentation references only. Galaxy Game's own wiki, code, and JSON determine Galaxy Game terminology, rules, and data. Do not import another game's branding or gameplay logic.

## First-attempt recovery

Before recreating a feature, inspect the current prototype, Git history, and the read-only local backup:

`/Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui-first-attempt`

The backup must not be modified. In particular, audit the EVE Dashboard waypoint JavaScript and any adapted copy already present in the first attempt. Copy only a verified, useful piece into the current prototype, with its source recorded. Do not wholesale-import the old CSS foundation.

## Proposed task sequence

| Order | Draft task | Scope |
|---:|---|---|
| 1 | `2026-11-04-HIGH-AUDIT-WAYPOINT-JS-RECOVERY.md` | Read-only audit of waypoint code in the current branch, first-attempt Git history, and local backup |
| 2 | `2026-11-04-MEDIUM-FINALIZE-SIGNIN-BRANDING.md` | Finish and verify Galaxy Game branding on the sign-in screen; preserve authentication behavior |
| 3 | `2026-11-04-MEDIUM-REPLACE-LANDING-LOGO.md` | Align the landing page logo with the approved Galaxy Game asset |
| 4 | `2026-11-04-MEDIUM-PORT-WAYPOINT-NAVIGATION.md` | Conditional port of existing waypoint code, only after the audit and Tracy's approval |

Dispatch one task at a time. Task 4 remains blocked until task 1 is reviewed. Confirm the correct logo asset/variant before task 3 changes it.

## Later feature work

After the initial tasks, review the current prototype and research to identify individual, small tasks for dashboard layout, navigation/wayfinding, market workflows, bulk valuation, and industry/manufacturing presentation. Do not bundle several screens or features into a single dashboard task. Check existing code and source material before drafting new work.

## Draft task requirements

Each task must:

- Cover one small feature or one bounded read-only audit.
- State whether it is read-only or may modify the current prototype.
- Identify exact paths and relevant source commits/files.
- Preserve existing working behavior unless a change is explicitly in scope.
- Include clear acceptance criteria and out-of-scope items.
- Avoid modifying the first-attempt backup.
- Avoid committing, pushing, or dispatching without Tracy's explicit authorization.

