---
title: "Seq 4 - PHP Security Audit (OWASP)"
status: backlog
priority: MEDIUM
type: MAINTENANCE
created: 2026-06-19
updated: 2026-06-19
estimated_effort: "2-3 weeks"
sequence: 4
blocked_by:
  - "2026-06-18-HIGHEST-SETUP-PHPUNIT-INTEGRATION" ✅ DONE
  - "2026-06-18-HIGH-FEATURE-LOGIN-TEST-SUITE" ✅ DONE
  - "2026-06-18-MEDIUM-RESEARCH-TEMP-ACCOUNT-PASSWORD-RESET" ✅ DONE (R1)
  - "2026-06-18-HIGH-MAINTENANCE-DEPRECATED-MYSQL-FUNCTIONS" ✅ DONE (Seq 3)
blocks:
  - "Seq 5: MySQL 5.7 → 8.4 LTS Upgrade"
  - "Seq 6: EngineAPI Distillation"
branch: "refactor/authentication-modernization" (shared working branch)
test_coverage: "70 passing tests (Seq 1-3 baseline, expanded in this task)"
---

# Task: PHP Security Audit (OWASP Top 10 Assessment)

## Problem Statement
R1 research identified three critical security issues in Authentication application:

1. **CRITICAL: Plain Text Password Storage** (temp account passwords stored unencrypted in database)
2. **CRITICAL: Missing Password Reset Mechanism** (users cannot reset forgotten passwords)
3. **HIGH: LDAP Bind Sync Unknown** (temp account authentication syncs with WVU-AD but process undocumented)

Additionally, Seq 3 Phase 2 deferred:
- **HIGH: String Interpolation in SQL Queries** (SQL injection vulnerability, EngineAPI 3.2 uses vsprintf instead of prepared statements)

This audit addresses all four issues with fixes and documentation.

## Scope: OWASP Top 10 Focus

**In Scope (This Task)**:
- A1: Injection — SQL injection hardening (Phase 2 from Seq 3)
- A2: Broken Authentication — Password storage & reset mechanism
- A3: Sensitive Data Exposure — LDAP sync investigation
- A5: Broken Access Control — ACL audit (verify no privilege escalation)
- A7: Cross-Site Scripting (XSS) — Review login forms, temp account flows
- A9: Using Components with Known Vulnerabilities — Already handled (Seq 3, EngineAPI 3.2)

**Out of Scope (Document for Future)**:
- Rate limiting implementation (tracked but low-priority for low-usage app)
- Audit logging infrastructure (Seq 7)
- Full OWASP compliance matrix

## Investigation Tasks

### Task 1: Plain Text Password Storage (CRITICAL FIX REQUIRED)

**Current State** (from R1):
```sql
-- Current schema (VULNERABLE)
CREATE TABLE tempAccounts (
  id INT PRIMARY KEY,
  username VARCHAR(255),
  password VARCHAR(12),  -- PLAIN TEXT!
  created_at TIMESTAMP
);
```

**What Needs Investigation**:
1. How are passwords currently set in tempAccounts?
   - File: `src/public_html/authentication/tempAccounts/add.php`
   - Look for: Password input, validation, storage logic
   - Question: Are passwords hashed or stored as-is?

2. Password lifecycle:
   - How long do temp accounts live? (Daily rotation mentioned by strategist)
   - Are old passwords ever deleted?
   - Can users change their own password?

3. LDAP binding with plaintext:
   - Why do temp accounts attempt LDAP bind? (discovered in R1)
   - Does WVU-AD require plaintext password for bind?
   - Or is this a sync/verification mechanism?

**Fix Required**:
Implement bcrypt or Argon2 password hashing. Update schema:
```sql
-- FIXED schema
ALTER TABLE tempAccounts MODIFY COLUMN password VARCHAR(255);  -- Accommodate hash
```

Test expectations:
- ✅ New temp accounts store hashed passwords
- ✅ Hashing uses bcrypt (recommended) or Argon2
- ✅ All 70 tests pass after schema change
- ✅ LDAP bind logic verified (does it need plaintext, or can it use hash?)

### Task 2: Password Reset Mechanism (CRITICAL FEATURE GAP)

**Current State** (from R1):
- File `src/public_html/authentication/tempAccounts/passwordReset.php` referenced in COMPONENTS.md but **DOES NOT EXIST**
- Users cannot reset forgotten passwords
- Admins cannot rotate credentials

**What Needs Investigation**:
1. Is passwordReset.php intentionally missing?
   - Check git history: `git log --all -- "*passwordReset*"`
   - Was it ever implemented?

2. Current temp account flow:
   - User forgets password → what happens?
   - Admin creates new account?
   - Or is reset expected to exist?

3. LDAP implications:
   - Can users use WVU-AD to reset (if temp account is LDAP-synced)?
   - Or is temp account completely separate from WVU-AD?

**Implementation Required**:
Build password reset feature OR document why it's not needed.

Test expectations:
- ✅ Password reset endpoint exists and is secure
- ✅ Token-based reset (time-limited, one-time use)
- ✅ Cannot reset other users' passwords (privilege check)
- ✅ All 70 tests pass + new reset tests added

### Task 3: LDAP Sync Investigation (DOCUMENT & CLARIFY)

**Current State** (from R1):
- Temp account authentication attempts LDAP bind against `wvu-ad.wvu.edu`
- File: `src/public_html/engineIncludes/login-3.0.php` line ~XXX
- Unknown: Why bind if account is local temp account?

**What Needs Investigation**:
1. LDAP bind logic in login-3.0.php:
   - For regular users: Bind to WVU-AD (expected)
   - For temp accounts: Also bind? (investigate why)
   - Look for: Conditional logic based on account type

2. Sync process:
   - Are temp account passwords synced to WVU-AD?
   - Or is bind attempt just verification?
   - What happens if bind fails?

3. Security implications:
   - If plaintext password stored → LDAP bind reveals it (vulnerability)
   - If hashed password stored → How does LDAP bind work?

**Documentation Required**:
- LDAP_SYNC_INVESTIGATION.md explaining:
  - Why temp accounts attempt WVU-AD bind
  - What happens if bind fails
  - Security implications of current design
  - Recommendation: keep as-is OR change approach

### Task 4: String Interpolation → Prepared Statements (Seq 3 Phase 2)

**Current State** (from Seq 3):
- EngineAPI 3.2 migrated from `mysql_*` → `mysqli_*`
- But still uses string interpolation instead of prepared statements
- Example: `vsprintf("SELECT * FROM users WHERE id=%d", $id)` (vulnerable)

**Files Requiring Hardening**:
1. Extracted engineAPI modules (in `src/phpincludes/engineAPI/engine/`)
2. Authentication-specific:
   - `src/phpincludes/databaseConnectors/database.lib.wvu.edu.remote.php`
   - `src/public_html/engineIncludes/login-3.0.php`
   - `src/public_html/authentication/acl.php`
   - `src/public_html/authentication/tempAccounts/*.php`

**Refactoring Pattern**:
```php
// BEFORE (vulnerable)
$query = sprintf("SELECT * FROM users WHERE id=%d AND name='%s'", $id, $name);
$result = $mysqli->query($query);

// AFTER (secure)
$stmt = $mysqli->prepare("SELECT * FROM users WHERE id = ? AND name = ?");
$stmt->bind_param("is", $id, $name);
$stmt->execute();
$result = $stmt->get_result();
```

Test expectations:
- ✅ All string interpolation queries converted to prepared statements
- ✅ Zero SQL injection vulnerabilities in codebase review
- ✅ All 70 tests pass after refactoring
- ✅ Performance verified (prepared statements may slightly improve query reuse)

### Task 5: XSS Audit (Login Forms & Temp Account Flows)

**Current State**:
- CSRF tokens already implemented (verified in Seq 2 tests)
- Need to verify output escaping

**Files to Review**:
1. `src/public_html/authentication/index.php` — Login form
2. `src/public_html/authentication/tempAccounts/index.php` — Temp account management UI
3. Check for:
   - Unescaped user input in HTML (echo $username vs echo htmlspecialchars($username))
   - Hidden form fields (tokens, IDs)
   - JavaScript event handlers from user data

Test expectations:
- ✅ No XSS vulnerabilities in form handling
- ✅ All user input properly escaped
- ✅ CSRF tokens present on all state-changing forms

## Success Criteria

✅ **Plain Text Passwords FIXED**
- [ ] Password hashing implemented (bcrypt/Argon2)
- [ ] Schema updated
- [ ] All 70 tests pass
- [ ] Old passwords migrated or legacy plan documented
- [ ] LDAP bind logic verified works with hashed passwords

✅ **Password Reset Feature**
- [ ] Implementation complete (if needed) OR documented why not needed
- [ ] Secure token-based reset (time-limited, one-time use)
- [ ] All new tests passing
- [ ] All 70 existing tests still passing

✅ **LDAP Sync Documented**
- [ ] doc/LDAP_SYNC_INVESTIGATION.md created (400+ lines)
- [ ] Why temp accounts bind to WVU-AD explained
- [ ] Security implications documented
- [ ] Recommendation provided (keep / change approach)

✅ **SQL Injection Hardening (Phase 2 from Seq 3)**
- [ ] All string interpolation queries converted to prepared statements
- [ ] Security review: zero SQL injection vulnerabilities found
- [ ] All 70 tests pass after refactoring
- [ ] Commits use `refactor: ` prefix

✅ **XSS Audit**
- [ ] Output escaping review complete
- [ ] No XSS vulnerabilities found
- [ ] CSRF token coverage verified
- [ ] Audit documented in SECURITY_AUDIT_SUMMARY.md

✅ **Final Deliverables**
- [ ] doc/SECURITY_AUDIT_SUMMARY.md (comprehensive findings + fixes)
- [ ] doc/LDAP_SYNC_INVESTIGATION.md (LDAP process explained)
- [ ] All commits use conventional commits (`security: `, `refactor: `, `docs: `)
- [ ] Commits pushed to `refactor/authentication-modernization` branch
- [ ] Task file moved to completed/ folder with full summary

## Testing Strategy

Run tests after each security fix:
```bash
cd ~/Documents/git/Authentication
./scripts/run-tests.sh
# Should show 70/70 passing (or higher if new tests added)
```

Add new security-focused tests for:
- Password hashing verification
- Password reset token validation
- SQL injection attempts (should all fail safely)
- XSS payload escaping

## Commit Strategy

Recommended order:
1. `security: Implement bcrypt password hashing for temp accounts`
2. `security: Implement password reset mechanism with token validation`
3. `docs: Document LDAP sync investigation & implications`
4. `refactor: Convert string interpolation to prepared statements (Phase 2)`
5. `security: Audit output escaping, verify XSS protection`
6. `docs: Create SECURITY_AUDIT_SUMMARY.md with all findings`

Each commit must maintain 70/70 tests passing (or note reason for test changes).

## Related Documentation

- **R1 Research**: doc/MYSQL_INSTANCE_DEPENDENCIES.md (identified plain text passwords + missing reset)
- **Seq 3 Phase 2**: doc/ENGINEAPI_3.2_ASSESSMENT.md (string interpolation security gap)
- **COMPONENTS.md**: Lines mentioning passwordReset.php (missing file)
- **TESTING.md**: Existing test patterns for security tests

## Notes

- Application is low-usage, daily-rotation temp accounts → urgency is moderate
- All work on local development environment (Docker)
- No specific production deployment timeline → work at your pace
- Strategist confirmed security audit is fine but no specific upgrade plan
