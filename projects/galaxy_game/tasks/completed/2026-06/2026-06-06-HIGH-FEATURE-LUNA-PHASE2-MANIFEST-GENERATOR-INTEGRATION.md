---
id: 2026-06-06-HIGH-FEATURE-LUNA-PHASE2-MANIFEST-GENERATOR-INTEGRATION
title: "Luna Phase 2 — ManifestGenerator Integration into AI Manager"
status: backlog
priority: HIGH
type: FEATURE
created: 2026-06-06
agent: Qwen3.5-27B
---

# Luna Phase 2 — ManifestGenerator Integration into AI Manager

## Objective

Wire `Logistics::ManifestGenerator.create_manifest` into the AI Manager decision loop.
Phase 1 wired `Settlements::CostAnalyzer` and added a log-only `execute_cost_reduction`
method in `StrategySelector`. Phase 2 replaces the log-only stub with a real manifest
creation call when cost reduction is recommended.

## Gate

Phase 1 complete ✅ (commit cd4d6800)

---

## Pre-Work — Verify Before Writing Any Code

**STOP. Do not write any code until all three verifications are complete and reported.**

### Verification 1 — CostAnalyzer API mismatch

`ManifestGenerator#create_manifest` calls:
```ruby
Settlements::CostAnalyzer.current_import_price(resource_name, destination_settlement)
```

But Phase 1 confirmed the public API is:
```ruby
Settlements::CostAnalyzer.compare_costs(resource_name, settlement)
```

There is no `current_import_price` method documented. Before proceeding:
- Read `app/services/settlements/cost_analyzer.rb` in full
- Confirm whether `current_import_price` exists as a public or private method
- If it does not exist, flag immediately — ManifestGenerator has a dependency on a
  non-existent method. This is a stop condition.

### Verification 2 — ServiceCoordinator

Check whether `Logistics::ServiceCoordinator` already calls `ManifestGenerator`
anywhere:
- Read `app/services/logistics/service_coordinator.rb` in full
- Report any existing calls to `ManifestGenerator.create_manifest`
- If it already calls it, Phase 2 wiring must not duplicate that path

### Verification 3 — Luna settlement context

Phase 2 needs to pass `source_settlement` and `destination_settlement` to
`create_manifest`. For Luna:
- `destination_settlement` = the Luna settlement (already available in
  `execute_cost_reduction` via the `settlement` argument)
- `source_settlement` = Earth supply settlement or AI Manager's home settlement

Confirm: does the AI Manager have access to an Earth/source settlement? Check
`app/services/ai_manager/manager.rb` for how settlements are initialized and
whether a source settlement is accessible.

---

## Target Files

- `app/services/ai_manager/strategy_selector.rb` — replace log-only stub
- `app/services/logistics/manifest_generator.rb` — read only, do not edit
- `app/services/settlements/cost_analyzer.rb` — read only, verify API
- `app/services/logistics/service_coordinator.rb` — read only, verify no duplicate
- `app/services/ai_manager/manager.rb` — read only, verify settlement access
- `spec/services/ai_manager/strategy_selector_spec.rb` — add Phase 2 specs

---

## Scope

### What to implement

Replace the log-only `execute_cost_reduction` in `StrategySelector` with a real
manifest creation call:

```ruby
def execute_cost_reduction(action, settlement)
  recommendations = action[:resources] || []
  return true if recommendations.empty?

  source = find_source_settlement  # TBD from verification 3
  items = recommendations.map do |resource|
    { resource: resource, quantity: default_quantity_for(resource) }
  end

  begin
    manifest = Logistics::ManifestGenerator.create_manifest(source, settlement, items)
    Rails.logger.info "[StrategySelector] Manifest created: #{manifest.manifest_id}"
    manifest
  rescue Logistics::ManifestGenerator::ManifestError => e
    Rails.logger.warn "[StrategySelector] Manifest creation failed: #{e.message}"
    false
  end
end
```

- `default_quantity_for(resource)` — private method returning a safe default
  quantity per resource type. Use 10 as the default unless blueprint system
  provides better data. This is a Phase 2 stub — Phase 3 will wire real quantities
  from ShortageDetector.
- `find_source_settlement` — private method to locate the Earth/source settlement.
  Implementation depends on verification 3 findings.

### What NOT to implement

- Do not call `ShortageDetector` — that is Phase 3
- Do not modify `ManifestGenerator` — read only
- Do not modify `ServiceCoordinator` — read only
- Do not add new action types or strategic focus areas — Phase 1 handles those
- Do not change `EARTH_LUNA_TRANSIT_DAYS` usage — that belongs in Phase 3
  contract creation

---

## Stop Conditions — Escalate Immediately

- `current_import_price` does not exist on `CostAnalyzer` — ManifestGenerator
  has a broken dependency. Report and stop. Do not work around it silently.
- `ServiceCoordinator` already calls `ManifestGenerator.create_manifest` in a
  way that would conflict with Phase 2 wiring
- Source settlement is not accessible from StrategySelector context
- Any existing spec failures not introduced by this task

---

## Testing

Run after implementation:
```
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/ 2>&1 | tail -30'
```

Add focused specs to `strategy_selector_spec.rb` covering:
- `execute_cost_reduction` calls `ManifestGenerator.create_manifest` with correct
  arguments when recommendations are present
- `execute_cost_reduction` returns false and logs warning when
  `ManifestGenerator::ManifestError` is raised
- `execute_cost_reduction` returns true without calling ManifestGenerator when
  recommendations list is empty

Stub `ManifestGenerator.create_manifest` in unit specs — do not hit the database
for manifest creation in StrategySelector unit tests.

---

## Synthesis Report Format

```
SYNTHESIS REPORT
Task: Luna Phase 2 — ManifestGenerator Integration

VERIFICATION 1 — CostAnalyzer API
current_import_price exists: [YES/NO]
If NO: [stop condition details]
If YES: [method signature and return value]

VERIFICATION 2 — ServiceCoordinator
ManifestGenerator already called: [YES/NO]
If YES: [where and how — conflict risk assessment]

VERIFICATION 3 — Source Settlement
Earth/source settlement accessible from StrategySelector: [YES/NO]
Access path: [how it can be reached]

PROPOSED find_source_settlement implementation:
[code]

PROPOSED default_quantity_for implementation:
[code]

RISK ASSESSMENT
[any concerns about the proposed implementation]

READY TO APPLY? — waiting for approval
```

---

## Completion Report (fill in when done)

- Completed by:
- Completion date:
- Commit:
- Spec result:
- Files modified:
- CostAnalyzer `current_import_price` finding:
- Open questions for Phase 3:
