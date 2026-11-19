---
status: draft
resume: false
priority: HIGH
type: feature
system_domain: DATA LOOKUP / SERVICES
mvp_alignment: OTHER
local_worker_safe: true
last_updated: 2026-11-19
---

# Port the main game's JSON lookup services

## Objective

Provide the Rails 8.1 UI prototype with the existing GalaxyGame JSON lookup capability, adapted from the Rails 7 main application. This is the first enabling task in the reviewed dispatch order.

## Read and reference

- Project rules: `projects/galaxy-game-ui/SESSION_GUIDANCE.md`
- Main GalaxyGame wiki: start at `docs/wiki/README.md`; use the relevant current architecture/data pages as the design authority.
- Main application reference implementation: `app/services/lookup/` and its shared path/configuration dependencies.
- Prototype repository: inspect the current Rails 8.1 app before deciding what to add or reuse.

The task is an execution brief, not a substitute for the wiki or source code. Confirm exact wiki page links and source files during implementation planning; do not copy their contents into this file.

## Scope

- Inspect the main application's lookup services and dependencies, then adapt the required JSON lookup behavior for the prototype.
- Preserve relevant path resolution, source precedence, normalization, lookup semantics, and caching behavior where they apply.
- Reuse the existing game JSON definitions; do not create parallel hand-authored copies of static game data.
- Identify the smallest prototype-facing interface needed by the existing UI views.

## Boundaries

- Keep this to JSON data lookup/loading in the Rails 8.1 prototype.
- Do not add unrelated models, migrations, persistence, general-purpose APIs/controllers, or simulation logic.
- Do not blindly copy Rails 7 code; verify compatibility and adapt only where necessary.
- Do not expand into individual view redesigns. Those remain separate reviewed tasks.
- Where wiki, source code, and prototype assumptions conflict, record the conflict and ask Tracy rather than inventing behavior.

## Acceptance criteria

- [ ] The implementation is based on the actual main-app lookup services and their required dependencies.
- [ ] The prototype can resolve and load the required existing game JSON definitions through a clear, tested interface.
- [ ] Relevant source/path precedence and lookup behavior are preserved or any intentional differences are documented and approved.
- [ ] No duplicate static game definitions or unrelated backend infrastructure are introduced.
- [ ] Focused tests cover successful lookup, missing/invalid input or data, and relevant path/source behavior.
- [ ] The prototype's existing test suite and relevant Rails checks pass.
- [ ] Any wiki gaps or unresolved design conflicts are reported for Tracy's decision.

## Dependencies

**Blocked by:** none  
**Blocks:** reviewed UI tasks that need shared game JSON lookup data.

## Dispatch note

This task is first in the proposed order, but remains a draft until Tracy approves the final scope and explicitly dispatches it.
