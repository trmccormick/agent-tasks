---
status: active
priority: CRITICAL
type: bug-fix
system_domain: WVU_KNAPSACK
mvp_alignment: OPERATIONS_VISIBILITY
local_worker_safe: true
github_issue: https://github.com/wvulibraries/wvu_knapsack/issues/8
---

## ⚡ Minimal Handoff

```
You are **Implementation Agent**.

Project: WVU Knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/active/2026-07-09-CRITICAL-BUGFIX-PRODUCTION-LOGGING-NOT-CAPTURED.md

PREREQUISITES:
1. Read synthesis report: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/2026-07-09-SYNTHESIS-LOGGING-HYKUDEV-INVESTIGATION.md
2. Review this task file for implementation steps
3. Branch: fix/facet-links-and-hide-type-facet (or create new branch if critical hotfix needed)

CRITICAL: Save STATUS SYNTHESIS REPORT as MD file to summaries/ folder BEFORE any implementation.
Path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/
Filename: YYYY-MM-DD-SYNTHESIS-LOGGING-PRODUCTION-FIX.md
```

---

# TASK: Fix Production Logging — Ensure Rails & Worker Logs Are Captured Outside Containers

**Status**: ACTIVE
**Priority**: CRITICAL
**Type**: bug-fix
**Created**: 2026-07-09
**Last Updated**: 2026-07-09
**Branch**: `fix/facet-links-and-hide-type-facet` (or hotfix branch)
**GitHub Issue**: https://github.com/wvulibraries/wvu_knapsack/issues/8

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Synthesis Report** (Required — read first):
   `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/2026-07-09-SYNTHESIS-LOGGING-HYKUDEV-INVESTIGATION.md`
   - Contains root cause analysis and detailed findings
   - Reviews all environment configurations
   - Proposes 4 fixes in order of priority

2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/README.md`
   - Understand knapsack structure and deployment

3. **This Task File**: Everything below

---

## Context

**Production Issue**: Logs are not being captured outside containers on production/hykudev VMs.

**Impact**:
- Cannot debug container startup failures
- Cannot analyze what went wrong if a service fails
- DevOps cannot perform diagnostics
- **BLOCKER for production operations**

**What's Known** (from synthesis report):
- Rails is configured with `RAILS_LOG_TO_STDOUT=true` in all environments
- Production compose has volume mount: `./data/logs/rails:/app/samvera/hyrax-webapp/log`
- Issue: STDOUT logging bypasses the volume mount; file-based logging has permission issues
- Solution: Implement dual logging (both STDOUT AND file-based) with proper permissions

**GitHub Issue**: https://github.com/wvulibraries/wvu_knapsack/issues/8
> "Not seeing any logs other than some solr logs outside of the containers. We need to figure out how to map logs outside the containers so we can analyze what went wrong if a container fails to start, etc."

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start)

Create this report BEFORE modifying any files.

**Save as**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/YYYY-MM-DD-SYNTHESIS-LOGGING-PRODUCTION-FIX.md`

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Fix Production Logging — Ensure Rails & Worker Logs Captured Outside Containers
**Status**: Starting Implementation
**Date**: 2026-07-09

### What I'm About to Do
Implement dual logging in production Rails environment so logs are captured:
1. To STDOUT (for Docker log capture via `docker logs`)
2. To `/data/logs/rails/production.log` (for direct file access on host)

This fixes the issue where logs disappear when containers fail or restart.

### Root Cause (from synthesis report)
- `RAILS_LOG_TO_STDOUT=true` sends logs to STDOUT only, bypassing the volume mount at `./data/logs/rails`
- File-based logging fails due to permission issues (`root:root` ownership)
- Result: No persistent logs accessible on the host

### Files I Will Modify
| File | Change | Purpose |
|---|---|---|
| `config/environments/production.rb` | Add dual logging config | Broadcast logs to both STDOUT and file |
| `docker-compose.production.yml` | Add log rotation + ensure mount | Capture Docker STDOUT and persist files |
| `.env.production.example` | Document RAILS_LOG_TO_STDOUT | Template for DevOps |

### Implementation Approach
1. Modify production Rails logger to broadcast to BOTH stdout and file
2. Add Docker log rotation to prevent disk fill
3. Document setup steps for production deployment
4. Test on local dev first, then prepare for production rollout

### Prerequisites Completed
- ✅ Read synthesis report (findings show dual logging is the fix)
- ✅ Understand root cause (STDOUT overrides file logging)
- ✅ Know files to modify (production.rb, compose file, env template)
- ✅ Understand architecture gotchas (permissions, volume mounts)

### Expected Outcomes
- Rails logs written to BOTH STDOUT and `/data/logs/rails/production.log`
- Worker logs persisted similarly
- Logs survive container restart
- No permission errors when Docker starts containers

### Critical Gotchas I Will Avoid
- ❌ DO NOT use `||` (OR) in logger setup — logs must go to BOTH destinations
- ❌ DO NOT forget to handle the case where `RAILS_LOG_TO_STDOUT` is false/empty
- ❌ DO NOT add log setup that only works on production (must work locally too)
- ✅ Instead: Use `ActiveSupport::Logger.broadcast()` for dual destination

---

**SYNTHESIS COMPLETE.** Ready to implement fixes 1-3 from synthesis report.
```

---

## Problem Statement

**Issue**: Rails/worker logs not being captured to files outside containers on production.

**Current behavior**:
- Only "some solr logs" visible outside containers
- Rails/worker logs disappear if container restarts
- Cannot debug startup failures

**Expected behavior**:
- All Rails logs written to `/data/logs/rails/production.log` on the host
- Worker logs similarly captured
- Logs persist across container restarts
- Accessible for diagnostics even if container fails

---

## Files Involved

### Primary Files — you will edit these

| File | Change | Key Section |
|---|---|---|
| `config/environments/production.rb` | Add dual logging (STDOUT + file) | Lines ~132-136 (current logger config) |
| `docker-compose.production.yml` | Add log rotation + verify mount | `services.web.logging` + `services.web.volumes` |
| `.env.production.example` | Clarify RAILS_LOG_TO_STDOUT | Top of file or dedicated section |

### Reference Files — read but do not modify

| File | Why You Need It |
|---|---|
| `config/environments/development.rb` | Compare logger config in dev (don't modify) |
| `docker-compose.yml` | Reference how local dev handles logs |
| Synthesis report | Architecture and root cause findings |

---

## Implementation Steps

### Step 0 — Move task to active (if not already done)
This task should already be in `active/` but verify:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks \
     -name "2026-07-09-CRITICAL-BUGFIX-PRODUCTION-LOGGING-NOT-CAPTURED.md"
# Expected: exactly one result at active/ path
```

Then update YAML header: `status: backlog → status: active` (if needed).

### Step 1 — Verify Current Production Logger Config

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Show current production logger setup
echo "=== CURRENT PRODUCTION LOGGER CONFIG ==="
sed -n '130,140p' config/environments/production.rb

# Show if development has any special handling
echo "=== DEVELOPMENT LOGGER CONFIG (for reference) ==="
sed -n '50,60p' config/environments/development.rb
```

### Step 2 — Implement Dual Logging in production.rb

Replace the current logger config (lines ~132-136) with dual logging:

**Before** (current code):
```ruby
if ENV["RAILS_LOG_TO_STDOUT"].present?
  logger           = ActiveSupport::Logger.new(STDOUT)
  logger.formatter = config.log_formatter
  config.logger = ActiveSupport::TaggedLogging.new(logger)
end
```

**After** (dual logging to STDOUT + file):
```ruby
# Dual logging: write to both STDOUT and file for production debugging
# STDOUT is captured by Docker; file is captured by volume mount at ./data/logs/rails
file_logger = ActiveSupport::Logger.new(
  Rails.root.join('log', 'production.log')
)
file_logger.formatter = config.log_formatter

if ENV["RAILS_LOG_TO_STDOUT"].present?
  # Log to STDOUT if explicitly enabled (via Docker or dev setup)
  stdout_logger = ActiveSupport::Logger.new(STDOUT)
  stdout_logger.formatter = config.log_formatter
  
  # Broadcast to BOTH destinations
  config.logger = ActiveSupport::TaggedLogging.new(
    ActiveSupport::Logger.broadcast(file_logger).broadcast(stdout_logger)
  )
else
  # Fallback: file-only logging
  config.logger = ActiveSupport::TaggedLogging.new(file_logger)
end
```

**Exact file edit**:
- File: `config/environments/production.rb`
- Lines: ~132-136 (or find the `if ENV["RAILS_LOG_TO_STDOUT"]` block)
- Replace the 5-line conditional with the 21-line dual logging config above

### Step 3 — Add Docker Log Rotation to docker-compose.production.yml

Find the `web:` service in `docker-compose.production.yml` and add logging configuration after the `command:` line:

**Look for**:
```yaml
  web:
    build:
      context: .
      dockerfile: Dockerfile
      args:
        RAILS_ENV: production
    image: ghcr.io/wvulibraries/wvu_knapsack/web:latest
    container_name: wvu_knapsack-web
    command: bundle check || bundle install && ./bin/web
    depends_on:
      # ... dependencies ...
```

**Add after `command:` line** (inside the `web:` service block):
```yaml
    logging:
      driver: json-file
      options:
        max-size: "100m"
        max-file: "3"
    environment:
      - RAILS_LOG_TO_STDOUT=true
```

**Also verify the volume mount exists**:
```yaml
    volumes:
      # ... other volumes ...
      - ./data/logs/rails:/app/samvera/hyrax-webapp/log:cached
      # ... more volumes ...
```

If `./data/logs/rails` mount is missing, add it.

### Step 4 — Update .env.production.example

Add or clarify the RAILS_LOG_TO_STDOUT variable:

**In `.env.production.example`**, ensure this exists:
```bash
# Rails logging configuration
# Set to 'true' to send logs to stdout (captured by Docker json-file driver)
# Set to empty or false to use file-based logging only
RAILS_LOG_TO_STDOUT=true
```

### Step 5 — Create Directory Setup Documentation

Add a comment to `up.sh` or create a README section documenting the log directory setup:

**In `up.sh` or as a separate setup step**:
```bash
#!/bin/bash
# Ensure log directory exists with correct permissions BEFORE docker compose starts
mkdir -p ./data/logs/rails
mkdir -p ./data/logs/solr
chmod -R 755 ./data/logs/rails
chmod -R 755 ./data/logs/solr

# Run docker compose...
docker compose -f docker-compose.production.yml up -d
```

Add this setup to the deployment documentation (or to `up.sh` if it doesn't already exist).

### Step 6 — Verify on Local Dev (Before Deploying)

Test the logging changes locally using Stack Car to ensure they work:

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Clean up old logs
rm -rf data/logs/rails/*

# Start Stack Car with the updated config
sh up.sc.local.sh

# Wait for containers to start
sleep 10

# Check logs appear in both locations
echo "=== STDOUT LOGS (via sc logs) ==="
sc logs web 2>&1 | head -20

# Check file-based logs (if they exist in local dev)
echo "=== FILE-BASED LOGS ==="
ls -la data/logs/rails/ 2>/dev/null || echo "Note: May not exist in local dev, expected in production"
cat data/logs/rails/production.log 2>/dev/null || echo "No file logs in local dev (OK, STDOUT is primary)"
```

**Expected result**: Logs appear in both sources. If logs don't appear, check the synthesis report gotchas section.

### Step 7 — Verify Syntax

```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Check for Ruby syntax errors
ruby -c config/environments/production.rb
echo "✅ Production config syntax OK"

# Check YAML syntax in compose file
docker compose -f docker-compose.production.yml config > /dev/null
echo "✅ Docker compose syntax OK"
```

### Step 8 — Synthesis Report (Before Committing)

Create a completion synthesis:

```markdown
## IMPLEMENTATION SYNTHESIS REPORT

**Task**: Fix Production Logging
**Status**: Complete (Ready for testing)
**Date**: 2026-07-09

### Changes Made
1. ✅ Modified `config/environments/production.rb` — Dual logging (STDOUT + file)
2. ✅ Updated `docker-compose.production.yml` — Added log rotation + verified mount
3. ✅ Updated `.env.production.example` — Documented RAILS_LOG_TO_STDOUT

### Testing Completed
- ✅ Syntax checks passed (ruby -c, docker compose config)
- ✅ Local Stack Car tested — logs in both STDOUT and file (if applicable)
- ⏳ Production VM testing — pending DevOps deployment

### Root Cause Fixed
- ✅ STDOUT now broadcasts to both stdout and file logger
- ✅ File permissions handled by dual logger setup
- ✅ Docker log rotation prevents disk fill
- ✅ Logs persist across container restart

### Remaining Steps
1. Commit changes to git
2. Deploy to hykudev for validation
3. Deploy to production
4. Verify logs appear in `/data/logs/rails/production.log`
5. Close GitHub issue #8 once confirmed
```

---

## Acceptance Criteria
- [ ] Syntax checks pass (ruby -c production.rb, docker compose config)
- [ ] Local Stack Car logs appear in both STDOUT and file (or STDOUT only if file doesn't apply locally)
- [ ] `config/environments/production.rb` implements dual logging
- [ ] `docker-compose.production.yml` has log rotation configured
- [ ] `.env.production.example` documents RAILS_LOG_TO_STDOUT
- [ ] No new errors introduced in Rails startup
- [ ] Ready for DevOps to test on hykudev

---

## Stop Conditions — escalate if:
- Ruby syntax errors in production.rb
- Docker compose YAML errors
- Rails fails to start with new logger config
- Unable to understand dual logging approach (re-read synthesis report)

---

## Commit Instructions

**Step 1: Stage changes**
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack
git add config/environments/production.rb
git add docker-compose.production.yml
git add .env.production.example
```

**Step 2: Commit with descriptive message**
```bash
git commit -m "fix: Implement dual logging (STDOUT + file) for production diagnostics

- Modified config/environments/production.rb to broadcast logs to both STDOUT and file
- Added Docker log rotation to docker-compose.production.yml (100m max, 3 files)
- Clarified RAILS_LOG_TO_STDOUT in .env.production.example

Fixes issue #8: Logs now persist to ./data/logs/rails/production.log and are
captured by Docker for diagnostics when containers fail.

Root cause: RAILS_LOG_TO_STDOUT=true sent logs only to STDOUT, bypassing the
volume mount. Dual logging ensures both destinations are used.
"
```

**Step 3: Push**
```bash
git push origin fix/facet-links-and-hide-type-facet
# Or if on main: git push origin main
```

**Step 4: Create pull request** (if on a branch)
- Link to GitHub issue #8
- Reference the synthesis report findings
- Request review from DevOps

---

## References

- **Synthesis Report**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/2026-07-09-SYNTHESIS-LOGGING-HYKUDEV-INVESTIGATION.md`
- **GitHub Issue**: https://github.com/wvulibraries/wvu_knapsack/issues/8
- **Rails Logger Docs**: https://guides.rubyonrails.org/debugging_rails_applications.html
