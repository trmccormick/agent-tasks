---
status: draft
resume: false
priority: MEDIUM
type: architecture
system_domain: TERRA_SIM
mvp_alignment: OTHER
local_worker_safe: true
---

## Agent Dispatch Interface

**Status**: DRAFT — NOT READY FOR DISPATCH. Human review and approval are required first.

This draft does not authorize implementation. The human must review and approve the task, move it through the prescribed lifecycle, and complete dispatch details before any agent begins work.

## Draft status

This is a review-only draft. It is not approved for backlog or implementation dispatch.

No implementation, formula changes, fixture creation, or test-suite changes are authorized by this draft.

# TASK: GalaxyGame Calculation Verification and Regression Baselines

**Status**: DRAFT  
**Priority**: MEDIUM  
**Type**: architecture  
**Created**: 2026-11-05  
**Author**: ChatGPT (draft for human review)

## Context

A review of TriffTools identified a useful engineering pattern in its isolated Pyfa/eos fitting service: the application documents the calculation authority, reports the installed engine version, and uses parity snapshots to detect changes in calculated results.

GalaxyGame has several interdependent calculation domains, including orbital/transit mechanics, planetary energy and climate, atmosphere and hydrosphere behavior, resource extraction, manufacturing, power, and terraforming. Existing targeted specs and domain-specific validation tasks may already cover parts of this. This task must discover and map existing coverage before proposing additions.

The external reference is:
- TriffTools fitting-service: https://github.com/NarcisussX/Triff.Tools-Source/tree/main/fitting-service
- TriffTools hardening/parity notes: https://github.com/NarcisussX/Triff.Tools-Source/blob/main/fitting-service/docs/HARDENING.md

These are examples of process and test design, not a recommendation to adopt Pyfa, Python, or a separate calculation service.

## Purpose

Assess whether GalaxyGame would benefit from a consistent calculation-verification approach using documented calculation authorities, representative known-input/known-output fixtures, regression tests, and explicit review of intentional baseline changes.

This is a research and design task. It must not recalibrate simulation formulas or change application code.

## Review scope

1. Inventory important calculation domains and identify their current source of truth.
2. Inspect existing specs, fixtures, validation tasks, frozen design documents, and wiki pages.
3. Identify calculations where a small change could create downstream effects across other systems.
4. Determine whether existing tests already provide adequate deterministic regression coverage.
5. Propose a proportionate approach for any gaps. Options may include focused RSpec examples, fixture-based scenarios, reference snapshots, or domain-specific validation reports.
6. Explain how intentional formula changes should be reviewed and how expected values should be updated without normal tests silently blessing new results.
7. Identify calculations that are stochastic, time-dependent, generated, or otherwise unsuitable for strict fixed-value snapshots; recommend appropriate tolerances or invariant/property checks instead.

## Required constraints

- Code and established wiki intent take precedence over this external example.
- Do not assume every calculation needs a snapshot.
- Do not create a universal calculation framework unless the audit demonstrates a real need.
- Do not introduce a new language, runtime, service, gem, database table, or dependency.
- Do not change calculation formulas, production behavior, fixtures, or specs.
- Respect GalaxyGame's targeted RSpec rules, including `unset DATABASE_URL` and sequential execution, if any validation is explicitly authorized later.
- Report stale or conflicting task/document assumptions rather than resolving architectural questions independently.

## Deliverable

Save a synthesis report in `projects/galaxy_game/summaries/` containing:

- Calculation-domain inventory and current authorities.
- Existing verification coverage, with concrete file paths.
- Gaps and risk ranking.
- Recommended minimal regression strategy, if justified.
- Examples of candidate baseline scenarios and why they matter.
- Open design decisions and dependencies.
- Explicit recommendation: no change needed, documentation-only follow-up, or one or more narrowly scoped future tasks.

## Acceptance criteria

- [ ] Relevant calculation domains and existing tests are inventoried from current code.
- [ ] Existing validation tasks and wiki decisions are checked to avoid duplicate work.
- [ ] The report distinguishes verified coverage from assumptions and gaps.
- [ ] Any proposed fixture/snapshot approach accounts for nondeterministic calculations.
- [ ] No code, formulas, tests, fixtures, dependencies, or runtime configuration are changed.
- [ ] The report recommends bounded follow-up work rather than a broad framework by default.

## Source note

TriffTools is an inspiration for version-aware calculation verification and parity testing only. GalaxyGame's simulation architecture and existing test conventions remain authoritative.
