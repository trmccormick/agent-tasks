---
status: draft
resume: false
priority: MEDIUM
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: true
---

## Draft status

This is a review-only draft. It is not approved for backlog or implementation dispatch.

## Agent Dispatch Interface

**Status**: DRAFT — NOT READY FOR DISPATCH. Human review and approval are required first.

No job, worker, scheduler, service, or simulation behavior changes are authorized by this draft.

# TASK: Simulation Processing and AI Manager Responsibility Boundaries Review

**Status**: DRAFT  
**Priority**: MEDIUM  
**Type**: architecture  
**Created**: 2026-11-05  
**Author**: ChatGPT (draft for human review)

## Context

TriffTools separates its web backend from background market workers and a specialized fitting calculation service. That illustrates how request handling, scheduled/background work, and specialized computation can have distinct responsibilities.

GalaxyGame already has Rails services, jobs, schedulers, and AI Manager components. The project also has established simulation-time and AI Manager rules. This review must map the actual current architecture before proposing changes; it must not assume that TriffTools' Node/worker/service layout should be reproduced.

A central GalaxyGame design boundary to preserve is that the simulation is authoritative. The AI Manager plans and directs work; it does not become a second independent source of physical or economic truth. The AI Manager's ability to continue operating without direct player communication must also be preserved.

External reference:
- TriffTools repository: https://github.com/NarcisussX/Triff.Tools-Source
- README and Docker Compose describe its backend/worker separation.

## Purpose

Review how GalaxyGame currently divides player-facing requests, simulation-time advancement, scheduled/background processing, AI Manager planning, and physical operation execution. Determine whether the boundaries are clear, reliable, and testable as the simulation grows.

This is an architecture assessment, not an implementation task.

## Review scope

1. Trace the real production path for simulation time advancement and identify which components own elapsed game time.
2. Inventory scheduled jobs, background jobs, rake tasks, service orchestrators, and other execution paths that affect persistent world state.
3. Identify duplicate or competing execution paths for the same domain behavior, including any risks of work being run both by simulation ticks and independent schedulers.
4. Map the AI Manager lifecycle: planning, task assignment, execution/status observation, recovery, and operation while player communications are unavailable.
5. Identify which component is authoritative for resource quantities, production, transport, facility state, and other persistent simulation outcomes.
6. Review idempotency, retry behavior, failure reporting, ordering, and concurrency assumptions where work can be repeated or overlap.
7. Determine whether existing architecture documentation and wiki pages accurately describe the live code.
8. Recommend whether any boundary clarification, instrumentation, or narrowly scoped follow-up is warranted.

## Required constraints

- Do not move work into new workers or services merely to imitate TriffTools.
- Do not change the simulation clock, tick cadence, game speed, scheduler, job queue, or AI Manager behavior.
- Do not redesign the AI Manager or introduce a second simulation authority.
- Do not assume Sidekiq, Redis, or another dependency is appropriate without verifying current configuration and documented decisions.
- Do not modify code, tests, database schema, runtime configuration, or deployment files.
- Treat existing task history and current wiki intent as important evidence; surface contradictions for human review.

## Deliverable

Save a synthesis report in `projects/galaxy_game/summaries/` containing:

- Current execution architecture and verified entry points.
- A responsibility map for web requests, simulation processing, AI Manager, and physical operations.
- Any duplicated/competing execution paths and their concrete consequences or risks.
- Existing safeguards and missing observability.
- Documentation/code mismatches.
- A recommended boundary model, clearly separating existing behavior from proposed design.
- Bounded follow-up tasks, if needed, with dependencies and priority suggestions.

## Acceptance criteria

- [ ] The live execution paths are traced from entry point to persistent state change.
- [ ] Simulation time authority and AI Manager responsibility are explicitly identified.
- [ ] Duplicate scheduling/execution risks are verified rather than inferred from filenames.
- [ ] Relevant existing tasks, wiki pages, and prior audits are checked for overlap.
- [ ] The report does not prescribe a service split unless evidence justifies it.
- [ ] No application code, tests, schema, configuration, or runtime behavior is changed.

## Source note

The TriffTools example supports the general idea of clear processing boundaries. GalaxyGame's existing Rails architecture, simulation authority, and AI Manager design remain controlling.
