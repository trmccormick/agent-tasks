---
status: backlog
priority: LOW
type: audit
system_domain: BACKLOG_HYGIENE | DOCUMENTATION
phase_alignment: phase6+ (or later — explicitly after Mars/Venus/Titan/Ceres operations are running and cyclers/logistics are functional)
created: 2026-06-18
assigned_to: Claude (web) — review/dispatch, or any agent with read access once relevant phase opens
---

# TASK: Backlog Hygiene Audit — Stale/Corrupted/Unreconciled Task Files

**Status**: BACKLOG
**Priority**: LOW
**Type**: audit
**Created**: 2026-06-18
**Phase Note**: Do NOT dispatch this until Mars, Venus, Titan, and Ceres operations are live and cyclers/L1 logistics are functional. DigitalTwinService and other terraforming-pattern-learning tech are downstream of operational multi-body presence, not prerequisites to it. This task documents findings for that later point — it is not itself time-sensitive.

---

## Context

During a 2026-06-18 review session (water escalation task staleness review, deposit/spawning reconciliation), several recurring backlog health problems surfaced that aren't urgent individually but indicate a broader pattern worth a dedicated cleanup pass once the project reaches the phase where terraforming/pattern-learning infrastructure becomes relevant.

This is not a request to fix any of these now — it's a catalog of what to check when that time comes.

---

## Findings to Investigate

### 1. File corruption pattern
`2026-02-11-HIGH-BUGFIX-ESCALATION-WATER-ESCALATION-ISRU-CHAIN.md` (now archived to `deprecated/`) was found triplicated — identical content pasted three times with mid-word splice points, misread initially as three genuine design revisions. 
**Action when revisited**: Scan other pre-June backlog files for the same signature (duplicate `# TASK:` headers, abrupt mid-sentence cutoffs, duplicate Completion Report sections) before trusting their content at face value.

### 2. Lineage drift / stale belief propagation
A belief that PORO classes (`IsruTrackA`, `IsruTrackB`, `RawGasBuffer`, `TankFarmSystem`, `LavaTubeAtmosphericHarvester`) existed in the codebase propagated from the corrupted water escalation file into a separate task (`2026-05-18-HIGH-FEATURE-ISRU-TRACK-A-RSPEC-SUITE.md`) that assumed these classes existed and asked for test coverage of them. None were ever implemented.
**Action when revisited**: Treat any task file's claims about existing code as unverified until grep-confirmed — this has now happened at least twice in this backlog's history.

### 3. Unreconciled parallel tracks for Luna resource acquisition
Two separate task lineages addressed "how does Luna get materials" without ever cross-referencing each other:
- April 2026 (`implement_luna_base_buildup_test_case.md`) → May 29 12-question analysis → shipped as Phase 1-5 (CostAnalyzer/ManifestGenerator/ShortageDetector/etc.) — entirely market/logistics, assumes resources already exist.
- May 18, 2026 batch (`DepositSpawner`, `Deposit::PlausibilityEngine`, `ResourceDeposit`) — deposit/mining mechanics, determines whether resources exist and are discoverable at all.

A research task is in progress as of 2026-06-18 (Ryzen 7 session) to determine whether these were ever wired together for Luna specifically. **Action when revisited**: confirm that research concluded and that its findings were acted on; if not, this needs resolution before any later body (Mars/Venus/Titan/Ceres) repeats the same disconnect at larger scale.

### 4. `ResourceAcquisitionService` — orphaned config file
Confirmed (2026-06-18 research finding): `is_local_resource?` uses a hard-coded static array instead of reading `resource_acquisition_logic_v1.json`, which exists in the repo but is never referenced by any service or rake task.
**Action when revisited**: Decide whether to wire the service to the JSON config (restoring intended data-driven design) or delete the orphaned file if the static-array approach is now intentional.

### 5. DigitalTwinService — confirmed real gap, correctly deferred
`2026-03-30-HIGH-FEATURE-DIGITAL-TWIN-SCHEMA.md` is well-specified and was never implemented (only stubbed admin views exist). It's the intended bridge between TerraSim (confirmed mature) and the AI Manager's pattern-learning library (`venus-industrial-cooling`, `mars-terraform-venus-co2`, etc. — named in `SIMEARTH_ADMIN_VISION.md`, May 2026). 
**Action when revisited**: This is likely the actual next step once this phase opens — not an audit item so much as a confirmed-ready task. Re-verify it's still accurate (no DigitalTwinService implementation has appeared in the interim) and re-check the test-suite-under-10-failures blocker before dispatching.

### 6. Reference doc existence unverified
Several backlog files point to architecture docs that haven't been confirmed to exist: `PHASE_4_DATABASE_SCHEMA.md`, `PHASE_4_PREPARATION_PLAN.md`, `docs/architecture/simulation/SIMULATION_SANDBOX.md`, `docs/architecture/simulation/DIGITAL_TWIN_SANDBOX.md`.
**Action when revisited**: Confirm these exist and are current before dispatching any task that depends on them (including #5 above).

---

## Why This Is Sequenced Here, Not Earlier

Cyclers, L1 shipyard, and orbital logistics (phase6+/7+) must be functional first — there's no meaningful terraforming pattern to learn from or project (DigitalTwinService's whole purpose) until Mars/Venus/Titan/Ceres have real operations generating real data. Running this audit earlier risks "fixing" backlog organization for systems that don't exist yet and may change shape before they're built.

## Acceptance Criteria
- [ ] Confirm which of the above 6 findings are still accurate (codebase may have changed)
- [ ] For #1: scan remaining pre-June 2026 backlog files for corruption signature
- [ ] For #3: confirm Ryzen 7's 2026-06-18 deposit/spawning research was acted on
- [ ] For #4: decide and implement either wiring or deletion of the orphaned JSON config
- [ ] For #5: re-verify readiness, then dispatch as its own implementation task (not part of this audit)
- [ ] For #6: confirm reference doc existence, flag any that are missing

## Dependencies
**Blocked by**: Mars/Venus/Titan/Ceres operational status, cycler/logistics functionality
**Blocks**: DigitalTwinService dispatch (item #5 should not proceed until this audit at least covers #5 and #6)
**Related tasks**: 2026-03-30-HIGH-FEATURE-DIGITAL-TWIN-SCHEMA.md