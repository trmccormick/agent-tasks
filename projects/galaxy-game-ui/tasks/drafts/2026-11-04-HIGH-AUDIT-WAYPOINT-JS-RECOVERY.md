---
status: drafts
resume: false
priority: HIGH
type: audit
system_domain: JAVASCRIPT / WAYFINDING
mvp_alignment: OTHER
local_worker_safe: true
---

# Audit Existing EVE Dashboard Waypoint JavaScript for Prototype Reuse

## Goal

Determine whether the EVE Dashboard waypoint/autopilot JavaScript was already copied or adapted during the first Galaxy Game UI prototype, and whether any of that work is already present in the current prototype. This is a **read-only audit**. Do not implement, port, refactor, or delete anything.

The goal is to avoid rebuilding a feature that may already exist in the local first-attempt backup or in preserved Git commits.

## Required sources

Inspect all three sources:

1. Current UI repository, working branch `review/qwen-ui-prototype`.
2. UI repository `main` and relevant first-attempt history, especially commit `8ce1250` and later commits that may contain extracted assets or JavaScript.
3. Local ignored backup (read-only):
   `/Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui-first-attempt`

Also inspect the original EVE Dashboard source if it is available locally at the path referenced by earlier research. If it is not available, report that limitation; do not guess its path or substitute an unrelated implementation.

## Audit steps

1. Read the current prototype's JavaScript entry points, controllers, routes/views that relate to navigation, systems, routes, or waypoints.
2. Search the current prototype, first-attempt Git history, and local backup for waypoint/autopilot/route-planning code and related assets.
3. Identify the original EVE Dashboard source file(s), if available, and compare them with any first-attempt copy/adaptation.
4. Check whether the first attempt already has working or partially working waypoint behavior, and identify its dependencies and integration assumptions.
5. Check whether any matching implementation already exists on the current prototype branch.
6. Report the smallest reusable unit and what would need adaptation for this Rails 8/Tailwind prototype.
7. Record exact paths, branch names, commit SHAs, and relevant function/controller names. Distinguish verified findings from assumptions.

## Required output

Create a concise audit report in the project's summaries folder. Include:

- Files and commits inspected.
- Where the original waypoint implementation was found (or state that it was not available).
- Where first-attempt copies/adaptations were found.
- Whether any waypoint code already exists in the current branch.
- Dependencies, libraries, event wiring, expected input data, and DOM assumptions.
- What can be reused directly, what needs adaptation, and what should not be copied.
- A recommendation: reuse existing code, adapt a specific first-attempt version, or design a small replacement only if no usable source exists.

## Guardrails

- Read-only everywhere, especially the local ignored backup and UI repository `main`.
- Do not modify files, switch branches, cherry-pick, copy files, install packages, or run formatters.
- Do not assume the feature is absent because a filename search finds nothing; inspect relevant JavaScript and history.
- Do not treat EVE autopilot behavior as Galaxy Game design intent. This task audits reusable interaction code only.
- Do not implement the feature as part of the audit.

## Acceptance criteria

- [ ] Current prototype, preserved Git history, and local ignored backup have all been inspected.
- [ ] Exact source paths and commit references are recorded.
- [ ] Existing versus missing behavior is clearly distinguished.
- [ ] Dependencies and integration requirements are documented.
- [ ] No files in either repository or backup were changed.
- [ ] A recommendation is provided for Tracy to review before any port task is dispatched.

## Dependencies

None. This is the first waypoint-related task and must complete before any waypoint implementation task.

## Out of scope

Implementing waypoint controls, changing navigation behavior, importing EVE Dashboard assets, adding dependencies, or modifying any source files.
