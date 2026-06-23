---
status: active
priority: HIGH
type: architecture
system_domain: AI_MANAGER
mvp_alignment: AI_MANAGER_LUNA_SETTLEMENT
local_worker_safe: false
---

# TASK: Verify StructureCore and OrbitalStructure Settlement Association

**Status**: ACTIVE
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-05-27
**Last Updated**: 2026-05-27

---

## Local Worker Triage Report

- **Template Conformance**: PASS
- **Docker Wrapper Check**: PASS
- **MVP Alignment**: VALID — OrbitalStructure must associate with OrbitalSettlement for Luna L1 depot construction to work correctly
- **MVP Impact Note**: If BaseStructure still hardcodes BaseSettlement, orbital construction for Luna L1 depot is broken at the model level
- **Action Line**: READY FOR CLOUD HANDOFF

---

## Agent Assignment

**Assigned To**: GPT-4.1 0x
**Why This Agent**: Audit and targeted fix, well-defined scope
**Supervision Level**: Watched carefully

---

## Context

`settlement_core.rb` concern exists in `app/models/concerns/settlement/`. The April 2026 architecture task identified that `BaseStructure` had a hardcoded `belongs_to :settlement, class_name: 'Settlement::BaseSettlement'` which prevented `OrbitalStructure` from associating with `OrbitalSettlement`. This task verifies whether that fix was applied and completes it if not.

`OrbitalDepot` and `SpaceStation` models still exist in `app/models/settlement/` and may be legacy — this task determines their status.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## Problem Statement

**Current behavior unknown** — need to verify:
- Does `BaseStructure` still hardcode `Settlement::BaseSettlement`?
- Does `OrbitalStructure` correctly associate with `OrbitalSettlement`?
- Are `OrbitalDepot` and `SpaceStation` legacy or active?
- Does `SettlementCore` concern handle polymorphic settlement association?

**Expected behavior**: `OrbitalStructure` associates with `OrbitalSettlement`. `BaseStructure` uses concern-based or polymorphic settlement association. Luna L1 depot can be constructed and associated with the L1 orbital settlement.

---

## Files Involved

### Primary Files — audit these first, edit only if fix needed
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/models/structures/base_structure.rb` | Base structure model | `belongs_to :settlement` |
| `app/models/structures/orbital_structure.rb` | Orbital structure model | settlement association |
| `app/models/concerns/settlement/settlement_core.rb` | Settlement concern | association definition |
| `app/models/settlement/orbital_depot.rb` | Legacy or active? | class definition |
| `app/models/settlement/space_station.rb` | Legacy or active? | class definition |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/models/settlement/orbital_settlement.rb` | Target association class |
| `app/models/settlement/base_settlement.rb` | Base settlement class |

---

## Implementation Steps

### Step 1 — Audit current association definitions

```bash
grep -n "belongs_to.*settlement\|settlement_core\|SettlementCore" galaxy_game/app/models/structures/base_structure.rb galaxy_game/app/models/structures/orbital_structure.rb
```

```bash
cat galaxy_game/app/models/concerns/settlement/settlement_core.rb
```

```bash
grep -n "class\|belongs_to\|has_many" galaxy_game/app/models/settlement/orbital_depot.rb galaxy_game/app/models/settlement/space_station.rb
```

### Step 2 — Check OrbitalSettlement associations

```bash
grep -n "has_many.*structure\|has_many.*project\|has_many.*construction" galaxy_game/app/models/settlement/orbital_settlement.rb
```

### Step 3 — Synthesis Report

```
SYNTHESIS REPORT
BaseStructure settlement association: [paste line]
OrbitalStructure settlement association: [paste line]
SettlementCore concern contents: [paste]
OrbitalDepot status: [active model or legacy stub]
SpaceStation status: [active model or legacy stub]
OrbitalSettlement has_many structures: [YES/NO]
OrbitalSettlement has_many orbital_construction_projects: [YES/NO]

ISSUE FOUND: [describe any broken associations]
PROPOSED FIX: [exact code change if needed]
RISK: [any specs or services affected]
READY TO APPLY? — waiting for approval
```

### Step 4 — Apply fix if needed (after approval)

If `BaseStructure` still hardcodes `Settlement::BaseSettlement`:

```ruby
# before
belongs_to :settlement, class_name: 'Settlement::BaseSettlement'

# after — polymorphic to support both surface and orbital settlements
belongs_to :settlement, polymorphic: true
```

If `OrbitalStructure` needs explicit association:
```ruby
belongs_to :settlement, class_name: 'Settlement::OrbitalSettlement',
           foreign_key: :settlement_id
```

### Step 5 — Verify specs pass

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/structures/ 2>&1 | tail -20'
```

Expected: 0 failures.

---

## Acceptance Criteria
- [ ] Audit completed and synthesis report produced
- [ ] BaseStructure settlement association verified or fixed
- [ ] OrbitalStructure correctly associates with OrbitalSettlement
- [ ] OrbitalDepot/SpaceStation status determined — legacy or active documented
- [ ] Structure specs pass — 0 failures
- [ ] No regressions

---

## Stop Conditions — escalate immediately if:
- Changing `belongs_to :settlement` to polymorphic causes migration requirement — stop, report
- More than 5 spec files reference the settlement association on structures — map all before fixing
- OrbitalDepot or SpaceStation are actively used in services — do not deprecate, flag

---

## Commit Instructions
```bash
git add galaxy_game/app/models/structures/base_structure.rb
git add galaxy_game/app/models/structures/orbital_structure.rb
git commit -m "arch: verify and fix OrbitalStructure settlement association — supports OrbitalSettlement"
git push
```

---

## Dependencies
**Blocked by**: none
**Blocks**: `2026-05-27-HIGH-FEATURE-ORBITAL-CONSTRUCTION-LOGISTICS-SERVICE.md`
**Related**: `2026-05-27-CRITICAL-REFACTOR-EXTRACT-ORBITAL-RESUPPLY-CYCLE.md`

---

## Completion Report
**Completed by**:
**Completion date**:
**Final test result**:

### What was changed
### Issues discovered
### Follow-up tasks needed
### Lessons learned

---

## Handoff Summary
HANDOFF SUMMARY: Structure/settlement association verified | OrbitalStructure→OrbitalSettlement confirmed | Legacy model status documented | Structure specs clean
