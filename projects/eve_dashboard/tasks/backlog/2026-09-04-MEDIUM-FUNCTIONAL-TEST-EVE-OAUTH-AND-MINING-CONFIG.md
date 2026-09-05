---
status: backlog
priority: MEDIUM
type: feature
system_domain: EVE_ONLINE_INTEGRATION
mvp_alignment: PLEX_FUND_OPTIMIZATION
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [x] All Step 0-N instructions are clear and actionable (not vague)
- [x] Synthesis report template is provided (copy/paste ready, not as example)
- [x] No placeholder text remains in Implementation Steps
- [x] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [x] Acceptance Criteria are measurable
- [x] OAuth credentials provided by user (Client ID + Secret)
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY for dispatch.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Functional Testing Agent** for Eve Dashboard Phase 2 OAuth Integration.

Project: eve_dashboard
Task: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/eve_dashboard/tasks/backlog/2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md \
         projects/eve_dashboard/tasks/active/2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/
  Filename pattern: 2026-09-04-FUNCTIONAL-TEST-SYNTHESIS.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: Eve Dashboard Phase 2 — OAuth Integration & Functional Testing

**Status**: BACKLOG (will become ACTIVE after Step 0)
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-09-04
**Last Updated**: 2026-09-04

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen (local via Copilot custom agent config)
**Why This Agent**: Local Qwen has terminal access needed for OAuth testing, Docker container management, and ESI API verification
**Supervision Level**: Watched carefully (first dispatch of Phase 2)

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Eve Dashboard README**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/README.md`
2. **Project Status**: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/status.md`
3. **This Task File**: Everything below

---

## Handoff from Phase 1

**What was completed in Phase 1:**

✅ **Logging Infrastructure** (app/logging_config.py created)
- Centralized logging with rotating file handlers (10MB size, 5 backups)
- Replaced 72+ bare `except:` clauses with proper `logger.exception()` calls
- All errors now logged to `data/logs/dashboard.log` with full context and traceback
- Module-specific loggers suppress verbose third-party library noise

✅ **Thread Safety** (app/config.py refactored)
- Settings class protected with `threading.RLock()` against concurrent FastAPI requests
- Implemented mtime-based caching to eliminate redundant credential file I/O on every request
- Race condition eliminated: credential reloads now safe in multi-threaded environment

✅ **Input Validation** (app/main.py updated)
- `_validate_character_ids()` function filters fleet operations against `db.list_characters()`
- Users can only access their own authorized characters
- Security boundary enforced at API layer

✅ **Error Standardization** (app/main.py updated)
- `_error_response()` helper returns consistent `{"error": message, "status": code}` format
- All endpoints now return predictable error JSON
- Frontend can parse errors uniformly

✅ **Request Size Limits** (app/main.py updated)
- `MAX_UPLOAD_SIZE = 10 * 1024 * 1024` (10MB) prevents DoS via file uploads
- File size validated in `pi_board_import()` before JSON parsing

✅ **Docker & Raspberry Pi Deployment** (NEW - Dockerfile, docker-compose.yml, systemd service)
- Python 3.11-slim base image
- Multi-architecture support (tested on Pi 4)
- Health checks every 60s via curl on port 8765
- Resource limits: 2 CPUs, 512MB RAM (tunable for Raspberry Pi)
- Systemd service for 24/7 operation with auto-restart

✅ **Code Quality Validation** (test-quality.sh - 27 tests passing)
- Syntax validation (22 Python files)
- Module import checks (logging_config integration verified)
- Required files present (credentials template, docker configs)
- Security features audited (4 implemented features)

**Current State:**
- Docker image builds successfully (~11.6 seconds)
- Container starts and health checks pass
- All Python syntax valid
- Logging initialized on app startup
- Ready for OAuth testing

**Why Phase 2 is next:**
Phase 1 built the operational foundation. Phase 2 validates the entire system with real EVE Online accounts and live ESI API connectivity. Must complete Phase 2 before Phases 3-6 can proceed.

---

## Context

Eve Dashboard is a multi-account mining operation management system for EVE Online. Phase 1 (logging, thread-safety, input validation, security) is complete. Phase 2 validates the entire system with real EVE Online accounts using OAuth authentication.

**User's Operation**:
- 7 EVE Online accounts (4 Omega premium, 3 Alpha free-to-play)
- 11 total characters (7 main Neon-named pilots, 4 support alts)
- Primary: Ice + ore mining fleet (4 Orca + Hulks) → refined locally → hauled to Jita for sale
- Goal: Accumulate ISK to purchase PLEX and upgrade Alpha accounts to Omega status

**OAuth Credentials (SEEDED — already registered)**:
- Client ID: `037bf01a1d25458099784221ef52a1cd`
- Client Secret: `eat_2IHoK4iZ3FKSiNnBWMG5hN6exSmCufpMw_2JFWAa`
- Scopes: esi-industry, esi-wallet, esi-assets, esi-markets, esi-characters, esi-universe
- Redirect URI: `http://localhost:8765/callback`

---

## Critical Information for This Task

### OAuth Credentials (PROVIDED)

| Field | Value | Notes |
|-------|-------|-------|
| Client ID | `037bf01a1d25458099784221ef52a1cd` | From developers.eveonline.com (Neon Red account registered) |
| Client Secret | `eat_2IHoK4iZ3FKSiNnBWMG5hN6exSmCufpMw_2JFWAa` | ESI OAuth token for multi-account auth |
| Redirect URI | `http://localhost:8765/callback` | Dashboard callback endpoint on port 8765 |
| Scopes | 8 scopes selected | mining, wallet, assets, markets, characters, universe (see full list in Implementation) |

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: Multi-Account OAuth Flow**
- ❌ Wrong: Try to authenticate all 11 characters at once in a single OAuth flow
- ✅ Right: OAuth flow works per-account (one login → one account's characters). User must repeat OAuth login for each of their 7 accounts
- Why: EVE Online SSO authenticates accounts, not individual characters. Once one account is authorized, all its characters are accessible via ESI.

⚠️ **GOTCHA 2: ESI Rate Limiting**
- ❌ Wrong: Hammer ESI endpoints rapidly during testing (will be rate-limited after ~150 requests/min)
- ✅ Right: Add delays between ESI queries during testing, check response headers for X-Esi-Error-Limit-Remain
- Why: CCP enforces rate limits. Hitting them blocks further API calls for 60 seconds.

⚠️ **GOTCHA 3: Alpha Account Limitations**
- ❌ Wrong: Assume all 11 characters can access all ESI endpoints equally
- ✅ Right: Alpha accounts have restricted ESI scope access (mining ledger available, but some other data may be limited)
- Why: Omega and Alpha have different API capabilities. Test both account types to verify data retrieval.

⚠️ **GOTCHA 4: SQLite WAL Mode Concurrency**
- ❌ Wrong: Stop the app between tests and wipe the database
- ✅ Right: Keep data persistent across test runs, verify WAL mode handles concurrent writes from FastAPI + background sync
- Why: Database must support multi-threaded access. Testing concurrency is critical to Phase 2 success.

⚠️ **GOTCHA 5: Homefronts Code Preservation**
- ❌ Wrong: Remove or disable homefront tracking code during testing
- ✅ Right: Keep homefront code intact, test it alongside new mining config
- Why: Original author's homefront feature must remain functional. This tests backward compatibility.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before running any commands or modifying any files, post a **synthesis report** in chat. This demonstrates you understand the task.

**Synthesis Report Template**:
```markdown
## STATUS SYNTHESIS REPORT — Phase 2 OAuth Integration

**Task**: Eve Dashboard Phase 2 Functional Testing
**Status**: backlog → active (after Step 0)
**Date**: 2026-09-04

### What I'm About to Do
1. Move this task file from backlog/ to active/ (Step 0)
2. Create config/credentials.env with OAuth credentials provided
3. Start the Eve Dashboard application (via python run.py or docker)
4. Authorize each of the 7 accounts via OAuth flow (manual login x7)
5. Verify all 11 characters load in the database
6. Validate wealth/assets/mining data displays correctly
7. Check for exceptions in logs
8. Document success/failures in summaries file

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/main.py` | FastAPI routes, OAuth callback handler | ✓ exists |
| `app/config.py` | Settings with OAuth credentials | ✓ exists, thread-safe |
| `app/sso.py` | ESI OAuth token management | ✓ exists |
| `app/db.py` | Database initialization + character storage | ✓ exists |
| `config/credentials.env.example` | Template (will copy to credentials.env) | ✓ exists |
| `data/logs/dashboard.log` | Error logging output | ✓ created by app |
| `requirements.txt` | Python dependencies | ✓ exists |
| `Dockerfile` | Container specification | ✓ exists |

### Acceptance Criteria I'm Verifying
- [ ] OAuth application registered successfully
- [ ] credentials.env created with provided Client ID + Secret
- [ ] App starts without errors (python run.py or docker run)
- [ ] OAuth callback endpoint accessible (GET /callback receives auth code)
- [ ] User can log in with Neon Red account via EVE SSO
- [ ] After login, Neon Red's characters appear in database
- [ ] ESI queries succeed (mining ledger, assets, wallet data retrieves)
- [ ] All 11 characters can be authorized (loop through 7 accounts)
- [ ] Dashboard displays wealth/assets/mining data for all characters
- [ ] No exceptions logged (data/logs/dashboard.log is clean or only warnings)
- [ ] Homefront tracking code still works (test homefront endpoint)
- [ ] SQLite database persists data across app restarts
- [ ] Integration synthesis report completed and saved to summaries/

### Dependencies Verified
- Python 3.11 environment ready
- pip dependencies installed (FastAPI, httpx, etc.)
- Docker available (if running in container)
- Internet access to developers.eveonline.com + esi.evetech.net
- Port 8765 available locally

### Known Blockers / Gotchas I'll Watch For
1. ESI rate limiting (add delays, monitor headers)
2. Alpha vs Omega scope differences (test both)
3. OAuth flow is per-account (not per-character)
4. Homefront code must remain intact
5. WAL mode concurrency (keep app running, test persistence)
```

**DO NOT START WORK UNTIL SYNTHESIS IS POSTED.**

---

## Implementation Steps

### Step 0: Move Task File (REQUIRED FIRST)
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git mv projects/eve_dashboard/tasks/backlog/2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md \
       projects/eve_dashboard/tasks/active/2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md
# Edit the file: change status: backlog → status: active
git add projects/eve_dashboard/tasks/active/2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md
git commit -m "Phase 2 OAuth testing: move task to active"
```
**Verify**: `find /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks -name "2026-09-04-MEDIUM-FUNCTIONAL-TEST-EVE-OAUTH-AND-MINING-CONFIG.md"` should return only ONE result (in active/ folder)

### Step 1: Create credentials.env from Template
```bash
cd /Users/tam0013/Documents/git/eve-dashboard
cp config/credentials.env.example config/credentials.env
```

Edit `config/credentials.env`:
```ini
ESI_CLIENT_ID=037bf01a1d25458099784221ef52a1cd
ESI_CLIENT_SECRET=eat_2IHoK4iZ3FKSiNnBWMG5hN6exSmCufpMw_2JFWAa
ESI_CALLBACK_URL=http://localhost:8765/callback
```

**Verify**:
```bash
grep -E "ESI_CLIENT_ID|ESI_CLIENT_SECRET" /Users/tam0013/Documents/git/eve-dashboard/config/credentials.env
# Should show your credentials (masked in logs)
```

### Step 2: Verify Python Environment
```bash
cd /Users/tam0013/Documents/git/eve-dashboard
python3 --version  # Should be 3.11 or higher
python3 -m pip list | grep -E "fastapi|httpx|uvicorn"  # Verify key packages
```

### Step 3: Start Application (Local Mode)
```bash
cd /Users/tam0013/Documents/git/eve-dashboard
python3 run.py
```

**Expected output**:
```
[INFO] Eve Dashboard initialized
[INFO] Uvicorn running on http://localhost:8765
[INFO] ESI Configuration loaded
[INFO] Database initialized (WAL mode enabled)
[INFO] Background sync scheduler started
```

**If errors occur**, check:
- Port 8765 already in use: `lsof -i :8765`
- Database locked: `rm data/dashboard.db data/dashboard.db-wal data/dashboard.db-shm` (clears DB)
- Config file missing: `ls -la config/credentials.env`

### Step 4: Test OAuth Flow (Manual Login)

**For each of your 7 accounts (do this sequentially)**:

1. Open browser: `http://localhost:8765`
2. Click "Log In" or "Add Account"
3. Browser redirects to EVE SSO login
4. Log in with one account (e.g., Neon Red)
5. EVE SSO asks for permission (shows requested scopes)
6. Click "Accept"
7. Browser redirects back to dashboard at `/callback?code=...&state=...`
8. Dashboard exchanges auth code for access token
9. Dashboard stores access token in database
10. Dashboard fetches characters for that account via ESI
11. Browser redirected to dashboard home or character list

**Repeat for all 7 accounts** (Neon Red, other Neon pilots, support alts). Each login adds that account's characters to the database.

### Step 5: Verify Database Population

**After all 7 accounts are logged in**, query the database:

```bash
cd /Users/tam0013/Documents/git/eve-dashboard
python3 << 'EOF'
import sqlite3
conn = sqlite3.connect('data/dashboard.db')
c = conn.cursor()

# Count characters
c.execute('SELECT COUNT(*) FROM characters WHERE active = 1')
total = c.fetchone()[0]
print(f"Total active characters: {total} (expected 11)")

# List all characters
c.execute('SELECT name, account_id FROM characters WHERE active = 1 ORDER BY name')
for name, account_id in c.fetchall():
    print(f"  - {name} (account {account_id})")

# Verify Neon pilots
c.execute('SELECT COUNT(*) FROM characters WHERE name LIKE "Neon%" AND active = 1')
neon_count = c.fetchone()[0]
print(f"\nNeon-named characters: {neon_count} (expected 7)")

conn.close()
EOF
```

**Expected output**:
```
Total active characters: 11 (expected 11)
  - Neon Red (account ...)
  - Neon Blue (account ...)
  - Neon Greene (account ...)
  ... (7 more Neon pilots + 4 support alts)

Neon-named characters: 7 (expected 7)
```

### Step 6: Test ESI Data Retrieval

**In another terminal** (while app is still running):

```bash
# Test mining ledger for Neon Red
curl -s "http://localhost:8765/api/mining?character_id=<NEON_RED_ID>" \
  -H "Authorization: Bearer <ACCESS_TOKEN>" | python3 -m json.tool

# Test assets for any character
curl -s "http://localhost:8765/api/assets?character_id=<CHAR_ID>" \
  -H "Authorization: Bearer <ACCESS_TOKEN>" | python3 -m json.tool

# Test wallet
curl -s "http://localhost:8765/api/wallet?character_id=<CHAR_ID>" \
  -H "Authorization: Bearer <ACCESS_TOKEN>" | python3 -m json.tool
```

**Expected**: JSON responses with character data (mining ore, assets held, ISK wallet value)

### Step 7: Check Logs for Errors

```bash
# View application logs
tail -50 data/logs/dashboard.log

# Check for exceptions
grep -i "exception\|error\|traceback" data/logs/dashboard.log

# Should show: successful logins, ESI queries, character data storage
# Should NOT show: unhandled exceptions, 401/403 auth errors
```

### Step 8: Test Homefront Tracking (Backward Compatibility)

```bash
# Verify homefront code still works
curl -s "http://localhost:8765/homefronts" | grep -c "homefront"
# Should return HTML with homefront content (non-zero count)
```

### Step 9: Test WAL Mode Persistence

```bash
# Stop app (Ctrl+C in terminal where it's running)
# In the now-stopped app terminal: press Ctrl+C

# Check database files
ls -la data/dashboard.db*
# Should see: dashboard.db, dashboard.db-wal, dashboard.db-shm

# Restart app
python3 run.py

# Verify data persisted
python3 << 'EOF'
import sqlite3
conn = sqlite3.connect('data/dashboard.db')
c = conn.cursor()
c.execute('SELECT COUNT(*) FROM characters WHERE active = 1')
print(f"Characters still in DB after restart: {c.fetchone()[0]}")
conn.close()
EOF
# Should show 11 (data persisted)
```

### Step 10: Test with Docker (Optional)

If you want to verify Docker also works:

```bash
cd /Users/tam0013/Documents/git/eve-dashboard
docker build -t eve-dashboard:latest .
docker run -p 8765:8765 \
  -v $(pwd)/config:/app/config \
  -v $(pwd)/data:/app/data \
  eve-dashboard:latest
# Should start successfully, same as python run.py
```

---

## Acceptance Criteria

**Task SUCCEEDS if ALL of these are true:**

- [ ] OAuth application created at developers.eveonline.com with Client ID + Secret provided
- [ ] config/credentials.env created with correct OAuth credentials
- [ ] Application starts without errors: `python3 run.py` succeeds
- [ ] OAuth callback endpoint works: `/callback` receives auth code
- [ ] All 7 accounts can be authorized via EVE SSO
- [ ] All 11 characters appear in database after authorization
- [ ] ESI queries return data: mining ledger, assets, wallet
- [ ] Homefront tracking code still works (endpoint responds)
- [ ] SQLite database persists across app restart (WAL mode validated)
- [ ] No exceptions in logs (data/logs/dashboard.log is clean)
- [ ] Wealth/assets/mining pages display data correctly
- [ ] Synthesis report saved to: `summaries/2026-09-04-FUNCTIONAL-TEST-SYNTHESIS.md`
- [ ] All changes committed to git with clear message

**Task FAILS if ANY of these are true:**
- OAuth flow returns 401/403 errors
- Characters fail to load in database
- ESI queries return errors or timeout
- Homefront code is broken or removed
- Exceptions appear in logs
- Database doesn't persist after restart

---

## Known Limitations / Future Work

This Phase 2 testing does NOT include:

- [ ] Rate-limit retry logic (Phase 3B task)
- [ ] Mining ledger aggregation (Phase 3 task)
- [ ] Price tracking (Phase 3B task)
- [ ] Logistics optimization (Phase 4.5 task)
- [ ] Production efficiency metrics (Phase 5 task)
- [ ] Raspberry Pi deployment (after Phase 2 passes)

These are in separate tasks. Phase 2 is ONLY about validating OAuth + ESI connectivity with real data.

---

## Architecture Notes

**What's being tested**:
- ✅ OAuth authentication flow (SSO → token → character list)
- ✅ ESI API integration (ESI scopes, rate limits, token management)
- ✅ Multi-account support (7 separate OAuth flows, 11 total characters)
- ✅ Database persistence (SQLite WAL mode under concurrent load)
- ✅ Backward compatibility (homefront code still works)
- ✅ Error logging (all errors captured with context)

**What's NOT being tested** (Phase 3+):
- Mining ledger aggregation, price tracking, efficiency metrics
- Dashboard UI rendering (manual visual inspection only)
- Raspberry Pi deployment specifics
- Docker-compose full stack (just docker build/run basics)

---

## Synthesis Report Output

After completing all steps, save this template to: `/Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/2026-09-04-FUNCTIONAL-TEST-SYNTHESIS.md`

```markdown
# SYNTHESIS REPORT — Phase 2 OAuth Integration & Testing

**Task**: Eve Dashboard Phase 2 Functional Testing
**Date**: 2026-09-04
**Status**: ✅ PASSED | ❌ FAILED

## Summary
[2-3 sentence summary of what was tested and result]

## Test Results

### OAuth & Authentication
- [✅/❌] OAuth app created at developers.eveonline.com
- [✅/❌] credentials.env populated with Client ID + Secret
- [✅/❌] ESI application configured with correct scopes (8 scopes)
- [✅/❌] OAuth callback endpoint responds to auth code

### Character Authorization
- [✅/❌] Neon Red (main Omega account) authorized
- [✅/❌] Other Omega accounts authorized (4 total)
- [✅/❌] Alpha accounts authorized (3 total)
- [✅/❌] All 7 accounts logged in without errors
- [✅/❌] All 11 characters stored in database

### ESI Data Retrieval
- [✅/❌] Mining ledger queries successful (esi-industry scope)
- [✅/❌] Asset queries successful (esi-assets scope)
- [✅/❌] Wallet queries successful (esi-wallet scope)
- [✅/❌] Market order queries successful (esi-markets scope)
- [✅/❌] Character info queries successful (esi-characters scope)
- [✅/❌] Rate limiting observed and handled correctly

### Database & Persistence
- [✅/❌] SQLite database initialized with WAL mode
- [✅/❌] Characters stored correctly (name, ID, account ID)
- [✅/❌] Data persisted across app restart
- [✅/❌] No database locks or corruption

### Backward Compatibility
- [✅/❌] Homefront tracking code still present
- [✅/❌] Homefront endpoints respond
- [✅/❌] Original functionality NOT broken

### Application Logs
- [✅/❌] No unhandled exceptions in logs
- [✅/❌] All OAuth flows logged with timestamps
- [✅/❌] ESI queries logged with results
- [✅/❌] Background sync scheduler working

### Dashboard UI
- [✅/❌] Home page loads
- [✅/❌] Character list displays all 11 characters
- [✅/❌] Wealth page shows ISK totals
- [✅/❌] Assets page shows inventory
- [✅/❌] No JavaScript errors in console

## Metrics

| Metric | Value | Status |
|--------|-------|--------|
| Total characters authorized | 11 / 11 | ✅ |
| Accounts logged in | 7 / 7 | ✅ |
| ESI queries successful | ??? / ??? | ✅ / ❌ |
| Exceptions in logs | 0 | ✅ |
| Database size | ??? MB | ✅ |
| Average ESI latency | ??? ms | ✅ |
| Homefront endpoints working | 2 / 2 | ✅ |

## Issues Encountered

### Issue 1: [Description]
- **Severity**: CRITICAL | HIGH | MEDIUM | LOW
- **Root Cause**: [explanation]
- **Resolution**: [what was done to fix]
- **Verification**: [how it was tested after fix]

[Repeat for each issue found]

## What's Ready for Phase 3

✅ Multi-account OAuth authentication validated
✅ ESI API connectivity confirmed
✅ Database persistence working
✅ Homefront code preserved
✅ Error logging functional
✅ All 11 characters accessible via dashboard

**Next: Dispatch Phase 3 (Mining + Market Sales Tracking)**

## Recommendations

1. [Action for next phase or improvement for current]
2. [Action for next phase or improvement for current]
3. [Action for next phase or improvement for current]

---
**Report generated by**: [Agent name]
**Commit hash**: [git rev-parse --short HEAD]
**Timestamp**: YYYY-MM-DD HH:MM:SS UTC
```

---

## Notes for Agent

- Keep the dashboard running in one terminal during entire test
- Use separate terminal for curl/database queries
- Homefront code is CRITICAL to preserve (test it, don't remove it)
- Each of 7 account logins is manual (no bulk automation)
- After Phase 2 PASSES, Phase 3 (Mining + Market Sales) can be dispatched
- Rate limiting will happen if you query ESI too fast (add 1-2 second delays)
- ESI errors are often rate-limiting or auth token expiration — check X-Esi-Error-Limit-Remain header
