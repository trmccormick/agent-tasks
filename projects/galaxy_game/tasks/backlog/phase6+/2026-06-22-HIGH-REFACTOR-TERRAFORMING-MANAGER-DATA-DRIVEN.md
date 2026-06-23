---
status: not-started
priority: HIGH
type: refactor
created: 2026-06-22
last-updated: 2026-06-22
phase: 6+
---

# TerraformingManager Refactor — Data-Driven World Configuration

## Context
`AIManager::TerraformingManager` currently uses hardcoded `@worlds` hash to define planetary terraforming parameters (atmosphere composition targets, biome selection, resource requirements). This makes it brittle and difficult to test. Parameters should be data-driven and loaded from JSON configuration.

**Current Code**:
- [terraforming_manager.rb](galaxy_game/app/services/ai_manager/terraforming_manager.rb) — lines with hardcoded @worlds hash

## Problem Statement
Hardcoded world definitions prevent:
- Easy configuration updates without code changes
- Dynamic world discovery and testing
- Separation of configuration from logic
- Reusability across different planetary contexts

## Scope
- Extract `@worlds` hash to JSON data file
- Create loader service to populate from JSON
- Maintain existing terraforming logic (refactor only configuration layer)
- Update tests to use configuration-driven data
- Ensure feature parity with original hardcoded behavior

## Target Files
- `app/services/ai_manager/terraforming_manager.rb` (refactor to load from config)
- `data/config/worlds.json` (NEW — extracted world definitions)
- `app/services/ai_manager/world_loader.rb` (NEW — loads and validates world config)
- `spec/services/ai_manager/terraforming_manager_spec.rb` (update tests)
- `spec/services/ai_manager/world_loader_spec.rb` (NEW)

## Acceptance Criteria
- [ ] All world definitions extracted to `worlds.json`
- [ ] `WorldLoader` service validates schema and loads config
- [ ] `TerraformingManager` uses loader instead of hardcoded hash
- [ ] Existing tests pass without modification to test expectations
- [ ] New world configurations can be added without code changes
- [ ] RSpec: Full coverage for loader validation and error cases

## Implementation Steps
1. Extract `@worlds` hash from terraforming_manager.rb to `data/config/worlds.json`
2. Implement `WorldLoader` service:
   - `load_worlds(path)` → validates and parses JSON
   - `get_world(name)` → returns world config
   - Error handling for missing/invalid configs
3. Modify `TerraformingManager` to inject loader dependency
4. Update all tests to use configuration
5. Document world config schema

## Dependencies
- Requires: Existing `TerraformingManager` logic unchanged
- No external dependencies

## Notes
- Keep logic in service, only extract configuration
- Schema should be versioned for future updates
- Loader should cache loaded config for performance
