# DRAFT ONLY — NOT DISPATCHED
# Mining Cadence and Rate Semantics Alignment

## Problem

The inspected mining paths use multiple time triggers:
- satellite `process_tick`;
- Sidekiq scheduler/job;
- mission task action.

The tick path does not scale mining by `time_skipped`. The scheduler passes a
four-hour interval that the job does not consume. Satellite-level fields named
`*_per_hour` are not proven payout inputs; current payout is driven by fitted
computer-unit values and multipliers.

This makes issuance cadence, rate meaning, and NPC profitability unreliable.

## Affected systems

- Satellite mining operation.
- Simulation tick processing.
- Sidekiq scheduler/job path.
- Mission task runner.
- Craft operational data and fitted computer/GPU configuration.
- AI Manager/NPC profitability and settlement decision support.

## Required outcome

After a human-approved time-model choice, establish an explicit and testable
meaning for mining rates and event cadence.

Possible time-model decision:
[FILL IN: game-time / wall-clock-event-driven / deliberate hybrid.]

## Acceptance criteria

- Every active mining rate has a defined unit and time basis.
- Exactly one source of truth determines payout cadence, or multiple triggers
  have explicit non-overlapping responsibilities.
- The selected time basis is consumed in the mining calculation.
- `time_skipped`, elapsed scheduled time, and mission-trigger semantics are
  handled intentionally and tested.
- Satellite-level and fitted-equipment rate fields have explicit documented
  roles; inactive fields are removed, deprecated, or clearly non-operational
  only after separate approval.
- AI/NPC profitability calculations can rely on a defined rate.
- No unrelated general clock redesign is introduced unless separately approved.

## Dependencies and blockers

- Requires human decision: game time, wall clock, or hybrid for NPC-only
  accelerated buildup.
- Coordinate with duplicate-credit remediation and GCC authorization policy.
- Do not infer that `*_per_hour` means real hour from field names or comments.

## Luna impact

High for autonomous settlement expansion, investment comparisons, power
planning, shortages/imports, and market development.