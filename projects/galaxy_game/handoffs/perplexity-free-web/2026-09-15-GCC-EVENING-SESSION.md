# Evening Handoff — Orbital Settlement Documentation
**Date:** 2026-09-15  
**Repository:** `galaxyGame`  
**Status:** Documentation correction and evidence audit complete; no further writes recommended tonight.

## Executive Summary

Tonight’s work corrected the documentation model for orbital stations.

`Settlement::SpaceStation` is a retired legacy class and must not be cited as current implementation evidence. The active architecture is centered on `Settlement::OrbitalSettlement` plus associated `Structures::BaseStructure` records, including the specialized `Structures::OrbitalStructure` model.

An orbital settlement is modeled as a constellation of structures rather than a single fixed station object. The current settlement model directly supports multiple structures, structure-level storage/population aggregation, and creation of planned specialized structures by blueprint identifier.

Depot and shipyard references remain canonical design intent and factory/planning vocabulary until their active runtime models, identifiers, blueprint validation, and construction lifecycle are independently verified.

---

## Work Completed Tonight

| Item | Status | Result |
|---|---|---|
| Create governance documentation guide | Complete | Created reusable section-template and adoption guide |
| Add retired/deprecated source-evidence rule | Complete | Added to the guide’s evidence-discipline section |
| Correct stale Station taxonomy | Complete | Removed current-model treatment of retired `Settlement::SpaceStation` |
| Audit active orbital settlement model | Complete | Verified settlement-to-structure relationship, aggregation, and planned structure creation |
| Create Stations/Settlements domain | Deferred | Do not create it yet |
| Verify depot/shipyard runtime types | Deferred | Needs focused source/data/construction audit |
| Implement code changes | Not started | No implementation changes authorized |

---

## Files Intentionally Changed

Only these documentation files were intentionally modified tonight:

```text
docs/wiki_reorganization/governance/WIKI_SECTION_TEMPLATE_AND_ADOPTION_GUIDE.md
docs/wiki_reorganization/transportation/01-craft-taxonomy.md
```

Both files are currently untracked/new documentation artifacts.

No files were staged or committed.

---

## Repository Hygiene Warning

The repository contained unrelated pre-existing modifications and untracked work. Do not use broad staging commands such as:

```bash
git add .
git add -A
```

Use path-scoped Git commands when this work is eventually reviewed or committed.

Known pre-existing modified/untracked work included:

```text
M  docs/reference/asset-generation/ASSET_PROMPT_COMPILER_CONTRACT.md
M  docs/reference/asset-generation/VISUAL_CONTRACT.md
M  docs/wiki_reorganization/economy/02-currencies-and-accounts.md
M  galaxy_game/lib/tasks/lunar_precursor_mission_validation.rake
?? docs/wiki_reorganization/governance/
?? docs/wiki_reorganization/transportation/
... additional pre-existing untracked asset-generation and mission files
```

Important reporting precision:

> Editing an untracked file is still a filesystem change, even if `git status --short` remains `??` before and after. The relevant safeguard is that no tracked-file state, staging area, commit, or out-of-scope path changed.

---

## Canonical Model Established

```text
Settlement::OrbitalSettlement
├── includes SettlementCore
├── has_many :structures
│   ├── class_name: Structures::BaseStructure
│   └── foreign_key: settlement_id
│
├── has_many :orbital_construction_projects
│   └── foreign_key: station_id
│
├── aggregates across structures
│   ├── total_storage_capacity
│   └── population_capacity
│
├── derives location from a constituent structure
│   └── structures.first.celestial_location
│
└── add_specialized_structure!(blueprint_id)
    └── structures.create!(
          identifier: blueprint_id,
          shell_status: "planned"
        )

Structures::OrbitalStructure
├── inherits Structures::BaseStructure
└── belongs_to :settlement,
    class_name: Settlement::OrbitalSettlement,
    optional: true
```

### Conceptual interpretation

```text
OrbitalSettlement
├── depot-like orbital structure
├── shipyard-like orbital structure
├── habitat structure
├── docking/logistics structure
├── storage or processing structure
└── other associated BaseStructure subclasses
```

“Station” is a gameplay/documentation term for this composite orbital infrastructure. It is not a current alias for the retired monolithic `Settlement::SpaceStation` class.

---

## Verified Implementation Facts

| Fact | Evidence status | Notes |
|---|---|---|
| `Settlement::OrbitalSettlement` inherits from `ApplicationRecord` | Verified implementation | Uses `base_settlements` table |
| `OrbitalSettlement` includes `SettlementCore` | Verified implementation | Structure association comes from this concern |
| Orbital settlements have `has_many :structures` | Verified implementation | Targets `Structures::BaseStructure` via `settlement_id` |
| Structure cardinality is \(0..N\) | Verified implementation | No collection limit found in association |
| `has_many :structures` has no explicit `dependent:` option in inspected declaration | Verified source evidence | Full deletion behavior still needs lifecycle/schema review |
| `Structures::OrbitalStructure` belongs to `Settlement::OrbitalSettlement` | Verified implementation | Explicit association override |
| Settlement storage capacity aggregates across structures | Verified implementation | Uses `structures.sum(&:total_storage_capacity)` |
| Settlement population capacity aggregates across structures | Verified implementation | Uses `structures.sum(&:habitat_capacity)` |
| Settlement location is derived from the first structure’s celestial location | Verified implementation | No guaranteed ordering; may be nil with no structures |
| Orbital settlement is a constellation, not a one-to-one fixed object | Verified source evidence | Reflected in settlement location design |
| `add_specialized_structure!(blueprint_id)` creates a planned structure | Verified implementation | Uses `structures.create!` with `identifier` and `shell_status: "planned"` |
| `add_specialized_structure!` raises on persistence failure | Verified implementation | Uses `create!` |
| `add_specialized_structure!` performs type/blueprint validation itself | Not verified / apparently absent in method | Validation may occur elsewhere; do not assume |
| `Settlement::SpaceStation` is retired | Verified retired-source status | Retirement date: 2026-04-10 |
| Retired `space_station` factory creates `Settlement::OrbitalSettlement` | Verified factory evidence | Factory name is legacy/misleading; runtime call-site impact remains unverified |

---

## Retired Model Rule

The governance guide now includes a retired/deprecated-source rule.

Use the following rule in all future documentation and implementation review:

> Before citing a source file as current implementation evidence, inspect its file header and nearby deprecation, retirement, or compatibility annotations.
>
> An explicit `RETIRED`, `DEPRECATED`, `DO NOT USE`, or equivalent marker takes precedence over inferences drawn from inheritance, associations, methods, or comments. A retained class may exist only for history, migrations, compatibility, or reference.
>
> When a retired file names a replacement, independently verify the replacement before attributing the old class’s behavior, ownership, interface, or lifecycle to it.

### Retired source status

`Settlement::SpaceStation` contains an explicit retirement notice:

```ruby
# RETIRED 2026-04-10
# Use Settlement::OrbitalSettlement with Structures::OrbitalStructure instead.
# This file is kept for git history only. Do not use this class.
```

Do not cite the retired class as current evidence for station ownership, structure associations, docking, atmosphere, population, storage, lifecycle, economics, or AI behavior.

---

## Transportation Documentation State

The corrected file is:

```text
docs/wiki_reorganization/transportation/01-craft-taxonomy.md
```

The `### Station` section now:

- Marks `Settlement::SpaceStation` as retired historical source material.
- States that orbital settlements are settlement types, not craft.
- Describes an orbital settlement as a constellation of structures rather than a single fixed object.
- States that `Structures::OrbitalStructure` belongs to `Settlement::OrbitalSettlement`.
- Labels depot/shipyard composition as canonical design intent.
- Does not claim unverified association APIs, construction behavior, depot/shipyard runtime support, AI behavior, economy behavior, or service interfaces.

### Ownership boundary

Transportation should retain only the following boundary:

```text
Craft versus orbital infrastructure.
```

A future Stations/Orbital Settlements domain should own detailed documentation for:

- settlement composition;
- structures and specialized facilities;
- depot and shipyard behavior;
- storage and population aggregation;
- construction/lifecycle;
- settlement ownership and economy;
- docking and logistics services;
- AI/NPC expansion behavior;
- orbital construction projects.

Economy should retain ownership of economic policy, currency governance, accounting rules, taxation, and market-policy mechanics—not general station architecture.

---

## Depot and Shipyard Status

| Topic | Current classification | What is known | What is not known |
|---|---|---|---|
| Depot | Factory/planning concept plus canonical design intent | `planned_structures` factory data uses `orbital_depot` | No active concrete `Structures::Depot` class verified |
| Shipyard | Factory/planning concept plus canonical design intent | Factory references `l1_shipyard_bp` | No active concrete `Structures::Shipyard` class verified |
| `planned_structures` | Factory/test fixture data | Present in `:earth_luna_l1` factory trait | Not verified as a model field, runtime workflow, or construction queue |
| `add_specialized_structure!` | Active minimal creation method | Creates planned structure with blueprint identifier | Does not itself validate or classify depot/shipyard types |
| Blueprint identifiers | Unresolved | Passed into a planned structure’s `identifier` field | Validation, lookup, and operational activation paths not audited |
| Construction lifecycle | Unresolved | `shell_status: "planned"` suggests a pipeline | No state transitions/services/specs reviewed |
| `OrbitalConstructionProject` | Partially identified | Settlement association exists through `station_id` | Relationship to structure creation/completion not inspected |

Do not claim that depots or shipyards are active, operational, concrete model types until the remaining audit confirms it.

---

## Test Coverage Status

No direct spec/test assertions were verified during tonight’s audit.

Factories found:

```text
spec/factories/settlement/orbital_settlement.rb
spec/factories/structures/orbital_structure.rb
spec/factories/settlement/space_station.rb
```

Factory evidence demonstrates fixture construction and intended vocabulary only. It does not establish:

- runtime behavior;
- model validation;
- multiple-structure attachment;
- depot/shipyard creation;
- construction completion;
- service functionality;
- test coverage.

No verified test was found that attaches multiple structures to one `OrbitalSettlement`.

---

## Deferred Questions

These questions must remain classified as unresolved until a focused source/test audit is performed:

1. Are depot and shipyard concrete structure subclasses, blueprint identifiers, unit configurations, factory labels, or a mixture of these?
2. What validates `blueprint_id` in `add_specialized_structure!(blueprint_id)`?
3. Can arbitrary identifiers create invalid planned structures, or is validation performed by `BaseStructure`, a callback, or a blueprint lookup service?
4. Which field/state transition turns `shell_status: "planned"` into built, completed, or operational?
5. Does `OrbitalConstructionProject` create, complete, or otherwise govern structures?
6. Why does `orbital_construction_projects` use `station_id` as its foreign key?
7. Are structures destroyed, retained, nullified, or database-cascaded when a settlement is removed?
8. Do tests cover multiple structures under one settlement?
9. Does the legacy `:space_station` factory have active test call sites?
10. Should the legacy factory be retained, renamed, deprecated, or documented as compatibility-only?

---

## Recommended First Task Tomorrow

Run a single **read-only evidence audit**. Do not write documentation, modify source, rename factories, or create a Stations domain until the report is reviewed.

### Audit goal

Inspect:

- `Structures::BaseStructure`;
- all concrete structure subclasses;
- `Lookup::BlueprintLookupService`;
- validations, callbacks, and services that use a structure identifier or blueprint ID;
- `OrbitalConstructionProject`;
- structure/construction call sites;
- specs and factories that reference orbital settlements, orbital structures, multiple structures, `:space_station`, depots, shipyards, and `add_specialized_structure!`.

### Required conclusions

The audit must establish, with exact file paths and line numbers:

1. Whether depot/shipyard are concrete runtime types, valid blueprint identifiers, fixture-only labels, or unresolved.
2. What validates `blueprint_id`.
3. Whether a planned structure has an implemented lifecycle.
4. Whether construction projects create or complete structures.
5. Whether multiple structures under one orbital settlement have direct test coverage.
6. Whether the legacy `:space_station` factory is still used and why.

### Evidence discipline

- Do not treat comments as runtime evidence.
- Do not treat factory data or configuration as runtime behavior without a verified consumer.
- Do not infer capability from a class/module name.
- Do not convert canonical design intent into implementation claims.
- Treat explicit retirement/deprecation headers as controlling.
- Distinguish implementation, tests, fixtures, configuration/data, documentation, retired legacy, and unresolved evidence.

---

## Qwen Command for Tomorrow

```text
You are Qwen operating in the galaxyGame repository.

Task type: READ-ONLY STRUCTURE, BLUEPRINT, AND CONSTRUCTION EVIDENCE AUDIT.

Do not create, edit, move, rename, delete, stage, commit, stash, reset, clean,
rebase, checkout, generate files, or otherwise alter any file or Git state.
Return findings only in your response.

Purpose:
Resolve the remaining evidence gaps before a future canonical
Stations/Orbital Settlements documentation domain is proposed. This is not a
refactor, implementation, documentation-writing, or cleanup task.

Established facts:
- `Settlement::SpaceStation` is retired and must not be used as active
  implementation evidence.
- `Settlement::OrbitalSettlement` has many `Structures::BaseStructure`
  records through `SettlementCore`.
- `Structures::OrbitalStructure` belongs to `Settlement::OrbitalSettlement`.
- `OrbitalSettlement#add_specialized_structure!(blueprint_id)` creates a
  planned structure with `identifier: blueprint_id` and
  `shell_status: 'planned'`.
- Depot/shipyard occurrences in factory `planned_structures` data are not,
  by themselves, proof of runtime implementation.

Required investigation:

1. Base structure and subclass inventory
- Locate and read the complete `Structures::BaseStructure` class.
- Find every concrete subclass beneath the structures namespace.
- For each, report inheritance, table/discriminator usage if relevant,
  settlement relationship, identifier/type fields, lifecycle/state fields,
  and direct capabilities.
- Identify whether any concrete depot, shipyard, habitat, docking, storage,
  or construction structure subclasses exist.

2. Blueprint and identifier validation
- Locate `Lookup::BlueprintLookupService` and every validation/callback/service
  path related to a structure `identifier` or `blueprint_id`.
- Determine whether `add_specialized_structure!` can create a record for an
  arbitrary identifier, whether lookup is mandatory, and where invalid or
  unknown blueprint identifiers fail.
- Distinguish source implementation from data definitions/configuration.

3. Lifecycle and construction
- Search for `shell_status`, `planned`, `constructed`, `operational`,
  `complete`, and relevant state transitions.
- Locate `OrbitalConstructionProject` and inspect its complete model plus
  direct callbacks/services/call sites that create, update, or complete
  structures.
- Determine whether construction projects are linked to
  `add_specialized_structure!`, structures, or blueprint identifiers.

4. Depot and shipyard classification
- Search current source, specs, factories, blueprint/configuration/data,
  tasks, and current documentation for:
  `depot`, `shipyard`, `orbital_depot`, `l1_shipyard_bp`,
  `planned_structures`, and `space_station`.
- Classify each substantive result as runtime implementation, spec/test,
  factory fixture, blueprint/configuration data, documentation, or
  retired/legacy.
- State whether depot and shipyard are verified operational structure types,
  valid blueprint identifiers only, fixture/planning concepts only, or
  unresolved.

5. Test and factory call-site evidence
- Find all usages of `create(:space_station)`, `build(:space_station)`,
  `:space_station`, `:orbital_settlement`, `:orbital_structure`, and
  `add_specialized_structure!` outside the factory definitions.
- Separate factory definitions from actual spec/test assertions.
- Identify exact tests that prove multiple structures can be attached to a
  single orbital settlement; state clearly if none exist.

Evidence discipline:
- Cite exact paths and line numbers.
- Do not treat comments, factory data, configuration, or a blueprint name as
  runtime behavior unless a verified consumer establishes it.
- Do not infer capability from a class/module name.
- Treat explicit RETIRED/DEPRECATED/DO NOT USE annotations as controlling.
- Separate verified implementation, verified tests, factory fixtures,
  configuration/data, documentation, retired legacy, and unresolved claims.

Required return format:

## Scope and No-Change Confirmation
- Repository root:
- Initial Git status:
- Exact files/directories searched:
- Explicit confirmation that no file or Git state changed:

## Structure Inventory
| Structure class | Inheritance | Settlement association | Lifecycle fields | Direct evidence | Limits |
|---|---|---|---|---|---|

## Blueprint Validation
| Question | Answer | Evidence | Classification | Limits |
|---|---|---|---|---|

## Construction Lifecycle
| Claim | Evidence | Classification | What it proves | What remains unknown |
|---|---|---|---|---|

## Depot and Shipyard Classification
| Finding | Path/lines | Classification | What it establishes | What it does not establish |
|---|---|---|---|---|

## Test Coverage and Call Sites
| Call site/test | Path/lines | Classification | What it proves | Limits |
|---|---|---|---|---|

## Facts Safe for Future Canonical Documentation
- Directly supported facts only.

## Open Questions / Evidence Gaps
- Concise list only.

## Smallest Recommended Next Task
- One bounded proposal only; do not begin it.
```

---

## Stop Condition

After the next evidence audit:

- Stop unless it identifies a concrete current defect or a clearly stale active documentation claim.
- Do not create a Stations/Orbital Settlements domain yet.
- Do not rename or delete the legacy `:space_station` factory.
- Do not implement depot/shipyard model classes.
- Do not change construction behavior.
- Do not infer system behavior from factories, comments, identifiers, or model names alone.
- Review the resulting evidence report before authorizing any code or documentation changes.