---
status: backlog
priority: HIGH
type: bug-fix
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## ⚡ Minimal Handoff

```
You are **Implementation Agent**.

Project: WVU Knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/2026-07-09-HIGH-BUGFIX-ADD-DUAL-LOGGING-DEVELOPMENT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-07-09-HIGH-BUGFIX-ADD-DUAL-LOGGING-DEVELOPMENT.md \
         projects/wvulibraries_knapsack/tasks/active/2026-07-09-HIGH-BUGFIX-ADD-DUAL-LOGGING-DEVELOPMENT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
```

---

# TASK: Add Dual Logging to Development Environment

**Status**: BACKLOG
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-09
**Branch**: `fix/facet-links-and-hide-type-facet`

---

## Prerequisites — READ FIRST

1. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/README.md`
2. **This Task File**: Everything below

---

## Context

Currently in development (Stack Car), logs only go to STDOUT via `RAILS_LOG_TO_STDOUT=true`. They are not written to `hyrax-webapp/log/development.log`, making it impossible to review logs after the container stops. This pairs with production logging fixes already committed (commit 7426102).

---

## Critical Information

### Architecture Gotchas

⚠️ **GOTCHA 1 — Hyrax is a Submodule**
- ❌ Wrong: Edit `config/environments/development.rb` in the root project
- ✅ Right: Edit `hyrax-webapp/config/environments/development.rb` in the submodule
- Why: The actual Rails app runs from inside hyrax-webapp; root's config is for the wrapper app only

⚠️ **GOTCHA 2 — Submodule Commits Must Be Done From Inside the Submodule**
- ❌ Wrong: `cd wvu_knapsack && git add hyrax-webapp/config/environments/development.rb`
- ✅ Right: `cd wvu_knapsack/hyrax-webapp && git add config/environments/development.rb && git push origin HEAD:<branch>`
- Why: Submodule is detached HEAD and has its own git remote

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start)

Create a synthesis report before any work. Save as MD file to summaries folder, do NOT paste in chat.

**Template**:
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Add Dual Logging to Development Environment
**Status**: backlog → active
**Date**: 2026-07-09

### What I'm About to Do
Replace STDOUT-only logging with dual logging (file + STDOUT) in hyrax-webapp's development environment.
Will verify with syntax check, local Stack Car test, and log file inspection.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `hyrax-webapp/config/environments/development.rb` | Development logger config | not started |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand: hyrax-webapp is submodule with separate git remote
- ✅ Know: commits must be pushed from inside hyrax-webapp directory

### Expected Outcomes
- Logs written to BOTH `hyrax-webapp/log/development.log` AND STDOUT
- File contains recent startup logs with proper timestamps
- Stack Car still streams live logs to terminal
- Syntax validation passes

### Critical Gotchas I Will Avoid
- ❌ Editing wrong development.rb (root vs submodule) — instead ✅ edit `hyrax-webapp/config/environments/development.rb`
- ❌ Committing from root repo — instead ✅ cd into hyrax-webapp and commit there

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

---

## Problem Statement

**Current behavior**: When running Stack Car (`./up.sc.local.sh`), all logs go to STDOUT only. The file `hyrax-webapp/log/development.log` is not created or populated.

**Expected behavior**: Logs should be written to both the file AND STDOUT, so developers can:
- Review logs after container stops
- Search large log files without terminal scroll limits
- Debug issues that persist after the container has exited

**Why this matters**: Debugging container failures is impossible without persisted logs. Users must either catch the error in real-time via terminal or lose the logs forever.

---

## Files Involved

### Primary File — you will edit this

| File | Purpose | Key Section |
|---|---|---|
| `hyrax-webapp/config/environments/development.rb` | Development logger configuration | Lines ~82-86 (logger setup) |

### Reference File — read but do not edit

| File | Why You Need It |
|---|---|
| `hyrax-webapp/config/environments/production.rb` | Already has dual logging implemented (see lines ~132-148); use as reference pattern |

---

## Implementation Steps

### Step 0 — Move task file to active/ (MANDATORY FIRST STEP)

**Do this before anything else:**

```bash
cd /Users/tam0013/Documents/git/agent-tasks

git mv projects/wvulibraries_knapsack/tasks/backlog/2026-07-09-HIGH-BUGFIX-ADD-DUAL-LOGGING-DEVELOPMENT.md \
       projects/wvulibraries_knapsack/tasks/active/2026-07-09-HIGH-BUGFIX-ADD-DUAL-LOGGING-DEVELOPMENT.md
```

Then open the moved file and change the YAML status:
```yaml
status: backlog  →  status: active
```

Verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks \
     -name "2026-07-09-HIGH-BUGFIX-ADD-DUAL-LOGGING-DEVELOPMENT.md"
```

**Expected**: Exactly one result at `tasks/active/2026-07-09-HIGH-BUGFIX-ADD-DUAL-LOGGING-DEVELOPMENT.md`

Paste the find output in chat before proceeding to Step 1.

### Step 1 — Create Synthesis Report

Before making any code changes, create a synthesis report file in the summaries folder:

```bash
cat > /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/2026-07-09-SYNTHESIS-ADD-DUAL-LOGGING-DEVELOPMENT.md << 'EOF'
[Use the template from "REQUIRED: Status Synthesis Report" section above]
EOF
```

Post link to this file in chat before proceeding to Step 2.

### Step 2 — Modify hyrax-webapp development.rb

Navigate to the file:
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack/hyrax-webapp
```

Open: `config/environments/development.rb`

**Find this section** (around line 82-86):
```ruby
  config.web_console.whitelisted_ips = ['172.18.0.0/16', '172.27.0.0/16', '0.0.0.0/0']
  if ENV["RAILS_LOG_TO_STDOUT"].present?
    logger           = ActiveSupport::Logger.new(STDOUT)
    logger.formatter = config.log_formatter
    config.logger = ActiveSupport::TaggedLogging.new(logger)
  end
```

**Replace with**:
```ruby
  config.web_console.whitelisted_ips = ['172.18.0.0/16', '172.27.0.0/16', '0.0.0.0/0']
  
  # Always log to file (for development debugging)
  file_logger = ActiveSupport::Logger.new(
    Rails.root.join('log', 'development.log')
  )
  file_logger.formatter = config.log_formatter

  # Log to STDOUT if enabled + also to file (dual logging)
  if ENV["RAILS_LOG_TO_STDOUT"].present?
    stdout_logger = ActiveSupport::Logger.new(STDOUT)
    stdout_logger.formatter = config.log_formatter
    config.logger = ActiveSupport::TaggedLogging.new(
      ActiveSupport::Logger.broadcast(file_logger).broadcast(stdout_logger)
    )
  else
    config.logger = ActiveSupport::TaggedLogging.new(file_logger)
  end
```

### Step 3 — Verify Syntax

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack/hyrax-webapp
ruby -c config/environments/development.rb
```

**Expected result**: `Syntax OK`

If not OK, fix the syntax error and re-run.

### Step 4 — Local Testing

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Stop existing Stack Car
sh down.sc.local.sh

# Wait for containers to stop
sleep 3

# Start Stack Car with new logging config
sh ./up.sc.local.sh
```

Wait 2-3 minutes for containers to fully start and the app to boot.

### Step 5 — Verify Logs Are Written

```bash
# Check that log file exists and has content
ls -lah hyrax-webapp/log/development.log

# View recent logs
tail -50 hyrax-webapp/log/development.log
```

**Expected**:
- File exists with recent timestamp (within the last 3 minutes)
- Contains Rails startup messages, request logs, etc.
- File size > 100KB typically for a full startup

### Step 6 — Verify STDOUT Still Works

```bash
# Live logs should still be visible:
sc logs web -f 2>&1 | head -20
```

**Expected**: Live logs streaming to terminal (same as before)

### Step 7 — Commit from Inside Submodule

**CRITICAL**: Commit from inside the hyrax-webapp directory, not the root:

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack/hyrax-webapp

git add config/environments/development.rb

git commit -m "fix: Add dual logging to development environment for file capture

- Broadcast logs to both file (log/development.log) and STDOUT
- Ensures logs are captured to hyrax-webapp/log/development.log for debugging
- Maintains STDOUT logging for Stack Car visibility"

git push origin HEAD:fix/facet-links-and-hide-type-facet
```

**Note**: The push uses `HEAD:<branch>` because the submodule is in detached HEAD state.

### Step 8 — Move Task File to Completed

From the agent-tasks repo root:

```bash
cd /Users/tam0013/Documents/git/agent-tasks

# Move to completed with date subfolder
git mv projects/wvulibraries_knapsack/tasks/active/2026-07-09-HIGH-BUGFIX-ADD-DUAL-LOGGING-DEVELOPMENT.md \
       projects/wvulibraries_knapsack/tasks/completed/2026-07/2026-07-09-HIGH-BUGFIX-ADD-DUAL-LOGGING-DEVELOPMENT.md

git add projects/wvulibraries_knapsack/tasks/completed/2026-07/2026-07-09-HIGH-BUGFIX-ADD-DUAL-LOGGING-DEVELOPMENT.md

git commit -m "task: Complete dual logging development environment fix"

git push
```

---

## Acceptance Criteria

- [x] `hyrax-webapp/config/environments/development.rb` modified with dual logging logic
- [x] Syntax validation passes: `ruby -c config/environments/development.rb` returns "Syntax OK"
- [x] Local Stack Car test: `hyrax-webapp/log/development.log` exists and contains logs
- [x] STDOUT logging still works: `sc logs web -f` shows live output
- [x] Commit pushed to `fix/facet-links-and-hide-type-facet` branch from inside hyrax-webapp
- [x] Task file moved to completed/

---

## Stop Conditions

Escalate to user immediately if:
- Ruby syntax check fails after fix
- Log file not created after Stack Car starts
- Stack Car fails to start after changes
- STDOUT logging breaks or stops working
- Commits fail to push (submodule remote issues)

---

## Related Work

**Completed Context**:
- Production logging fixes already implemented (commit 7426102 on `fix/facet-links-and-hide-type-facet`)
  - Added dual logging to `config/environments/production.rb`
  - Added Docker log rotation to `docker-compose.production.yml`
  - Both changes follow identical pattern to development logging

**Next Steps After This**:
- Deploy to HykuDev for testing
- Verify logs appear in `/data/logs/rails/production.log` on HykuDev
- Deploy to production if HykuDev validation passes
- Close GitHub issue #8
