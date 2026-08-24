
---

# TASK: Audit how StarSim (importer + procedural generation) produces hydrosphere composition data

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: architecture
**Created**: 2026-08-23
**Last Updated**: 2026-08-23

---

## Context

A related task (`2026-08-23-MEDIUM-BUG-FIX-HYDROSPHERE-COMPOSITION-SCHEMA-CONSISTENCY.md`) checks whether anything currently *reads* `hydrosphere_attributes.composition` and whether existing Sol data (Titan vs Earth) uses a consistent shape. This task looks at the other side: how does that data get *produced* in the first place — both for the hand-authored Sol bodies (via whatever importer/loader ingests `sol-complete.json` into the running data-driven celestial-body system) and for procedurally-generated bodies (via StarSim's generation logic, per the same data-driven-generation principle established in the magnetosphere_strength refactor — no hardcoded per-body logic, values calculated and stored in JSON).

The concern: if Sol's hand-authored data already has two inconsistent `composition` shapes (per the sibling task), it's worth confirming whether that's a hand-authoring slip only, or whether the generation/import pipeline itself has no single canonical shape it enforces — meaning a newly procedurally-generated body could come out with yet another inconsistent shape, or with no `composition` data populated at all.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- Data-driven generation principle established for magnetosphere_strength (see prior magnetosphere refactor task) — same "no hardcoded per-body values in Ruby, calculate/store in JSON" standard likely applies here

> If a doc doesn't exist for hydrosphere generation conventions, do not create one during this task. Flag the gap in your completion report instead.

---

## Problem Statement

**Unknown / to be determined by this task**:
1. **Importer side**: what loads `sol-complete.json`'s `hydrosphere_attributes` into the actual running system (DB records, in-memory models, or is the JSON read live)? Does that importer validate/normalize `composition` into one shape, or pass whatever shape is in the file straight through unchanged?
2. **Generation side**: does StarSim's procedural body-generation logic populate `hydrosphere_attributes.composition` for newly generated bodies at all? If yes, what shape does it emit, and is that shape consistent with either of the two shapes found in Sol data? If no, that's a gap — procedurally generated bodies may have no hydrosphere composition data at all, same category as the magnetosphere_strength gap found previously (procedural bodies getting a neutral/stub value with no real calculation).
3. Is there a single canonical `composition` schema intended anywhere in the codebase (a serializer, a schema validator, a comment) that both sides are supposed to converge on, or has this never been decided?

**Expected behavior**: Both the importer and the procedural generator should read/write `hydrosphere_attributes.composition` in one agreed schema, and procedurally-generated bodies should actually receive real (not stubbed) composition data consistent with their generated hydrosphere makeup (e.g. a generated Titan-analog should plausibly get CH4/C2H6-dominant composition, not a copy-pasted water-world default).

---

## Files Involved

### Primary Files — likely relevant, confirm before editing
| File | Purpose | Key Method/Section |
|---|---|---|
| `[FILL IN]` | Importer/loader for sol-complete.json into running system | `[FILL IN]` |
| `[FILL IN]` | StarSim procedural body generation (likely near SystemBuilderService / AtmosphereGeneratorService per magnetosphere refactor) | `[FILL IN]` — check for a hydrosphere-equivalent generator |
| `data/json-data/.../sol-complete.json` | Source data for hand-authored bodies | `hydrosphere_attributes` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| Magnetosphere refactor code (`calculate_magnetosphere_strength`, `SystemBuilderService`, `AtmosphereGeneratorService`) | Established pattern for how data-driven generation is supposed to work — hydrosphere generation likely should follow the same shape |

### Migration (if needed)
- [x] Unknown — this is a research task; note in completion report if a migration/schema decision turns out to be needed

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)
[Standard — see dispatch interface above]

### Step 1 — Trace the importer path
Find whatever loads `sol-complete.json` into the running system. Confirm whether `hydrosphere_attributes.composition` is read/normalized/validated at import time, or passed through as-is.

### Step 2 — Trace the procedural generation path
Find StarSim's procedural celestial-body generator (likely alongside `generate_procedural_terrestrial()` referenced in the magnetosphere work). Confirm whether it populates hydrosphere composition at all, what shape, and whether the values are genuinely calculated or a stub/default (same pattern as the magnetosphere `procedural_baseline = 0.5` stub gap found previously — check for the equivalent here).

### Step 3 — Cross-reference against sibling task's findings
Once the sibling schema-consistency task reports its findings, confirm whether the importer/generator sides explain *why* Titan and Earth ended up with different shapes (e.g. hand-authored at different times, generator changed shape between them) or whether it's unexplained.

### Step 4 — Synthesis Report (before any fix, before committing anything)

SYNTHESIS REPORT
Importer behavior: [normalizes / passthrough / doesn't exist as a separate step]
Generator behavior: [populates real data / stub-only / doesn't populate field at all]
Canonical schema found: [yes, cite location / no, undecided]

ROOT CAUSE (why the two sides may disagree, if they do)
[one paragraph]

PROPOSED FIX (only if needed)
[e.g. add real hydrosphere-composition calculation to generator, add import-time normalization, or "no fix needed — both sides already consistent"]

RISK
[any other code/data affected]

READY TO APPLY? — waiting for approval


---

## Acceptance Criteria
- [ ] Importer-side handling of `hydrosphere_attributes.composition` is confirmed with file:line reference
- [ ] Generator-side handling is confirmed with file:line reference, including whether it's a real calculation or a stub
- [ ] Report states clearly whether a canonical schema exists anywhere, or needs to be decided
- [ ] No fix applied without separate approval

---

## Stop Conditions — escalate to user immediately if:
- StarSim's procedural generator doesn't populate hydrosphere composition at all (same class of gap as the magnetosphere stub — significant enough to need a design decision, not a quick fix)
- Fixing this would require touching the same shared generation service the magnetosphere refactor already modified (risk of conflicting with that work)
- The importer and generator use fundamentally different data models (e.g. one is JSON-only, the other expects DB-backed records) — that's an architecture question, not a data-consistency one

---

## Dependencies
**Blocked by**: none, but should run alongside/after `2026-08-23-MEDIUM-BUG-FIX-HYDROSPHERE-COMPOSITION-SCHEMA-CONSISTENCY.md` for cross-reference
**Blocks**: none yet — feeds the same not-yet-filed EscalationService acquisition-routing design task
**Related tasks**: `2026-08-23-MEDIUM-BUG-FIX-HYDROSPHERE-COMPOSITION-SCHEMA-CONSISTENCY.md`; magnetosphere_strength data-driven generation refactor (established the pattern/precedent this task checks against)

---

## Completion Report
*Filled in by the implementing agent after completion*

## Handoff Summary
*Filled in at end of session*