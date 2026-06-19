---
title: "Seq 6 - EngineAPI Framework Distillation"
status: backlog
priority: MEDIUM
type: IMPLEMENTATION
created: 2026-06-19
updated: 2026-06-19
estimated_effort: "2-3 weeks"
sequence: 6
blocked_by:
  - "2026-06-18-HIGHEST-SETUP-PHPUNIT-INTEGRATION" ✅ DONE
  - "2026-06-18-HIGH-FEATURE-LOGIN-TEST-SUITE" ✅ DONE
  - "2026-06-18-HIGH-MAINTENANCE-DEPRECATED-MYSQL-FUNCTIONS" ✅ DONE
  - "2026-06-19-HIGH-MAINTENANCE-SECURITY-AUDIT" ✅ DONE
blocks:
  - "2026-06-19-LOW-FEATURE-CENTRALIZED-LOGGING"
  - "2026-06-19-LOW-MAINTENANCE-DOCUMENTATION-AUDIT"
branch: "refactor/authentication-modernization" (shared working branch)
test_coverage: "70/70 passing tests required throughout"
---

# Seq 6: EngineAPI Framework Distillation

## Problem & Strategy

The Authentication application currently includes an 88-file engineAPI framework package extracted from `/Users/tam0013/Documents/git/engineAPI/engine/3.2-develop`. However, Authentication uses only a minimal subset of this framework:
- **LDAP authentication** (5-10 files)
- **Session management** (2-3 files)
- **CSRF token handling** (1-2 files)
- **ACL/permission loading** (1-2 files)
- **Temporary account lookup** (1-2 files)

**Core insight**: Authentication is the ONLY remaining active engineAPI user (MFCS being retired, data extraction to WVU Knapsack already happening). Once Authentication is off engineAPI, the entire 88-file framework can be deprecated.

**Strategy**: Audit the 88 extracted files, identify what's actually used, delete everything else. Result: 88 files → ~500 lines of distilled, purpose-built Authentication code.

**Outcome**: 
- Reduced codebase (easier to maintain, understand, modify)
- No external framework dependency to upgrade/maintain
- Clear deprecation path for engineAPI (retire entirely once Authentication migration complete)

## Investigation Steps

**Step 1: Inventory extracted engineAPI files**
```bash
ls -la src/phpincludes/engineAPI/engine/ | wc -l
# Should show 88 files + directories
```

**Step 2: Audit code usage (find what Authentication actually uses)**
```bash
# Search for class/function calls to engineAPI modules
cd /Users/tam0013/Documents/git/Authentication
grep -r "engineAPI\|engine\->" src/public_html/ src/phpincludes/ | grep -v "\.js" | head -50

# Look for includes from engine/
grep -r "engine\/" src/public_html/ src/phpincludes/ | grep "require\|include"

# Check what engine/ subdirectories are actually referenced
find src/phpincludes/engineAPI/engine -name "*.php" -exec grep -l "class\|function" {} \; | sort
```

**Step 3: Create dependency map**
Document:
- Which engine/ modules are actually used by Authentication code
- Which are only used by other modules (transitive dependencies)
- Which are completely unused (dead code)

Example mapping:
```
CORE DEPENDENCIES (directly used by Authentication):
- engine/config/ (configuration files)
- engine/objects/ (base classes, if any)
- engine/database/ (query helpers, if any)

SECONDARY DEPENDENCIES (used by core):
- engine/utilities/ (helpers, formatters)
- engine/cache/ (caching layer, if used)

UNUSED (can delete):
- engine/xml/ (XML parsing, not used)
- engine/csv/ (CSV handling, not used)
- [list others found to be unused]
```

**Step 4: Identify lines of actual code vs framework**
```bash
# Count lines in each subdirectory
for dir in src/phpincludes/engineAPI/engine/*/ ; do 
  count=$(cat "$dir"*.php 2>/dev/null | wc -l)
  echo "$(basename $dir): $count lines"
done

# Identify which are <100 lines (candidates for migration to core app)
```

**Step 5: Plan distillation approach**
Document strategy:
1. **Extract core functions** (LDAP, sessions, CSRF, ACL, temp accounts) into new files:
   - `src/phpincludes/auth/LDAP.php` (LDAP authentication)
   - `src/phpincludes/auth/SessionManager.php` (session handling)
   - `src/phpincludes/auth/CSRFProtection.php` (CSRF tokens)
   - `src/phpincludes/auth/ACL.php` (permission loading)
   - `src/phpincludes/auth/TempAccounts.php` (temp account lookup)

2. **Update all references** from engineAPI calls to new distilled modules

3. **Delete entire engine/ directory** (after verification)

## Execution Steps

### Phase 1: Audit & Mapping (Investigation + Planning)

**Do not delete anything yet.** Goal: Complete understanding of what's used.

1. Execute investigation steps above
2. Document all findings in: `doc/ENGINEAPI_DISTILLATION_AUDIT.md`
   - List all 88 files with usage status (used / unused / transitive)
   - Identify core functions for extraction
   - Create distillation plan (which modules to extract, how to reorganize)
3. Run full test suite: `./scripts/run-tests.sh` (baseline: 70/70 passing)

### Phase 2: Extract & Migrate (Implementation)

1. Create new `src/phpincludes/auth/` directory structure
2. Extract core functions from engine/ modules into distilled versions
3. Update all includes/requires in application code
4. Verify tests still pass after each module extraction: `./scripts/run-tests.sh` (must stay 70/70)
5. Commit changes incrementally: `refactor: Extract LDAP from engineAPI`, `refactor: Extract sessions...` etc.

### Phase 3: Cleanup (Deletion)

1. Delete `src/phpincludes/engineAPI/engine/` directory (after all migrations complete)
2. Delete or archive `src/phpincludes/engineAPI/` if it becomes empty
3. Final test run: `./scripts/run-tests.sh` (must pass 70/70)
4. Commit: `refactor: Remove unused engineAPI framework (88 files → distilled codebase)`

## Success Criteria

✅ **Phase 1: Audit Complete**
- [ ] Dependency map created and committed to `doc/ENGINEAPI_DISTILLATION_AUDIT.md`
- [ ] All 88 files categorized (used / transitive / unused)
- [ ] Core functions identified for extraction
- [ ] Distillation plan documented with file-by-file strategy
- [ ] 70/70 tests pass before changes

✅ **Phase 2: Extraction & Migration**
- [ ] New `src/phpincludes/auth/` directory created with distilled modules
- [ ] All engineAPI references in application code updated to new distilled modules
- [ ] All 70 tests passing after each extraction commit
- [ ] 5-10 commits made (one per major function extraction)
- [ ] No breaking changes to public API (external calls still work)

✅ **Phase 3: Cleanup & Verification**
- [ ] Entire `src/phpincludes/engineAPI/engine/` directory deleted
- [ ] All 70 tests passing after deletion
- [ ] Final commit message documents results: "Reduced codebase from 88 files to X lines, maintained 70/70 test pass rate"
- [ ] Task moved to completed/ folder with summary

## Testing Strategy

Run tests after each logical extraction:
```bash
cd ~/Documents/git/Authentication
./scripts/run-tests.sh
# ✅ Must show 70/70 passing (Seq 1+2 tests + Seq 3/4 validation)
```

Stop and debug if tests fall below 70. All code paths have test coverage via:
- `tests/LoginTest.php` (login scenarios)
- `tests/Unit/AuthenticationTest.php` (core auth functions)
- `tests/Fixtures/MockLDAPServer.php` (LDAP mock)

## Branch & Commit Strategy

**All work on**: `refactor/authentication-modernization`

**Phase 1 Commit** (audit results):
```bash
git commit -m "docs: EngineAPI distillation audit (dependency mapping)

88-file framework analyzed:
- X files used (core functions)
- Y files transitive dependencies
- Z files unused (candidates for deletion)

Distillation plan: Extract core → new src/phpincludes/auth/ → delete engine/
All 70 tests baseline passed before distillation."
```

**Phase 2 Commits** (extraction, one per major function):
```bash
git commit -m "refactor: Extract LDAP auth from engineAPI framework

- Migrated LDAP bind logic to src/phpincludes/auth/LDAP.php
- Updated references in login-3.0.php, authentication/index.php
- All 70 tests passing"

git commit -m "refactor: Extract session management from engineAPI framework
...
git commit -m "refactor: Extract CSRF protection from engineAPI framework
...
```

**Phase 3 Commit** (final cleanup):
```bash
git commit -m "refactor: Remove unused engineAPI framework (88 files → distilled)

- Deleted src/phpincludes/engineAPI/engine/ directory
- All core functions migrated to src/phpincludes/auth/
- Codebase reduced from 88 framework files to ~500 lines of purpose-built code
- All 70 tests passing
- Result: Authentication now has zero external framework dependencies"
```

## Long-Term Impact

- **Authentication**: Zero framework dependency (self-contained, minimal codebase)
- **engineAPI**: Can be archived/deprecated once this task complete (no remaining active users after MFCS retirement)
- **Maintenance**: Future Authentication changes don't require framework upgrades/maintenance
- **New developers**: Easier onboarding (minimal codebase to understand)

## Reference Docs

- Audit plan: `doc/ENGINEAPI_DISTILLATION_AUDIT.md` (will be created during Phase 1)
- Framework files: `src/phpincludes/engineAPI/engine/` (88 files total)
- Current tests: `tests/LoginTest.php`, `tests/Unit/AuthenticationTest.php`
- Test runner: `scripts/run-tests.sh`
