---
status: backlog
priority: HIGH
type: data
system_domain: TERRA_SIM
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

# TASK: Data Foundation — SolarSystem Connectivity Status
**Status**: BACKLOG
**Priority**: HIGH
**Type**: data
**Created**: 2026-03-26
**Last Updated**: 2026-05-29

---

## Local Worker Triage Report
*Filled in by local model (Ollama via Continue) during backlog review*

- **Template Conformance**: PASS — all sections present
- **Docker Wrapper Check**: PASS — RSpec strings use correct `docker exec` format
- **MVP Alignment**: VALID — unblocks Mission Planner orphaned system logic, critical for settlement connectivity awareness
- **MVP Impact Note**: Enables Mission Planner to determine settlement access to Network Import (Tier 3 sourcing); blocks AI Manager resource allocation for orphaned systems
- **Action Line**: READY FOR CLOUD HANDOFF — well-specified, single model, single migration, explicit paths and commands provided

---

## Agent Assignment

**Assigned To**: GPT-4.1 0x
**Why This Agent**: Single model, single migration, fully specified with explicit file paths and migration commands — no inference required
**Supervision Level**: Watched carefully

> ⚠️ 0x agent: read every section carefully before starting.
> Do not infer file paths or method names — they are provided explicitly below.
> Generate migration with generator command, verify output, then apply.

---

## Context
The Mission Planner needs to know whether a solar system is connected to the wider network (Earth/Sol imports available) or orphaned (cut off from external supply). This is a system-level property — if a system is orphaned, every settlement in it loses access to Network Import (Tier 3 sourcing), forcing reliance on ISRU and local production. This task adds the data foundation only. No service or UI logic is included here.

**Relevant Architecture Docs** — read before starting:
- `docs/architecture/systems/asteroid_conversion_physics.md` — background on orphaned system constraints
- `docs/architecture/systems/survey_and_handshake_protocol.md` — context on system connectivity

> Do not create any documentation during this task.
> Flag any gaps in your completion report.

---

## Problem Statement
`SolarSystem` model has no `connectivity_status` field. The Mission Planner cannot determine whether a system is connected, distressed, or orphaned. This blocks all Tier 3 sourcing logic.

**Current behavior**: `SolarSystem` has no connectivity awareness; schema lacks connectivity_status column.
**Expected behavior**: `SolarSystem` has a `connectivity_status` string column with enum values `:connected`, `:distressed`, `:orphaned`, defaulting to `:connected`.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/models/solar_system.rb` | SolarSystem model | add enum + scopes |
| `db/migrate/[timestamp]_add_connectivity_status_to_solar_systems.rb` | Migration | new file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `db/schema.rb` | Verify column added correctly after migration |
| `spec/factories/solar_systems.rb` | Check factory needs updating with default status |

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

The generator output will be minimal. Update it to:
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

### Step 6 — Add enum to SolarSystem model

In `app/models/solar_system.rb`, add after existing validations (after `validates :identifier, presence: true, uniqueness: true`):

```ruby
enum connectivity_status: {
  connected: 'connected',
  distressed: 'distressed',
  orphaned: 'orphaned'
}, _prefix: :connectivity

scope :orphaned_systems, -> { where(connectivity_status: 'orphaned') }
scope :connected_systems, -> { where(connectivity_status: 'connected') }
```

Use string-based enum values to match the string column — do NOT use integer values.

### Step 7 — Check factory

Inspect `spec/factories/solar_systems.rb`. Confirm:
- Factory either omits `connectivity_status` (DB default will handle it), OR
- Factory explicitly sets `connectivity_status { 'connected' }`

No change required if defaults are working correctly.

### Step 8 — Verify isolation

Run the SolarSystem model spec:
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/solar_system_spec.rb -v 2>&1'
```

Expected result: All examples pass, 0 failures.

### Step 9 — Synthesis Report (before committing)

Pause and produce this report — **do not commit until user approves**:

```
SYNTHESIS REPORT
Files modified:
  - app/models/solar_system.rb — added enum + 2 scopes (lines ~22-29)
  - db/migrate/[timestamp]_add_connectivity_status_to_solar_systems.rb — new file
  - db/schema.rb — auto-updated by migration

Migration applied:
  ✅ dev database
  ✅ test database
  
Factory status:
  [specify: no changes needed | updated | needs update]

Spec result:
  [paste test output summary — should be: X examples, 0 failures]

RISKS
  - SolarSystem used across Mission Planner, Settlement, and Wormhole expansion logic
  - No other models reference connectivity_status (verified via grep)
  - String enum values ensure compatibility with serialization

READY TO APPLY? — waiting for approval
```

---

## Testing Sequence

After Step 9 synthesis report and user approval:

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
- [ ] `SolarSystem` responds to `connectivity_status_before_type_cast`
- [ ] Scopes `orphaned_systems` and `connected_systems` return correct results (testable via console)
- [ ] `spec/models/solar_system_spec.rb` passes with 0 failures
- [ ] No regressions in `spec/models/` suite
- [ ] Commit message follows format: `feature: solar_system — add connectivity_status enum (connected/distressed/orphaned)`

---

## Stop Conditions — escalate to user immediately if:
- Migration generates unexpected schema changes beyond connectivity_status column
- `solar_system.rb` already has a `connectivity_status` field under a different name or uses a different enum structure
- Factory changes cause failures in specs other than solar_system_spec.rb
- Any architectural decision required beyond what is specified here
- String enum values fail to serialize/deserialize correctly (rare but possible with PostgreSQL)

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add app/models/solar_system.rb
git add "db/migrate/[timestamp]_add_connectivity_status_to_solar_systems.rb"
git add db/schema.rb
git commit -m "feature: solar_system — add connectivity_status enum (connected/distressed/orphaned)"
git push
```

---

## Documentation
- [ ] No doc changes needed for this task — doc creation is part of related Mission Planner feature task

---

## Dependencies
**Blocked by**: none
**Blocks**: Mission Planner orphaned system logic (separate HIGH-priority feature task)
**Related tasks**: 
- Mission Planner feature task (not yet in backlog)
- Network Import sourcing validation (downstream)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `app/models/solar_system.rb` — added enum with values (connected/distressed/orphaned) + orphaned_systems and connected_systems scopes
- `db/migrate/[timestamp]_add_connectivity_status_to_solar_systems.rb` — new migration file
- `db/schema.rb` — auto-updated by migration run

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: Migration generated and applied | SolarSystem enum added with 2 scopes | Ready for Mission Planner feature integration
