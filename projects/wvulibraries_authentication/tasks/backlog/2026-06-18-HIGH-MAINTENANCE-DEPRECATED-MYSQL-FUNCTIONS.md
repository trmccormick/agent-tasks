---
title: "Deprecated MySQL Function Migration"
status: backlog
priority: HIGH
type: MAINTENANCE
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "3-4 weeks"
sequence: 3
blocked_by:
  - "2026-06-18-HIGHEST-SETUP-PHPUNIT-INTEGRATION" ✅ DONE
  - "2026-06-18-HIGH-FEATURE-LOGIN-TEST-SUITE" ✅ DONE
  - "2026-06-18-MEDIUM-RESEARCH-TEMP-ACCOUNT-PASSWORD-RESET" ✅ DONE (R1)
blocks:
  - "2026-06-18-MEDIUM-MAINTENANCE-SECURITY-AUDIT"
  - "2026-06-18-MEDIUM-MAINTENANCE-MYSQL-5.7-TO-8.0"
branch: "refactor/authentication-modernization" (shared working branch)
test_coverage: "35 passing tests (MockLDAPServer + BaseTestCase infrastructure ready)"
---

# Task: Deprecated MySQL Function Audit and Migration

## Problem
The Authentication application contains 12 PHP files using deprecated `mysql_*` functions (mysql_query, mysql_fetch_array, mysql_escape_string, etc.). These functions were removed in PHP 7.0 and later versions. While the application currently runs on PHP 8.3, this creates:

- **Critical compatibility risk**: PHP 9.0+ will not support these functions at all
- **Security vulnerability**: mysql_* functions lack prepared statement support (SQL injection risk)
- **Maintenance burden**: Any code using these functions is difficult to audit and extend
- **Performance impact**: No query optimization or connection pooling available

**R1 Research Finding**: Zero external dependencies, SAFE to refactor (no other WVU Libraries apps share this MySQL instance)

## Files Affected (12 total with mysql_* calls)
1. `src/phpincludes/databaseConnectors/database.lib.wvu.edu.remote.php` — Core DB connector (START HERE)
2. `src/public_html/engineIncludes/login-3.0.php` — LDAP/login logic (HIGH IMPACT)
3. `src/public_html/authentication/acl.php` — Permission loading (CRITICAL)
4. `src/public_html/authentication/tempAccounts/add.php` — Temp account creation
5. `src/public_html/authentication/tempAccounts/index.php` — Temp account management
6. `src/public_html/authentication/tempAccounts/edit.php` — Temp account editing
7. `src/public_html/authentication/tempAccounts/passwordReset.php` — Password reset (if used)
8. `src/public_html/engineIncludes/emailSendBulk.php` — Email bulk operations
9-12. EngineAPI engine modules (see details in COMPONENTS.md)

## Investigation Starting Points

**Find all mysql_* calls**:
```bash
cd ~/Documents/git/Authentication
grep -r "mysql_" src/public_html/ src/phpincludes/ | grep -v "\.git" | wc -l
# Count should decrease to ZERO as you refactor
```

**Check database connector setup**:
```bash
grep -r "\$mysqli\|\$db\|\$engineDB" src/phpincludes/databaseConnectors/
# Look for how database connection is stored/managed
```

**Manual verification after refactoring**:
```bash
php -l src/public_html/engineIncludes/login-3.0.php
# Should return: No syntax errors detected
```

## Commit Strategy

**Recommended order** (dependent refactoring):
1. Database connector first (`database.lib.wvu.edu.remote.php`) — foundation for all others
2. Login module (`login-3.0.php`) — highest impact code path
3. Permission/ACL module (`acl.php`) — critical for security
4. Temp account modules (`tempAccounts/*`)
5. Email and utility modules (lower priority)

**Commit naming**:
```bash
git commit -m "refactor: Migrate database connector to mysqli prepared statements"
git commit -m "refactor: Replace mysql_* calls with mysqli in login-3.0.php"
git commit -m "refactor: Update acl.php permission loading for mysqli"
# Continue with descriptive refactor commits...
```

**After each commit**: Run `./scripts/run-tests.sh` to verify 35/35 passing.

## Success Criteria
- [ ] All 12 files converted to mysqli prepared statements
- [ ] Zero `mysql_*` function calls remain (`grep -r "mysql_" src/` returns nothing)
- [ ] All 35 tests pass after each commit (`./scripts/run-tests.sh` = 35/35)
- [ ] Application functional at http://localhost:8080/authentication/
- [ ] All commits use `refactor: ` prefix (conventional commits)
- [ ] Commits pushed to `refactor/authentication-modernization` branch
- [ ] Task file moved to completed/ folder with full summary

## Testing Strategy
Run tests after each file or logical group:
```bash
cd ~/Documents/git/Authentication
./scripts/run-tests.sh
# ✅ Should show 35/35 passing (20 Seq 1 + 15 Seq 2)
```

Stop and debug if tests fall below 35. All code paths have test coverage via MockLDAPServer fixtures.

## Refactoring Pattern

**Before** (deprecated, vulnerable):
```php
$result = mysql_query("SELECT * FROM accountUsernames WHERE username='$username'");
$row = mysql_fetch_assoc($result);
```

**After** (secure, prepared statements):
```php
$stmt = $mysqli->prepare("SELECT * FROM accountUsernames WHERE username = ?");
$stmt->bind_param("s", $username);  // "s" = string type
$stmt->execute();
$result = $stmt->get_result();
$row = $result->fetch_assoc();
```

Key changes:
1. Use `prepare()` with `?` placeholders for parameters
2. Bind values with `bind_param()` (type-safe, prevents SQL injection)
3. Execute prepared statement
4. Fetch results from `get_result()`

## Important R1 Findings (For Context, Not Blocking This Task)

⚠️ **Critical Security Issues Discovered**:

1. **Plain Text Password Storage** (CRITICAL — for Seq 4 priority)
   - Temp account passwords stored unencrypted in database
   - Anyone with DB access can read all credentials directly
   - Must implement bcrypt/Argon2 hashing in Seq 4

2. **Password Reset NOT Implemented** (for Seq 4 investigation)
   - File `tempAccounts/passwordReset.php` referenced in docs but does NOT exist
   - Users cannot reset forgotten passwords
   - Admins cannot rotate credentials

3. **LDAP Bind Mystery** (for Seq 4 investigation)
   - Temp accounts attempt LDAP bind against WVU-AD
   - Unknown synchronization process for password sync

**Bottom line**: These don't affect Seq 3. They're documented here so you're aware. Focus on function migration. Seq 4 will address security gaps.

## Related
- **R1 Research**: doc/MYSQL_INSTANCE_DEPENDENCIES.md (confirms safe to refactor — zero external dependencies)
- EngineAPI assessment (planned: Seq 6 Distillation)
- Security audit (planned: Seq 4, priority: fix plain text password storage)
- Docker modernization (MySQL 5.7 → 8.4, compatibility already verified)

## Notes
- This is core infrastructure work; proceed carefully with testing
- EngineAPI is a shared framework; changes may affect other WVU Libraries applications
- Focus on minimal changes first to reduce regression risk
