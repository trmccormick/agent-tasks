---
status: backlog
priority: HIGH
type: feature
system_domain: AI_MANAGER
mvp_alignment: CONNECTIVITY_GRAPH
local_worker_safe: true
---

# TASK: Wormhole Network — Connectivity Status on SolarSystem
**Status**: BACKLOG
**Priority**: HIGH
**Type**: feature
**Created**: 2026-07-15
**Last Updated**: 2026-07-15

---

## Context
The Mission Planner needs to know whether a solar system is connected to Sol (Earth imports available) or orphaned. This is derived from the wormhole graph topology — not a manually-set field. Your 1995 Pascal BFS (`PATHS.PAS`) is the ancestor of `WormholeNavigator`, which already implements BFS across wormholes. We need to wire this into a persistent `connectivity_status` on SolarSystem.

**Architecture docs** (read before starting):
- `docs/architecture/ai_manager/AI_MANAGER_WAYFINDING.md` — BFS algorithm, WormholeNavigator
- `docs/architecture/services/ai_manager/planner.md` — three-tier sourcing hierarchy, orphaned system behavior
- `docs/legacy/PATHS.PAS` — original 1995 Pascal BFS (algorithmic foundation)

> Do not create any documentation during this task.
> Flag any gaps in your completion report.

---

## Problem Statement
`SolarSystem` has no `connectivity_status` field. The wormhole graph exists (`Wormhole` model connects two SolarSystems) but nothing computes or persists connectivity. `WormholeNavigator` does BFS but isn't called for status computation.

**Current behavior**: No connectivity awareness on SolarSystem; Mission Planner cannot gate Tier 3 sourcing.
**Expected behavior**: `SolarSystem#compute_connectivity_status` runs BFS from system to Sol via traversable/stable wormholes, persists result as enum.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/models/solar_system.rb` | Add enum + scopes + compute method | new enum + `compute_connectivity_status` |
| `db/migrate/[timestamp]_add_connectivity_status_to_solar_systems.rb` | Migration | new file |
| `app/services/wormhole_navigator.rb` | May need minor update for connectivity query | `find_shortest_path` already exists |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/models/wormhole.rb` | Wormhole model: `stability` enum, `wormhole_type` enum, `safe_for_travel?` |
| `app/services/wormhole_navigator.rb` | BFS algorithm — may reuse for connectivity computation |
| `spec/factories/solar_systems.rb` | Check factory needs updating |
| `spec/models/solar_system_spec.rb` | Add connectivity tests |

### Migration
- [x] Migration needed: add `connectivity_status` string column to `solar_systems` table with default `'connected'`, null: false

---

## Implementation Steps

> Follow these steps exactly in order.

### Step 1 — Generate migration

Run the Rails generator inside the container:
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && bundle exec rails generate migration AddConnectivityStatusToSolarSystems connectivity_status:string'
```

Verify the migration file was generated in `db/migrate/`. Note the timestamp prefix.

### Step 2 — Review and update migration

Open the generated migration file: `db/migrate/[timestamp]_add_connectivity_status_to_solar_systems.rb`

Update it to:
```ruby
class AddConnectivityStatusToSolarSystems < ActiveRecord::Migration[7.0]
  def change
    add_column :solar_systems, :connectivity_status, :string,
      default: 'connected', null: false
  end
end
```

Ensure:
- ✅ `default: 'connected'` is present
- ✅ `null: false` is present
- ✅ String column type (not integer)

### Step 3 — Run migration on dev database

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && bundle exec rails db:migrate'
```

Verify migration completes without error.

### Step 4 — Run migration on test database

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rails db:migrate'
```

Verify migration completes without error.

### Step 5 — Verify schema updated

Open `db/schema.rb` and confirm `solar_systems` table now has:
```ruby
t.string "connectivity_status", default: "connected", null: false
```

### Step 6 — Add enum + compute method to SolarSystem model

In `app/models/solar_system.rb`, add after the existing validations (after `validates :identifier, presence: true, uniqueness: true`):

```ruby
# Connectivity status derived from wormhole graph topology
enum connectivity_status: {
  connected: 'connected',
  distressed: 'distressed',
  orphaned: 'orphaned'
}, _prefix: :connectivity

scope :orphaned_systems, -> { where(connectivity_status: 'orphaned') }
scope :connected_systems, -> { where(connectivity_status: 'connected') }
scope :distressed_systems, -> { where(connectivity_status: 'distressed') }

# Compute connectivity by BFS from this system to Sol via traversable/stable wormholes
def compute_connectivity_status
  sol = SolarSystem.find_by(identifier: 'SOL-01')
  return 'orphaned' unless sol
  
  # Use WormholeNavigator to find shortest path
  path = WormholeNavigator.find_shortest_path(id, sol.id)
  
  if path.nil?
    update!(connectivity_status: 'orphaned')
    return 'orphaned'
  end
  
  # Check if any wormhole along the path is distressed (unstable or approaching threshold)
  path.each_cons(2) do |from_id, to_id|
    wh = Wormhole.joins(:endpoints)
      .where(solar_system_a_id: [from_id, to_id])
      .where(solar_system_b_id: [from_id, to_id])
      .find_by(stability: 'unstable')
    return 'distressed' if wh
  end
  
  update!(connectivity_status: 'connected')
  'connected'
end

# Recompute and persist status for all solar systems (admin/periodic task)
def self.recompute_all_connectivity
  all.each(&:compute_connectivity_status)
end
```

Use string-based enum values to match the string column.

### Step 7 — Integrate with WormholeManager

In `app/services/ai_manager/wormhole_manager.rb`, after `execute_shift_discharge` marks a wormhole as orphaned, also update the affected SolarSystem:

After line 30 (`wormhole[:status] = 'orphaned'`), add:
```ruby
# Update SolarSystem connectivity status when wormhole shifts
affected_systems.each(&:compute_connectivity_status)
```

You'll need to determine which SolarSystems are affected by the shift. Check the wormhole model for `solar_system_a` and `solar_system_b` associations.

### Step 8 — Check factory

Inspect `spec/factories/solar_systems.rb`. Confirm:
- Factory either omits `connectivity_status` (DB default will handle it), OR
- Factory explicitly sets `connectivity_status { 'connected' }`

No change required if defaults are working correctly.

### Step 9 — Add model spec

In `spec/models/solar_system_spec.rb`, add tests for:
```ruby
describe 'connectivity_status' do
  let(:solar_system) { create(:solar_system) }
  
  it 'defaults to connected' do
    expect(solar_system.connectivity_status).to eq('connected')
  end
  
  describe '#compute_connectivity_status' do
    it 'returns orphaned when no path to Sol exists' do
      # Create system with no wormhole connections
      isolated = create(:solar_system, identifier: 'ISOLATED-TEST')
      expect(isolated.compute_connectivity_status).to eq('orphaned')
    end
    
    it 'updates persisted status' do
      solar_system.update!(connectivity_status: 'connected')
      expect(solar_system.reload.connectivity_status).to eq('connected')
    end
  end
  
  describe 'scopes' do
    it 'returns correct systems for each scope' do
      # Test connected_systems, orphaned_systems, distressed_systems
    end
  end
end
```

### Step 10 — Verify isolation

Run the SolarSystem model spec:
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/solar_system_spec.rb -v 2>&1'
```

Expected result: All examples pass, 0 failures.

### Step 11 — Synthesis Report (before committing)

Pause and produce this report — **do not commit until user approves**:

```
SYNTHESIS REPORT
Files modified:
  - app/models/solar_system.rb — added enum + scopes + compute_connectivity_status + recompute_all_connectivity
  - db/migrate/[timestamp]_add_connectivity_status_to_solar_systems.rb — new file
  - db/schema.rb — auto-updated by migration
  - app/services/wormhole_manager.rb — integrated connectivity update on shift (if applicable)

Migration applied:
  ✅ dev database
  ✅ test database
  
Factory status:
  [specify: no changes needed | updated | needs update]

Spec result:
  [paste test output summary — should be: X examples, 0 failures]

RISKS
  - WormholeNavigator.find_shortest_path may need to filter by stable/traversable wormholes
  - SolarSystem used across Mission Planner, Settlement, and Wormhole expansion logic
  - No other models reference connectivity_status (verified via grep)
  - String enum values ensure compatibility with serialization

READY TO APPLY? — waiting for approval
```

---

## Testing Sequence

After Step 11 synthesis report and user approval:

1. **Isolation run**:
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/solar_system_spec.rb 2>&1'
```

Expected: X examples, 0 failures

2. **Related model specs** (ensure no regressions):
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/ --exclude spec/models/solar_system_spec.rb 2>&1 | tail -20'
```

Expected: no new failures

3. **Full suite** — only after steps 1 and 2 are green:
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec > /home/galaxy_game/log/rspec_full_$(date +%s).log 2>&1'
```

Then verify:
```bash
docker exec -it web bash -c 'tail -50 /home/galaxy_game/log/rspec_full_*.log | grep -E "examples?|failures?"'
```

---

## Acceptance Criteria
- [ ] Migration file generated and reviewed
- [ ] Migration runs cleanly on dev database without errors
- [ ] Migration runs cleanly on test database without errors
- [ ] `schema.rb` shows `connectivity_status` string column with default `'connected'`, null: false
- [ ] `SolarSystem` responds to `.connectivity_connected`, `.connectivity_distressed`, `.connectivity_orphaned` predicates
- [ ] `SolarSystem#compute_connectivity_status` returns correct status based on wormhole BFS
- [ ] Scopes `orphaned_systems`, `connected_systems`, `distressed_systems` return correct results
- [ ] `self.recompute_all_connectivity` updates all systems
- [ ] `spec/models/solar_system_spec.rb` passes with 0 failures
- [ ] No regressions in `spec/models/` suite
- [ ] Commit message follows format: `feature: solar_system — add connectivity_status enum derived from wormhole graph`

---

## Stop Conditions — escalate to user immediately if:
- Migration generates unexpected schema changes beyond connectivity_status column
- `solar_system.rb` already has a `connectivity_status` field under a different name
- Factory changes cause failures in specs other than solar_system_spec.rb
- Any architectural decision required beyond what is specified here
- WormholeNavigator.find_shortest_path doesn't exist or has incompatible signature

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add app/models/solar_system.rb
git add "db/migrate/[timestamp]_add_connectivity_status_to_solar_systems.rb"
git add db/schema.rb
git commit -m "feature: solar_system — add connectivity_status enum derived from wormhole graph"
git push
```

---

## Dependencies
**Blocked by**: none
**Blocks**: Mission Planner tier3 sourcing (separate HIGH-priority feature task)
**Related tasks**: 
- Mission Planner tier3 sourcing (next task in sequence)
- WormholeNavigator spec coverage (if needed)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `app/models/solar_system.rb` — added enum with values (connected/distressed/orphaned) + scopes + compute_connectivity_status + recompute_all_connectivity
- `db/migrate/[timestamp]_add_connectivity_status_to_solar_systems.rb` — new migration file
- `db/schema.rb` — auto-updated by migration run
- `app/services/wormhole_manager.rb` — integrated connectivity update on wormhole shift (if applicable)

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: Migration generated and applied | SolarSystem enum added with compute_connectivity_status (BFS via wormhole graph) + scopes | Ready for Mission Planner tier3 sourcing integration
