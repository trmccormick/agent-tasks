---
status: drafts
resume: false
priority: CRITICAL
type: feature
system_domain: CONTROLLERS
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 DRAFT REVIEW CHECKLIST

- [ ] Acceptance Criteria are clear and measurable
- [ ] Task scope is well-bounded
- [ ] Dependencies listed and verified
- [ ] No contradictions in specification
- [ ] Review agent disposition: GO

---

## 🔴 Agent Dispatch Interface (Required)

```
You are **Implementation Agent**.

Project: galaxy-game-ui
Task: <agent-tasks>/projects/galaxy-game-ui/tasks/drafts/[FILENAME].md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/galaxy-game-ui/tasks/drafts/[FILENAME].md \
         projects/galaxy-game-ui/tasks/active/[FILENAME].md
  Then open the moved file and change: status: drafts → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: drafts → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/galaxy-game-ui/tasks -name "[FILENAME].md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/galaxy-game-ui/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**

---

# TASK: Remove Legacy Donor Seeding Code and Create Default Admin/Player Users
**Status**: DRAFTS  
**Priority**: CRITICAL  
**Type**: refactor  
**Created**: 2026-10-23  
**Last Updated**: 2026-10-23  

---

## 🔄 PARTIAL COMPLETION NOTE (2026-10-23)

**⚠️ PLANNING AGENT HAS STARTED THIS TASK — Some work already done:**

✅ **COMPLETED** (commit 9f64cd3):
- Deleted `app/services/donor_csv_loader.rb`
- Deleted all related spec/test files:
  - `spec/factories/graduate_records.rb`
  - `spec/models/graduate_record_spec.rb`
  - `spec/requests/graduate_records_spec.rb`
  - `spec/services/donor_csv_loader_spec.rb`
  - `test/fixtures/graduate_records.yml`
  - `test/models/graduate_record_test.rb`

❌ **STILL NEEDED** (for this task to complete):
- **Step 3**: Rewrite `db/seeds.rb` with hardcoded admin/player account creation
- **Step 4**: Update `.gitignore` to add `galaxy-game-ui-first-attempt/`
- **Step 5-6**: Verify setup.sh and login routes work cleanly
- Create synthesis report in `summaries/`

**Rationale**: The dead code cleanup was prerequisite housekeeping; the core task work (seeds rewrite + .gitignore update + verification) remains.

**Next agent**: Continue from Step 3 of Implementation Steps below. Do NOT re-delete files or re-do cleanup work already committed.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/rules/GUARDRAILS.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/galaxy-game-ui/README.md`
3. **Context**: See Status & Context below

---

## Context

Phases 1–3 of the fresh Galaxy Game UI cleanup are complete:
- GraduateRecord model, controller, views, routes, and migrations removed ✅
- User role field added (enum: player/admin) ✅
- Database renamed to galaxy_game_ui_development/test ✅

The `setup.sh` script is failing because it still calls legacy donor seeding code (`DonorCsvLoader`) that references a CSV file (`data/donors.csv`) that no longer exists. This blocks all further work — users cannot log in and the app cannot start cleanly.

Additionally, the `galaxy-game-ui-first-attempt/` folder should be git-ignored so it does not pollute the repository.

## Current State

### Files That Must Be Deleted
| File | Status | Reason |
|------|--------|--------|
| `app/services/donor_csv_loader.rb` | ✅ **ALREADY DELETED** (commit 9f64cd3) | Legacy donor CSV loading service |
| Donor/graduate spec/test files | ✅ **ALREADY DELETED** (commit 9f64cd3) | Dead code, no longer needed |

### db/seeds.rb Current Content (BROKEN)
```ruby
users = UserCsvLoader.call
puts "Users CSV loaded: #{users.created} created, #{users.skipped} skipped."

result = DonorCsvLoader.call
puts "Donor CSV loaded: #{result.created} created, #{result.updated} updated, #{result.skipped} skipped."
```

### .gitignore Current Content (incomplete)
```
.DS_Store
/data/
/data/*
/env/
/env/*
```

### users.csv Location (already updated with credentials)
`data/imports/users.csv` — already contains admin and player accounts. Not needed for seeds.rb but kept as reference data.

---

## Implementation Steps

### ✅ Step 1-2: Delete donor_csv_loader.rb and related files
**STATUS**: ALREADY COMPLETED (commit 9f64cd3)

Files have been deleted from both host and container. Do NOT re-run these steps.

```bash
# VERIFY ALREADY DONE:
grep -r "DonorCsvLoader" /Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui/app 2>/dev/null
# Expected result: (empty — file gone)
```

### Step 3: Rewrite db/seeds.rb
Replace the entire content with this (inline admin + player accounts):
```ruby
# Create default admin and player accounts if they do not exist.
admin = User.find_by(email_address: 'admin@galaxygame.local') || User.create!(
  email_address: 'admin@galaxygame.local',
  password_digest: BCrypt::Password.create('password123'),
  role: 'admin'
)

player = User.find_by(email_address: 'player@galaxygame.local') || User.create!(
  email_address: 'player@galaxygame.local',
  password_digest: BCrypt::Password.create('password123'),
  role: 'player'
)

puts "Default accounts ready:"
puts "  Admin:    admin@galaxygame.local / password123"
puts "  Player:   player@galaxygame.local / password123"
```

> Use `BCrypt::Password.create` instead of `has_secure_password`'s `.password=` setter because seeds.rb runs outside the normal Rails controller context and we need a one-shot digest. The `has_secure_password` concern on the User model will still validate during login.

### Step 4: Update .gitignore
Add to `/Users/tam0013/Documents/git/galaxy-game-ui/.gitignore`:
```
galaxy-game-ui-first-attempt/
```
Append this line after the existing `/env/*` line.

### Step 5: Verify setup.sh runs cleanly
```bash
docker exec galaxy_game bash -c 'unset DATABASE_URL && bin/rails db:migrate'
docker exec galaxy_game bash -c 'unset DATABASE_URL && bin/rails db:seed'
```

Expected output:
```
== 20261023135130 AddRoleToUsers: migrating ...
   -> OK
Default accounts ready:
  Admin:    admin@galaxygame.local / password123
  Player:   player@galaxygame.local / password123
```

### Step 6: Verify login routes intact
```bash
docker exec galaxy_game bash -c "unset DATABASE_URL && bin/rails routes | grep session"
```
Expected: `new_session GET    /session/new` and all other session routes present.

---

## Gotchas

1. **Docker bind mounts**: All files live on the host at `/Users/tam0013/Documents/git/galaxy-game-ui/galaxy-game-ui/`. Deletions must happen on both host and container (they are the same file via bind mount — deleting from either removes it everywhere).

2. **BCrypt in seeds**: Must call `BCrypt::Password.create()` directly; do NOT use `User.new(password: '...')` because that requires the password column to exist, which it does, but `password_digest=` is not available without going through `has_secure_password`. Direct BCrypt create is safest.

3. **Idempotency**: Seeds should be re-runnable — using `find_by || create!` pattern ensures no duplicates on repeated runs.

---

## Acceptance Criteria

- [ ] `app/services/donor_csv_loader.rb` deleted (confirmed via grep)
- [ ] `app/services/user_csv_loader.rb` deleted (confirmed via grep)
- [ ] `db/seeds.rb` contains only admin/player account creation code (no donor references)
- [ ] `.gitignore` includes `galaxy-game-ui-first-attempt/`
- [ ] `bin/rails db:seed` runs without errors and outputs confirmation of default accounts
- [ ] Login routes (`/session/new`) still functional
- [ ] No production database was touched (confirmed via config/database.yml)

---

## Dependencies

**Depends on**: None — this is a cleanup prerequisite for all Phase 4+ work.  
**Blocks**: ALL dashboard/landing/wormholes/blueprints/admin tasks — users cannot authenticate until default accounts exist and seeds run cleanly.
