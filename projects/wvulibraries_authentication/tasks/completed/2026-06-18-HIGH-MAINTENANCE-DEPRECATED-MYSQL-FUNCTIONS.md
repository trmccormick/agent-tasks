---
title: "Deprecated MySQL Function Migration"
status: active
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

## Problem & Strategy (UPDATED: Option B)
The Authentication application contains deprecated `mysql_*` functions. A separate engineAPI repo (`/Users/tam0013/Documents/git/engineAPI`, branch `engineAPI-3.2-develop`) has already migrated to mysqli with fixes and improvements already applied by you.

**New Strategy**: Extract engineAPI 3.2 into Authentication + harden prepared statements where 3.2 uses string interpolation.

**Why this approach**:
- Avoids duplicating work already done in 3.2
- Beneficiary: engineAPI 3.2 already has mysqli migration + your custom fixes
- Drawback: 3.2 uses string interpolation for some queries (vulnerable to SQL injection)
- Solution: Extraction + prepared statement hardening in one pass

**R1 Research Finding**: Zero external dependencies, SAFE to refactor (no other WVU Libraries apps share this MySQL instance)
**R2 Research Finding**: EngineAPI 3.2 is 100% migrated from mysql_* → mysqli, but lacks true prepared statements (uses string interpolation)

## Phase 1: Extract engineAPI 3.2 (REPLACES bundled stale copy)
1. Copy updated engineAPI files from `/Users/tam0013/Documents/git/engineAPI/engine/` (3.2-develop)
2. Replace `src/phpincludes/engineAPI/engine/` with extracted version
3. Verify all 35 tests pass with extracted version
4. Document extraction in commit: `refactor: Extract engineAPI 3.2-develop (mysqli migration + fixes)`

**Files extracted**: engine/* modules (already have your fixes, zero mysql_* calls)

## Phase 2: Harden Prepared Statements (extracted + Authentication-specific code)
After extraction, identify and harden queries using string interpolation instead of true prepared statements.

**Authentication-specific files requiring prepared statement hardening**:
1. `src/phpincludes/databaseConnectors/database.lib.wvu.edu.remote.php` — DB connector helper functions
2. `src/public_html/engineIncludes/login-3.0.php` — LDAP/login logic
3. `src/public_html/authentication/acl.php` — Permission loading
4. `src/public_html/authentication/tempAccounts/add.php` — Temp account creation
5. `src/public_html/authentication/tempAccounts/index.php` — Temp account management
6. `src/public_html/authentication/tempAccounts/edit.php` — Temp account editing
7. `src/public_html/engineIncludes/emailSendBulk.php` — Email bulk operations

**Extracted engineAPI files requiring prepared statement hardening**:
- Any engine/* files using string interpolation in SQL queries (identify during Phase 1 review)

## Investigation & Execution Steps

**Step 1: Review extracted engineAPI 3.2 for string interpolation vulnerabilities**
```bash
grep -n "SELECT\|INSERT\|UPDATE\|DELETE" /Users/tam0013/Documents/git/engineAPI/engine/*.php | grep -v '\$'
# Look for queries that build strings vs using prepared statements
# Document: which files use string interpolation, which use true prepared statements
```

**Step 2: Extract engineAPI 3.2 into Authentication**
```bash
cd ~/Documents/git/Authentication
rm -rf src/phpincludes/engineAPI/engine
cp -r /Users/tam0013/Documents/git/engineAPI/engine/ src/phpincludes/engineAPI/
```

**Step 3: Verify extraction with tests**
```bash
./scripts/run-tests.sh
# ✅ Should show 35/35 passing
```

**Step 4: Identify string interpolation queries (security vulnerability)**
```bash
grep -rn "\$query\|sprintf\|\"SELECT.*\$\|\"INSERT.*\$\|\"UPDATE.*\$\|\"DELETE.*\$" \
  src/phpincludes/engineAPI/engine/ \
  src/public_html/engineIncludes/ \
  src/public_html/authentication/
# Document findings: which files, which lines, what patterns
```

**Step 5: Harden prepared statements (Phase 2 commits)**
- Convert string interpolation queries to mysqli prepared statements
- Use `bind_param()` for type-safe parameter binding
- Verify each hardening with: `./scripts/run-tests.sh` (must stay 35/35)

## Commit Strategy

**Phase 1 Commit** (extraction):
```bash
git commit -m "refactor: Extract engineAPI v3.2-develop (mysqli migration + custom fixes)

- Replace bundled stale engineAPI copy with v3.2-develop from external repo
- All 35 tests passing after extraction
- Removed: deprecated mysql_* calls from extracted code
- Added: your custom fixes already in 3.2-develop"
```

**Phase 2 Commits** (prepared statement hardening, dependent order):
```bash
git commit -m "refactor: Harden prepared statements in database connector"
git commit -m "refactor: Convert login-3.0.php queries to true prepared statements"
git commit -m "refactor: Harden acl.php permission loading queries"
git commit -m "refactor: Update tempAccounts modules for parameterized queries"
git commit -m "refactor: Harden emailSendBulk.php query construction"
# Continue with extracted engineAPI files requiring hardening...
```

**After each commit**: Run `./scripts/run-tests.sh` to verify 35/35 passing.

## Success Criteria

✅ **Phase 1: Extraction**
- [ ] engineAPI 3.2-develop extracted into `src/phpincludes/engineAPI/engine/`
- [ ] Bundled stale copy replaced (old copy deleted)
- [ ] All 35 tests pass after extraction
- [ ] One extraction commit: `refactor: Extract engineAPI v3.2-develop...`
- [ ] No deprecated `mysql_*` calls in extracted code

✅ **Phase 2: Prepared Statement Hardening**
- [ ] All string interpolation queries converted to true prepared statements
- [ ] No more `"SELECT * ... \$var"` patterns (all use `?` placeholders + `bind_param()`)
- [ ] Files hardened: database connector, login-3.0, acl, tempAccounts/*, emailSendBulk, extracted engine modules
- [ ] All 35 tests pass after each commit
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

## Refactoring Patterns

### Pattern 1: String Interpolation → Prepared Statements (PRIORITY)
This is the security hardening focus for Seq 3.

**Vulnerable** (string interpolation):
```php
$mysqli->query("SELECT * FROM users WHERE id=" . $id . " AND name='" . $name . "'");
```

**Secure** (prepared statements):
```php
$stmt = $mysqli->prepare("SELECT * FROM users WHERE id = ? AND name = ?");
$stmt->bind_param("is", $id, $name);  // "i" = int, "s" = string
$stmt->execute();
$result = $stmt->get_result();
$row = $result->fetch_assoc();
```

Key improvements:
1. Parameters separated from query string
2. `bind_param()` ensures type-safe binding
3. Prevents SQL injection automatically
4. Engine can optimize query plan

### Pattern 2: Legacy mysqli calls → Modern mysqli (already done in 3.2)
Extracted code already converted `mysql_*` → `mysqli_*`. No work needed here.

**For reference** (already done):
```php
// Old: $result = mysql_query("SELECT ...");
// New: $result = $mysqli->query("SELECT ...");

// Old: $row = mysql_fetch_assoc($result);
// New: $row = $result->fetch_assoc();

// Old: mysql_num_rows($result)
// New: $result->num_rows
```

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
