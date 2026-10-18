# Session Handoff — 2026-09-24
**Repo:** galaxyGame (M4 MacBook)
**Agents involved:** GitHub Copilot / local Qwen (implementation), Grok (AI Manager slice), Claude (coordination/review)
**Scope of session:** Inventory/manufacturing RSpec failure cluster, following up on a prior partial-session baseline of 178 failures (4733 examples)

---

## 1. Completed & Committed

### AI Manager slice (Grok) — committed `3be815fb`
- `TerraformingManager`: `initialize_depots` was undefined, called from `#initialize`. Fixed with a private no-op `initialize_depots`; `@orbital_depots` stays `{}` until externally populated (existing contract). Also fixed `default_params` missing `liquid_water_threshold: 1.0` (was causing `Float < nil` errors in phase/biosphere checks).
- `ResourceFlowSimulator#build_current_inventory`: spec used `inventory.add_item`, which failed `can_store?` silently. Fixed by using `items.create!` (matches pattern elsewhere in the file).
- Verified: `terraforming_manager_spec.rb` + `resource_flow_simulator_spec.rb` → 31 examples, 0 failures.
- **Files touched:** `terraforming_manager.rb`, `resource_flow_simulator_spec.rb` only.
- **Optional follow-up flagged, not done:** a real (non-no-op) `initialize_depots`, or nil-guards, if any production path calls O2/methane/H2 helpers without pre-populated `@orbital_depots`.

---

## 2. Completed, Needs ONE Corrective Commit (working tree currently uncommitted)

### Root cause found and fixed: `BaseUnit#storage_type` key mismatch — REAL FIX IS A REVERT

**Important:** an earlier fix *this same session* changed `storage_type` to read a nested `operational_data.dig('storage', 'type')` key, based on what test fixtures used. That was **wrong** — confirmed against all 11 real storage-unit JSON files in `data/json-data/operational_data/units/storage/`, 0 of which have a nested `storage.type` key. All 11 use a **top-level `"subcategory"`** key. This matches an older, already-established project fix that the session had accidentally reverted.

**Final state (correct):**
- `app/models/units/base_unit.rb` line ~240:
  ```ruby
  def storage_type
    operational_data['subcategory']
  end
  ```
- Capacity fields (`storage.capacity` / `storage.current_level`) were already correct in fixtures — matches the dominant real-data pattern (7/11 files use this shape; 2/11 use a differently-named `storage_system` shape with `capacity_kg`/`capacity_l`; 2/11 have no capacity field at all — not relevant to any current fixture).

**Fixtures corrected (5 occurrences, 4 files)** — each now has a top-level `"subcategory" => "general"` added, with the existing correct `storage.capacity`/`current_level` block preserved:
- `spec/models/inventory_spec.rb` (line 38)
- `spec/models/units/base_unit_spec.rb` (lines 135, 169, 187)
- `spec/services/manufacturing/production_service_spec.rb`
- `spec/services/manufacturing/component_production_service_spec.rb`

**Verified:** `inventory_spec.rb` and `base_unit_spec.rb` are green after this fix.

**Working tree state — uncommitted, needs ONE commit:**
```
 M app/models/units/base_unit.rb
 M spec/models/inventory_spec.rb
 M spec/models/units/base_unit_spec.rb
 M spec/services/manufacturing/component_production_service_spec.rb
 M spec/services/manufacturing/production_service_spec.rb
```
**Suggested commit message:** `Revert wrong storage_type fix + correct fixtures to match real subcategory schema`
Do not fold anything else into this commit. Do not push without review.

### Known follow-up, NOT touched (needs its own Synthesis Report per project rule on shared/global BaseUnit changes)
- `BaseUnit#store_resource` also checks `operational_data.dig('storage', 'type')` — same wrong-key bug as the one just fixed. Not currently exercised by any failing spec in this cluster (everything here routes through the general-storage path, not `store_resource`), so left alone tonight. File as its own task before touching.

---

## 3. New Issue Found — NOT Fixed, Needs Its Own Task

### Heisenbug in `production_service_spec.rb` (15 failures)

**Symptom:** All 15 examples in this file fail with `"Insufficient materials: binding_agent (need X, have 0.0)"` when the whole file is run together. Every example passes when run in isolation (e.g. `rspec spec/services/manufacturing/production_service_spec.rb:71` alone → 0 failures).

**Ruled out:** Simple ActiveRecord staleness. A DIAG module that wraps `validate_materials_available` and makes an *extra* live `get_available_amount` query before calling `super` makes all 15 failures disappear — but so does a **no-op** wrapper that does nothing but print one line and call `super`, with **no extra query at all**. This proves it's not about refreshing stale data — it's about the mere act of `prepend`-ing a module onto the class.

**Leading hypothesis for next session:** a duplicate/shadowed method definition somewhere in the ancestor chain. Prepending shifts Ruby's method resolution order, so `super` from the prepended module may land on a *different* definition of `validate_materials_available` than what gets dispatched to normally (e.g. a stray module included twice, or the file loaded via two different autoload paths).

**Cheap, read-only first step for next session (no test run needed):**
```ruby
Manufacturing::ProductionService.instance_method(:validate_materials_available).source_location
Manufacturing::ProductionService.ancestors.select { |m| m.instance_methods(false).include?(:validate_materials_available) }
```
If more than one ancestor defines the method, that's very likely the whole bug.

**Also not yet investigated:** `component_production_service_spec.rb`'s remaining 6 failures may be the same class of issue (same "insufficient materials" symptom shape) — check this hypothesis before assuming a fresh diagnosis is needed.

---

## 4. Deferred (not started this session)

- **Stage 2 — Shell printing Hash/Item boundary fix** (`shell_printing_service.rb`, `calculate_shell_materials`): `item.amount` called on a Hash from blueprint JSON `material_requirements` in some branches, an actual `Item` model in others, under one variable name. Needs a single defined conversion boundary with distinct variable names — no `respond_to?(:amount)` duck-typing. Not started.
- **Combined run** of all six original checkpoint files together (to check cross-file interference) — not run since the storage_type saga interrupted the original playbook sequence.
- **Downstream diagnostic reruns** (`unit_module_assembly_service_spec.rb`, `luna_operations_simulation_service_spec.rb`) — not run this session.
- **`item_spec.rb:293`** (regolith composition mismatch) — confirmed data/spec drift, intentionally left untouched per standing scope decision.

---

## 5. Design Notes Surfaced This Session (not implementation work, filed for later)

- **Regolith composition should be variable and tracked, not fixed.** Tracy's framing: a pile's composition should reflect *what was actually harvested* (world/region-dependent — Luna highlands vs. mare, Mars vs. Luna pipeline differences), tracked in item metadata (a `find_available_material`-style `composition` lookup on Item already exists as precedent). Real design questions still open: blend-on-merge vs. keep-batches-separate; whether TEU/PVE yields should actually compute from tracked composition (bigger change) vs. composition just riding along for now; where composition originates (geosphere-sampled at harvest vs. randomized placeholder).
- **Mars vs. Luna material pipeline differs structurally, not just numerically.** Confirmed pipeline: raw_regolith → TEU (hopper sorts fragments, oven drives off easy volatiles) → processed_regolith → PVE (breaks oxides, pulls O2 + remaining volatiles) → depleted_regolith. **On Mars, PVE is skipped entirely** — O2 comes directly from atmospheric CO2 processing instead, since PVE's oxide-breaking step is unnecessary there. This is early-ISRU framing; worth reviewing whether current code assumes a single universal pipeline.
- **Regolith is never actually scarce in-game** (it's the surface itself). The "insufficient raw regolith" unit test is legitimate as a guard-clause test using an artificially small pile — but it's testing a **local stock/logistics gap** (pile hasn't been harvested/allocated yet), not global resource depletion. Keep that distinction in any future design discussion referencing this test.

---

## 6. Immediate Next-Session Priorities (suggested order)

1. Commit the storage_type revert + fixture fixes (section 2) — clean, verified, ready.
2. Run the two read-only ancestor-chain checks (section 3) before any further test-run experimentation on the Heisenbug.
3. If the Heisenbug resolves cleanly, re-run the combined six-file checkpoint (section 4) to confirm no cross-file interference before moving to Stage 2 (shell printing).
4. File `store_resource`'s wrong-key bug (section 2) as its own scoped task with a Synthesis Report, per standing project rule on shared BaseUnit changes.
