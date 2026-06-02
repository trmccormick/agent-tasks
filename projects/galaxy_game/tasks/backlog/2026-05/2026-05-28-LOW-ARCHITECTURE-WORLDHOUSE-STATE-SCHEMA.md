---
status: backlog
priority: LOW
type: architecture
system_domain: MANUFACTURING
mvp_alignment: OTHER
local_worker_safe: false
---

# TASK: Define and Integrate Worldhouse State Schema

**Status**: BACKLOG
**Priority**: LOW
**Type**: architecture
**Created**: 2026-04-17
**Last Updated**: 2026-05-28

---

## Local Worker Triage Report

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A — schema definition task, no RSpec runs until implementation
- **MVP Alignment**: STALE — worldhouse is post-Luna MVP, models exist but services not built yet
- **MVP Impact Note**: Not blocking Luna MVP — worldhouse construction is a later game tier after L1 shipyard is operational
- **Action Line**: NEEDS MANUAL REVIEW — blocked until worldhouse architectural plan is complete

---

## Agent Assignment

**Assigned To**: GPT-4.1 0x
**Why This Agent**: Schema definition and service integration, explicit file targets
**Supervision Level**: Watched carefully

---

## Context

Worldhouse is a mega-structure — a dome enclosing a crater for habitation. Models exist (`worldhouse.rb`, `worldhouse_segment.rb`) but the construction, maintenance, and failure services have not been built. This task defines the canonical JSON schemas for all worldhouse states and integrates them with the relevant simulation and AI modules.

All simulation and AI modules must use these schemas for state tracking and validation. This task is blocked until the worldhouse architectural plan is complete.

**Relevant Architecture Docs** — read before starting:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules

---

## Problem Statement

**Current behavior**: No canonical JSON schema exists for worldhouse states. Construction, maintenance, and failure modules cannot share a consistent state representation.

**Expected behavior**: A validated JSON schema defines all worldhouse states. All relevant modules use this schema for state tracking and validation.

---

## Files Involved

### Primary Files — you will create/edit these
| File | Purpose |
|---|---|
| `data/schemas/worldhouse_state.schema.json` | Canonical JSON schema |
| `app/models/structures/worldhouse.rb` | Schema validation integration |
| `app/services/worldhouse_maintenance.rb` | Create if not exists |
| `app/services/worldhouse_failure_analyzer.rb` | Create if not exists |
| `app/services/ai_manager/worldhouse_learning.rb` | Create if not exists |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/models/structures/worldhouse_segment.rb` | Segment model structure |
| `app/models/structures/base_structure.rb` | Base structure patterns |

---

## Implementation Steps

### Step 1 — Audit existing worldhouse models
```bash
cat galaxy_game/app/models/structures/worldhouse.rb
cat galaxy_game/app/models/structures/worldhouse_segment.rb
```

### Step 2 — Synthesis Report
```
SYNTHESIS REPORT
Worldhouse model fields: [list]
WorldhouseSegment model fields: [list]
Existing state tracking: [any status/state fields]
Services that exist: [list]
Services that need creating: [list]
Proposed schema states: [construction, maintenance, failure — outline]
READY TO APPLY? — waiting for approval
```

### Step 3 — Draft canonical JSON schema (after approval)
Create `data/schemas/worldhouse_state.schema.json` covering:
- Construction state (progress, segments completed, materials consumed)
- Maintenance state (integrity, last inspection, pending repairs)
- Failure state (breach location, severity, response status)

### Step 4 — Integrate schema validation with modules

### Step 5 — Write RSpec coverage
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/models/structures/worldhouse_spec.rb spec/services/worldhouse_maintenance_spec.rb 2>&1 | tail -20'
```

---

## Acceptance Criteria
- [ ] Architectural plan confirmed complete before starting
- [ ] Canonical JSON schema defined and validated for all worldhouse states
- [ ] Schema integrated with construction, maintenance, failure, and AI learning modules
- [ ] RSpec coverage for schema validation and integration
- [ ] 0 new failures in full suite

---

## Stop Conditions — escalate immediately if:
- Worldhouse architectural plan not found or incomplete — do not proceed
- Schema requires migration changes — stop, report
- More than 5 services need creating — scope is too large, break down further

---

## Commit Instructions
```bash
git add galaxy_game/data/schemas/worldhouse_state.schema.json
git add galaxy_game/app/models/structures/worldhouse.rb
git add galaxy_game/app/services/worldhouse_maintenance.rb
git add galaxy_game/app/services/worldhouse_failure_analyzer.rb
git add galaxy_game/app/services/ai_manager/worldhouse_learning.rb
git commit -m "arch: worldhouse state schema — canonical JSON schema and service integration"
git push
```

---

## Dependencies
**Blocked by**: Worldhouse architectural plan — do not start until confirmed complete
**Blocks**: nothing currently
**Related**: `2026-04-17-CRITICAL-ARCHITECTURE-ENCLOSED-ATMOSPHERE-FAILURE-PREDICTION-PLANNING.md`

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
HANDOFF SUMMARY: Worldhouse state schema defined | Services created | Schema validation integrated | RSpec coverage added
