---
status: backlog
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Implementation Agent**.

Task: /Users/tam0013/Documents/git/agent-tasks/projects/galaxy_game/tasks/backlog/current/2026-07-12-HIGH-BUGFIX-GAMEDATA-GENERATOR.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy_game/tasks/backlog/current/2026-07-12-HIGH-BUGFIX-GAMEDATA-GENERATOR.md \
         projects/galaxy_game/tasks/active/2026-07-12-HIGH-BUGFIX-GAMEDATA-GENERATOR.md
  Then open the moved file and change: status: backlog → status: active

READ FIRST: Task file contains all details.
```

---

# TASK: GameDataGenerator fails to create output file
**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-12

---

## Context
`GameDataGeneratorSpec` expects a file at `output_path` after running the generator, but the file doesn't exist. This is likely a path resolution issue — the generator may be writing to a different location than the spec expects.

---

## Problem Statement
```
Failure/Error: expect(File).to exist(output_path)
  expected File to exist
```

**Current behavior**: Generator runs but output file not found at `output_path`
**Expected behavior**: File exists at the path returned by the generator

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|------|---------|-------------------|
| `galaxy_game/spec/services/generators/game_data_generator_spec.rb` | Spec (line 13) | Output path assertion |
| `galaxy_game/app/services/generators/game_data_generator.rb` | Generator service | File write logic |

### Reference Files
| File | Why You Need It |
|------|-----------------|
| `spec/factories/` | Any related factories |
| `config/initializers/` | Path configuration |

---

## Implementation Steps

### Step 1 — Investigate path mismatch
```bash
# Check what output_path the spec expects
grep -n "output_path" galaxy_game/spec/services/generators/game_data_generator_spec.rb

# Check where the generator actually writes
grep -rn "write\|save\|create_file" galaxy_game/app/services/generators/game_data_generator.rb
```

### Step 2 — Determine root cause
- Generator may be writing to `Rails.root.join('tmp', ...)` instead of spec's expected path
- Spec may need to use the generator's actual return value
- Path may need a directory creation step (`Dir.mkdir_p`)

### Step 3 — Apply fix and verify
```bash
docker-compose -f docker-compose.dev.yml exec -T web bundle exec rspec \
  spec/services/generators/game_data_generator_spec.rb:13 \
  --format documentation 2>&1 | tail -20
```

Expected result: 1 example, 0 failures

---

## Acceptance Criteria
- [ ] File exists at expected path after generator runs
- [ ] No regressions in related generator specs
- [ ] Path resolution works in both test and production environments

---

## Stop Conditions — escalate to user immediately if:
- Generator architecture needs rethinking (writes to DB instead of file)
- Fix requires changing more than 2 files

---

## Dependencies
**Blocked by**: none
**Blocks**: none
**Related tasks**: MaterialLookupService JSON error handling spec failure
