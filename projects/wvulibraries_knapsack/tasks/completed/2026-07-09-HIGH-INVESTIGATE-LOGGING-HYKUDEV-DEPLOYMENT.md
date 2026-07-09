---
status: completed
priority: HIGH
type: bug-fix
system_domain: WVU_KNAPSACK
mvp_alignment: OPERATIONS_VISIBILITY
local_worker_safe: true\ncompleted_date: 2026-07-09
---

## ⚡ Minimal Handoff (Copy this to send to agent)

```
You are **Research Agent**.

Project: WVU Knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/active/2026-07-09-HIGH-INVESTIGATE-LOGGING-HYKUDEV-DEPLOYMENT.md

STEP 0 — TASK STATUS (already active):
This file is already in the active/ folder. No move needed.
Change this line in the YAML: status: backlog → status: active (if needed)

READ FIRST: Task file contains all prerequisites, research areas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries/ folder BEFORE starting any work.
  Path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/
  Filename: YYYY-MM-DD-SYNTHESIS-LOGGING-HYKUDEV-INVESTIGATION.md
  Do NOT paste in chat — save as file.
```

---

# TASK: Investigate Logging Configuration Differences (HykuDev vs Local Dev vs Production)
**Status**: ACTIVE
**Priority**: HIGH
**Type**: bug-fix
**Created**: 2026-07-09
**Last Updated**: 2026-07-09
**Branch**: `fix/facet-links-and-hide-type-facet` (deployed to hykudev)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/README.md`
   - Understand knapsack structure, Docker setup, environments
2. **Hyrax Build Guide**: `/Users/tam0013/Documents/git/wvu_knapsack/HYKU_BUILD_GUIDE.md`
   - Local dev (Stack Car), HykuDev VM setup, production deployment
3. **This Task File**: Everything below

---

## Context
The `fix/facet-links-and-hide-type-facet` branch was deployed to hykudev (production-like VM) for testing. During testing, **logging configuration differences** were observed between Stack Car local development and the hykudev deployment.

**Current Status**:
- ✅ Production (`admin-hyku.lib.wvu.edu`): Running normally
- ✅ Local dev (Stack Car): Works but uses different logging setup
- ⚠️ HykuDev VM: Logs not appearing in expected location(s)

**Relevant Architecture**:
- `/Users/tam0013/Documents/git/wvu_knapsack/docker-compose.local.yml` — Stack Car local compose
- `/Users/tam0013/Documents/git/wvu_knapsack/docker-compose.production.yml` — Production/HykuDev compose
- `/Users/tam0013/Documents/git/wvu_knapsack/config/environments/` — Rails env configs
- `/Users/tam0013/Documents/git/wvu_knapsack/up.sh` — HykuDev deploy script
- `/Users/tam0013/Documents/git/wvu_knapsack/up.sc.local.sh` — Stack Car script

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start)

Create this report BEFORE running any commands or reading files beyond the task file.

**Save as**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/YYYY-MM-DD-SYNTHESIS-LOGGING-HYKUDEV-INVESTIGATION.md`

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Investigate Logging Configuration Differences (HykuDev vs Local Dev vs Production)
**Status**: Starting Research
**Date**: 2026-07-09

### What I'm About to Do
Research and document logging configuration differences across three environments:
1. Local dev (Stack Car)
2. HykuDev VM (production-like)
3. Production

Then identify root cause of missing logs on hykudev and propose fixes.

### Environments I Will Compare
| Environment | Script | Compose File | Log Expectation |
|---|---|---|---|
| Local dev | `up.sc.local.sh` | `docker-compose.yml` (via Stack Car) | stdout via `sc logs` |
| HykuDev | `up.sh` | `docker-compose.production.yml` | `/data/logs/rails/` |
| Production | `up.sh` | `docker-compose.production.yml` | `/data/logs/rails/` |

### Files I Will Reference
| File | Purpose | Status |
|---|---|---|
| `docker-compose.yml` | Stack Car local config | pending review |
| `docker-compose.production.yml` | HykuDev/prod config | pending review |
| `config/environments/production.rb` | Rails logging config | pending review |
| `config/environments/development.rb` | Local logging config | pending review |
| `HYKU_BUILD_GUIDE.md` | Deployment documentation | pending review |

### Prerequisites Completed
- ✅ Read this task file
- ✅ Understand knapsack structure from project README
- ✅ Know where compose files are located
- ✅ Know HykuDev is the testing environment where issue occurs

### Critical Research Areas
1. **Volume Mounts**: How are logs directories mounted in each compose file?
2. **Rails Logger**: Is logging to stdout or to file? How is it configured per environment?
3. **Log Drivers**: What Docker log drivers are configured?
4. **Setup Scripts**: What does `up.sh` vs `up.sc.local.sh` do differently?

### Expected Synthesis Output
- Document each environment's logging configuration
- Compare compose file volume mounts
- Compare Rails environment configs
- Identify which differences cause logs to disappear on hykudev
- Propose fix (compose changes, Rails config changes, or both)

---

**SYNTHESIS COMPLETE.** Ready to proceed with research.
```

---

## Problem Statement
**What is wrong**: Logs are not appearing in expected locations on hykudev deployment, making debugging difficult.

**Current behavior**:
- Stack Car: Logs visible via `sc logs web -f` (container stdout)
- HykuDev: Logs not found at `/data/logs/rails/` or not appearing in `docker logs`

**Expected behavior**:
- HykuDev and Production: Logs written to `/data/logs/rails/` directory
- Logs accessible for debugging production-like issues

---

## Files Involved (Research — Read Only)

| File | Purpose | Key Areas to Check |
|---|---|---|
| `docker-compose.yml` | Stack Car compose file | volumes, log_driver sections |
| `docker-compose.production.yml` | Production/HykuDev compose | volumes, log_driver sections |
| `config/environments/production.rb` | Rails production logger config | logger config, log level |
| `config/environments/development.rb` | Rails development logger config | logger config, log level |
| `up.sh` | HykuDev deploy script | environment variables, docker compose commands |
| `up.sc.local.sh` | Stack Car script | differences from up.sh |
| `HYKU_BUILD_GUIDE.md` | Hyku setup documentation | logging expectations |

---

## Implementation Steps (Research Only)

### Step 1 — Compare Compose Volume Mounts
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Show volume mounts in local compose
echo "=== LOCAL COMPOSE (docker-compose.yml) ==="
grep -A10 "^volumes:" docker-compose.yml | head -20

# Show volume mounts in production compose
echo "=== PRODUCTION COMPOSE (docker-compose.production.yml) ==="
grep -A10 "^volumes:" docker-compose.production.yml | head -20

# Look for log-specific volumes
echo "=== LOG VOLUMES LOCAL ==="
grep -i "logs\|data" docker-compose.yml | grep "volumes\|:"

echo "=== LOG VOLUMES PRODUCTION ==="
grep -i "logs\|data" docker-compose.production.yml | grep "volumes\|:"
```

### Step 2 — Compare Web Service Volume Mounts
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Local dev web service
echo "=== WEB SERVICE LOCAL ==="
grep -A30 "^  web:" docker-compose.yml | grep -A20 "volumes:"

# Production web service
echo "=== WEB SERVICE PRODUCTION ==="
grep -A30 "^  web:" docker-compose.production.yml | grep -A20 "volumes:"
```

### Step 3 — Check Rails Logger Configuration
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Production Rails config
echo "=== PRODUCTION RAILS LOGGER ==="
grep -n "logger\|log_level\|log_to_stdout" config/environments/production.rb

# Development Rails config
echo "=== DEVELOPMENT RAILS LOGGER ==="
grep -n "logger\|log_level\|log_to_stdout" config/environments/development.rb
```

### Step 4 — Document Setup Script Differences
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# Compare startup scripts
echo "=== DIFFERENCES: up.sh vs up.sc.local.sh ==="
diff -u <(cat up.sh) <(cat up.sc.local.sh) | head -50
```

### Step 5 — Check Data Directory Structure
```bash
# Local dev — what actually gets created
ls -la data/logs/ 2>/dev/null || echo "No /data/logs/ in local dev"

# Note: HykuDev is on VM — you may need to SSH to check
# If you have access: ssh hykudev 'ls -la /data/logs/'
```

### Step 6 — Document Your Findings
Synthesize all findings into the report. Document:
1. **What volumes are mounted** in each environment for logs
2. **Where Rails is configured to write logs** in each environment
3. **Why logs disappear** on hykudev (missing mount? different Rails config? both?)
4. **What fix is needed** to solve the issue

---

## Acceptance Criteria
- [ ] Synthesis report created and saved to `summaries/` folder (not in chat)
- [ ] Compose file volume mounts documented and compared
- [ ] Rails logger configuration documented for each environment
- [ ] Root cause identified (e.g., "missing volume mount in production compose", "Rails logger disabled in production", etc.)
- [ ] Proposed fix documented with exact files/lines to change
- [ ] No implementation changes made (research only for this task)

---

## Stop Conditions — escalate if:
- Cannot access hykudev VM to verify current logging state
- Compose files or Rails config cannot be found
- Documentation (HYKU_BUILD_GUIDE.md) is outdated or incomplete
- Root cause involves undocumented environment variables

---

## Commit Instructions
**No commits for this task** — synthesis report only.
After synthesis report is approved, a separate implementation task will be created.
