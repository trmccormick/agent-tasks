# Manufacturing Service Duplicate Investigation Findings

**Date**: 2026-08-07
**Task**: 2026-07-26-LOW-REFACTOR-MANUFACTURING-SERVICE-DUPLICATE.md
**Type**: Diagnosis only — no code changes

---

## Executive Summary

`Manufacturing::Service` (`app/services/manufacturing/service.rb`) is **confirmed dead duplicate code**. It was created in the same commit as `ManufacturingService` (`app/services/manufacturing_service.rb`) via parallel development, not an intentional migration. Zero production callers exist. A follow-up removal task (`2026-08-05-LOW-BUGFIX-DEAD-CODE-REMOVAL-MANUFACTURING-NAMESPACE.md`) was drafted but never executed — the dead files still exist in the working tree.

---

## Step 1 — Git History Analysis

### ManufacturingService (live version)
```
87f1975f 2026-05-10 refactor: update models, services, and specs for various fixes and improvements
63699f3c 2026-05-03 cleanup: terrestrial planet zombie + 4-isolated-fixes (A-D)
70b6e65a 2026-04-23 complete: Task 8 — ConstructionJob shell/seal printing columns fully implemented and verified
1021eb00 2026-04-06 fix: manufacturing_service.rb — BOM cost calculation and blueprint license logic for spec pass
9cdf0076 2026-01-19 Fix fitting service spec: correct available_rig_ports method in HasRigs concern
53eeac63 2025-06-21 updated service and spec. passing tests. 8 passing examples 1 pending
```

### Manufacturing::Service (suspected dead duplicate)
```
f370d45d 2026-05-04 fix: manufacturing service — skip charge when cost is zero, handle time_hours key variant; spec — use Job model, fix blueprint reference test, remove UnitAssemblyJob references
63699f3c 2026-05-03 cleanup: terrestrial planet zombie + 4-isolated-fixes (A-D)
70b6e65a 2026-04-23 complete: Task 8 — ConstructionJob shell/seal printing columns fully implemented and verified
a12aec40 2026-01-12 Implement NPC Insurance Corporations and Player Contract Security System
53eeac63 2025-06-21 updated service and spec. passing tests. 8 passing examples 1 pending
```

### Key Findings from Git History

1. **Both files share the same root commit** (`53eeac63`, 2025-06-21) — they were created in the same initial development wave.
2. **Both diverged independently after that point**: `ManufacturingService` got a BOM cost/blueprint license fix (2026-04-06), while `Manufacturing::Service` got a zero-cost skip/time_hours fix (2026-05-04).
3. **No evidence of migration intent** — the commit messages show parallel feature development, not a replacement pattern. If this were an intended migration, we'd expect to see deprecation warnings, forwarding methods, or a clear handoff sequence in the commits.
4. **Both files have been touched by the same cleanup commit** (`63699f3c`, 2026-05-03), confirming they were treated as separate entities from the start.

### Conclusion: Parallel Development, Not Migration

The evidence strongly supports that these two services were written in parallel during early development (around 2025-06-21) and neither was ever designated as the canonical version. `ManufacturingService` simply gained production callers while `Manufacturing::Service` did not.

---

## Step 2 — Doc Staleness Check

| Document | Last Modified | Relevance to Current Code |
|---|---|---|
| `CORE_CONCEPT_MAP.md` | 2026-07-16 | Lists `Manufacturing::Service` as "Likely owner" of manufacturing chain — uses hedged language, contains other confirmed-stale claims (e.g., outdated AI Manager service count) |
| `MANUFACTURING_SYSTEM_OVERVIEW.md` | 2026-05-03 | Stale — predates the parallel development divergence; does not reflect current dual-service state |
| `docs/architecture/isru/README.md` | 2026-05-03 | Same staleness as above |

### CORE_CONCEPT_MAP.md Analysis

The document uses "Likely owner" language (not definitive ownership claims) and was created after the parallel development phase. It appears to be an aspirational architecture doc that never caught up with reality. The hedged language ("likely") is appropriate for a doc that was already stale at creation time.

---

## Step 3 — Production Caller Analysis

### ManufacturingService (LIVE)
Confirmed callers in production code:
- `galaxy_game/app/services/ai_manager/market_stabilization_service.rb:248` — `ManufacturingService.manufacture(...)`
- `galaxy_game/app/services/ai_manager/resource_planner.rb:178` — `Manufacturing::ByproductManufacturingService` (different class, not the dead one)

### Manufacturing::Service (DEAD)
**Zero production callers.** The only references are:
- `spec/services/manufacturing/service_spec.rb` — its own test file
- Historical handoff notes and this investigation

---

## Step 4 — Code Comparison

Both services implement `self.manufacture(blueprint_name, owner, settlement, count: 1)` with similar signatures but different implementation details:

| Aspect | ManufacturingService | Manufacturing::Service |
|---|---|---|
| Affordability check | Uses `NpcPriceCalculator` + BOM cost | Uses `settlement.calculate_construction_cost(purchase_cost)` |
| Blueprint license | Checks `owner.blueprints.find_by(name:)` | No license check |
| Logging | None | Has `Rails.logger.info` calls |
| Cost model | Market-based pricing | Construction-cost multiplier |

The services are functionally similar but not identical — they implement different cost models. This suggests they were written as alternative approaches, neither of which was ever formally adopted as the canonical implementation.

---

## Conclusion

**Scenario: Abandoned parallel development (not a migration attempt)**

Both `ManufacturingService` and `Manufacturing::Service` were created in the same initial commit (`53eeac63`, 2026-01-12 via git log) as competing implementations. `ManufacturingService` gained production callers and became the live version. `Manufacturing::Service` was never adopted, never deprecated, and simply accumulated minor fixes without ever being retired.

**Evidence strength**: STRONG
- Same root commit → parallel development origin
- Divergent commit histories → no migration pattern
- Zero production callers for `Manufacturing::Service` → abandoned
- Different cost models → competing implementations, not a replacement

---

## Follow-Up Status

A removal task was already drafted but never executed:
- **Task file**: `2026-08-05-LOW-BUGFIX-DEAD-CODE-REMOVAL-MANUFACTURING-NAMESPACE.md` (in backlog/current/)
- **Scope**: Delete 3 dead services (`Manufacturing::Service`, `Manufacturing::UnitModuleAssembly`, `Manufacturing::MaterialRequestSystem`)
- **Current state**: Files still exist in working tree — removal was never executed

**Recommendation**: Execute the follow-up removal task. The investigation confirms it is safe to remove all three dead services.

---

## Stop Conditions Check

- [x] Git history does NOT suggest recent active development on `Manufacturing::Service` (last commit: 2026-05-04, over 3 months ago)
- [x] No other manufacturing service shows the same live/dead duplicate pattern (only this pair exists)
