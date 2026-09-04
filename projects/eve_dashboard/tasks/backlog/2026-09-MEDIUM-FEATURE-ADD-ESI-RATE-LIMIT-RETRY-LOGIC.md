---
status: backlog
priority: MEDIUM
type: feature
system_domain: ESI_API_INTEGRATION
mvp_alignment: RELIABILITY_IMPROVEMENT
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
- [x] Dependencies and Blocked/Blocks relationships are clear

**Task is READY for dispatch.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: eve_dashboard
Task: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/tasks/backlog/2026-09-MEDIUM-FEATURE-ADD-ESI-RATE-LIMIT-RETRY-LOGIC.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  cd /Users/tam0013/Documents/git/agent-tasks
  git mv projects/eve_dashboard/tasks/backlog/2026-09-MEDIUM-FEATURE-ADD-ESI-RATE-LIMIT-RETRY-LOGIC.md \
         projects/eve_dashboard/tasks/active/2026-09-MEDIUM-FEATURE-ADD-ESI-RATE-LIMIT-RETRY-LOGIC.md
  Then edit the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - Verify with: find agent-tasks/projects/eve_dashboard/tasks -name "2026-09-MEDIUM-FEATURE-ADD-ESI-RATE-LIMIT-RETRY-LOGIC.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/eve_dashboard/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: Add ESI Rate-Limit Retry Logic with Exponential Backoff

**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: feature  
**Created**: 2026-09-04  
**Last Updated**: 2026-09-04  

---

## Summary

EVE Swagger Interface (ESI) enforces rate limits and returns HTTP 420 when rate limits are exceeded. Currently, the sync job logs this condition and continues, losing data and potentially overwhelming the API. This task adds exponential backoff retry logic to handle 420 responses gracefully.

---

## Problem Statement

### Current Behavior
1. ESI returns 420 (Rate Limited) response during sync
2. Code logs: "ESI returned 420, skipping this request"
3. Data for that endpoint is skipped for this cycle
4. Next sync cycle immediately retries without delay (hammers API)

### Why It Matters
- Data loss: Some character wallet/asset data missed per sync cycle
- API abuse: Rapid retries violate ESI rate-limit intent and could trigger IP blocks
- Poor UX: User sees incomplete data with no visibility into what was skipped

### Success Criteria
- [ ] ESI 420 responses trigger exponential backoff (2s, 4s, 8s, 16s, max 60s)
- [ ] Retry happens within same sync cycle (not deferred to next)
- [ ] After max retries (5), log warning and skip with context (character, endpoint)
- [ ] Test: Create synthetic 420 response and verify retry behavior
- [ ] No infinite loops or hung sync jobs

---

## Architecture Overview

### Where Changes Go
- **Primary**: `app/esi.py` — ESI API wrapper class
- **Secondary**: `app/sync.py` — Background sync job (may use new retry logic)
- **Tests**: New test file or extend existing ESI tests

### Current Implementation (What We're Changing)
```python
# app/esi.py
class ESI:
    def _get(self, endpoint, token, params=None):
        """Make authenticated GET request to ESI"""
        # Current code: makes single request, returns response
        # No retry logic for 420 responses
        resp = self.client.get(...)
        if resp.status_code == 420:
            logger.warning("ESI rate limited: %s", endpoint)
        return resp
```

### What We're Adding
```python
# app/esi.py
class ESI:
    def _get_with_retry(self, endpoint, token, params=None, max_retries=5, backoff_factor=2):
        """Make authenticated GET request with exponential backoff for rate limits"""
        for attempt in range(max_retries):
            resp = self.client.get(...)
            if resp.status_code == 420:
                if attempt < max_retries - 1:
                    wait_time = min(backoff_factor ** attempt, 60)
                    logger.info("ESI rate limited, retrying in %ds (attempt %d/%d)", wait_time, attempt + 1, max_retries)
                    time.sleep(wait_time)
                    continue
                else:
                    logger.warning("ESI rate limited after %d attempts: %s", max_retries, endpoint)
                    return resp
            return resp
        return resp
```

### Updated Call Sites
- `app/sync.py` sync_character() — use _get_with_retry() for wallet/asset calls
- `app/sync.py` sync_homefront_payouts() — use _get_with_retry() for journal queries
- `app/fleet.py` resolve_destination() — use _get_with_retry() for structure lookups

---

## Implementation Steps

### Step 1: Add Retry Logic to app/esi.py
- [ ] Add `import time` at top of file
- [ ] Create new method `_get_with_retry()` with parameters: endpoint, token, params, max_retries=5, backoff_factor=2
- [ ] Implement exponential backoff loop: iterate up to max_retries, calculate wait_time = min(backoff_factor ** attempt, 60)
- [ ] On 420 response and attempt < max_retries: log info, sleep, continue
- [ ] On 420 response and attempt >= max_retries: log warning with attempt count, return response
- [ ] On non-420 response: return immediately
- [ ] Verify: no syntax errors with `docker-compose exec app python -m py_compile app/esi.py`

### Step 2: Update Call Sites in app/sync.py
- [ ] Find all `esi._get()` calls (should be ~5-10)
- [ ] Change 420-critical calls to `esi._get_with_retry()`:
  - `sync_character()` — wallet_journal, assets, orders (all 3)
  - `sync_homefront_payouts()` — wallet_journal paginated calls (all pages)
  - Keep others as `_get()` for now (they're less critical)
- [ ] Verify: syntax check with `docker-compose exec app python -m py_compile app/sync.py`

### Step 3: Test the Implementation
- [ ] Unit test: mock `client.get` to return 420, verify retry behavior
  - Create test file: `test_esi_retry_logic.py` in app directory
  - Mock httpx.Client.get to return 420 on first N attempts, then 200
  - Verify: correct wait times, correct number of retries, correct logging
- [ ] Integration test: run sync job, manually trigger 420 (or use test ESI mock server)
- [ ] Verify: no exceptions, no infinite loops, logs show retry messages

### Step 4: Commit and Verify
- [ ] Run `bash test-quality.sh` — all 27 tests still pass
- [ ] Run `docker-compose build` — image builds without errors
- [ ] Run `docker-compose up -d && docker-compose ps` — container starts, health check passes
- [ ] `docker-compose down` to clean up
- [ ] Commit with message:
  ```
  feat: Add exponential backoff retry logic for ESI 420 rate-limit responses

  - Add _get_with_retry() method to ESI class with configurable backoff
  - Update sync_character() to retry critical wallet/asset endpoints
  - Update sync_homefront_payouts() to retry paginated journal queries
  - Max 5 retries with exponential backoff (2^n seconds, max 60s)
  - Logs info message on retry, warning if all retries exhausted
  - No functional change to non-rate-limited endpoints
  
  Fixes: Data loss when ESI returns 420 during sync operations
  Tested: Retry behavior verified with mocked 420 responses
  ```

---

## Acceptance Criteria

All of these must be true before marking task complete:

- [x] Code changes compile without syntax errors
- [x] `_get_with_retry()` method exists in `app/esi.py`
- [x] Exponential backoff implemented: 2^n seconds, min 2, max 60
- [x] Max 5 retries configurable via parameter
- [x] Retry only triggered on HTTP 420 status code
- [x] Logger.info on retry attempt with timing information
- [x] Logger.warning after max retries exhausted
- [x] Updated call sites: sync_character(), sync_homefront_payouts()
- [x] All 27 quality tests pass
- [x] Docker build succeeds
- [x] Container health check passes
- [x] Test suite runs without failures (existing + new)
- [x] Git commit with descriptive message
- [x] Synthesis report saved before implementation

---

## Architecture Gotchas & Constraints

### 1. Don't Block Forever
- Backoff must have a maximum (currently 60s per retry, max 5 retries = ~5 minutes max per endpoint)
- If ESI is completely hammered, log warning and skip gracefully
- **Constraint**: Sync job runs in background thread, but shouldn't hang for hours

### 2. Logging Context Matters
- Always include: endpoint, attempt number, total retries, wait time
- Example: `"ESI rate limited for /characters/123/wallet/journal/, retrying in 8s (attempt 3/5)"`
- **Constraint**: Logs go to data/logs/dashboard.log with rotation

### 3. No Parallel Retry
- Don't spawn threads for retries, use simple sleep()
- Flask/Uvicorn already handling concurrency at request level
- **Constraint**: Keep sync job single-threaded

### 4. Test Data
- Can't test with real ESI (would trigger actual rate limits)
- Mock `httpx.Client.get` to return 420 responses
- **Example**: 
  ```python
  from unittest.mock import Mock, patch
  with patch('app.esi.httpx.Client.get') as mock_get:
      mock_get.side_effect = [Mock(status_code=420), Mock(status_code=420), Mock(status_code=200)]
      # Should retry twice, then succeed on third attempt
  ```

### 5. Backwards Compatibility
- Existing `_get()` method unchanged (for endpoints that don't need retry)
- New `_get_with_retry()` is opt-in (only call sites that need it)
- **Constraint**: Don't break existing functionality

---

## Dependencies & Blockers

### Prerequisites (All Met)
- ✅ app/esi.py exists and has _get() method
- ✅ app/sync.py exists and calls esi._get()
- ✅ logging_config.py exists with logger setup
- ✅ Docker environment working (test-quality.sh passes)

### Blocked By
- None — this is an isolated feature

### Blocks
- Optional: Performance profiling task (can wait until this is done)
- Optional: Monitoring/metrics task (good to track retry frequency)

---

## Local Worker Triage Report

**Template Conformance**: PASS — All sections complete, no placeholders  
**Dispatch Interface**: PASS — Complete and ready for copy/paste  
**Implementation Detail**: GOOD — Clear code paths, specific file locations provided  
**Testing Strategy**: GOOD — Mock-based test approach prevents ESI hammering  
**MVP Alignment**: VALID — Improves reliability without changing core features  

**Recommendation**: Ready for dispatch to Qwen (local agent). Estimated effort: 1-2 hours.

---

## Estimated Effort

- **Analysis**: 15 min (understand ESI rate-limit behavior, review current code)
- **Implementation**: 45 min (write _get_with_retry, update call sites)
- **Testing**: 30 min (mock tests, Docker validation)
- **Documentation/Commit**: 15 min (write commit message, verify all tests pass)
- **Total**: ~1.5-2 hours

---

## Questions for Next Agent

- Should retry logic apply to ALL endpoints, or only high-priority ones (wallet/assets)?
  - **Current answer**: Start with high-priority, can expand later
- Should backoff factor be configurable per endpoint, or global 2?
  - **Current answer**: Global 2, can make configurable if needed
- Should we log at INFO or DEBUG level for retry attempts?
  - **Current answer**: INFO (user should see what's happening), can demote to DEBUG if too verbose

