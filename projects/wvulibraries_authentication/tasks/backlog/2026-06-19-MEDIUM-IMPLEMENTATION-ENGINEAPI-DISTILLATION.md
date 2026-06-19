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

# Seq 6: EngineAPI Framework Bloat Removal

## Problem & Strategy

The Authentication application currently includes an 88-file engineAPI framework package at `src/phpincludes/engineAPI/engine/`. However, Authentication uses only a minimal subset:
- **LDAP authentication** (specific modules)
- **Session management** (specific modules)
- **CSRF token handling** (specific modules)
- **ACL/permission loading** (specific modules)
- **Temporary account lookup** (specific modules)

Most engineAPI modules are unused bloat.

**Core insight**: Authentication is the ONLY remaining active engineAPI user (MFCS being retired, data extraction to WVU Knapsack already happening). Once Authentication is off engineAPI, the entire 88-file framework can be deprecated.

**Strategy**: Audit the 88 files in `src/phpincludes/engineAPI/engine/`, identify what Authentication actually uses, delete the unused modules/directories. Keep engineAPI in same location, just remove bloat.

**Outcome**: 
- Reduced local codebase (easier to maintain, understand, modify)
- Same `src/phpincludes/engineAPI/engine/` directory, just with unused modules removed
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

**Step 5: Plan deletion approach**
Document strategy:
1. Identify which engine/ subdirectories are unused
2. Create ordered deletion plan (delete dependencies last)
3. Note: Keep all code in same location, just remove unused modules

## Execution Steps

### Phase 1: Audit & Mapping (Investigation + Planning)

**Do not delete anything yet.** Goal: Complete understanding of what's used.

1. Execute investigation steps above
2. Document all findings in: `doc/ENGINEAPI_DISTILLATION_AUDIT.md`
   - List all engine/ subdirectories with usage status (used / unused)
   - Identify which modules are actively referenced in Authentication code
   - Create deletion plan (ordered list of which directories to remove)
3. Run full test suite: `./scripts/run-tests.sh` (baseline: 70/70 passing)

### Phase 2: Delete Unused Modules (Cleanup)

1. Delete unused engine/ subdirectories identified in audit
2. Verify tests still pass after each deletion: `./scripts/run-tests.sh` (must stay 70/70)
3. Commit changes incrementally: `refactor: Remove unused engineAPI module (xyz/)`
4. Final test run after all deletions: `./scripts/run-tests.sh` (must pass 70/70)
5. Commit summary: `refactor: Remove unused engineAPI modules (88 files → distilled)`

**Note**: engineAPI stays in same location (`src/phpincludes/engineAPI/engine/`), just with bloat removed.

## Success Criteria

✅ **Phase 1: Audit Complete**
- [ ] Dependency map created and committed to `doc/ENGINEAPI_DISTILLATION_AUDIT.md`
- [ ] All engine/ subdirectories categorized (used / unused)
- [ ] Deletion plan documented with ordering/dependencies noted
- [ ] 70/70 tests pass before deletions

✅ **Phase 2: Deletion & Cleanup**
- [ ] All unused engine/ modules deleted from `src/phpincludes/engineAPI/engine/`
- [ ] All 70 tests passing after each deletion
- [ ] Multiple commits made (one per module deletion or group)
- [ ] 70/70 tests pass after all deletions

✅ **Phase 3: Cleanup & Verification**
- [ ] Entire `src/phpincludes/engineAPI/engine/` directory deleted
- [ ] All 70 tests passing after deletion
- [ ] Final commit message documents results: "Reduced codebase from 88 files to X lines, maintained 70/70 test pass rate"
- [ ] Task moved to completed/ folder with summary

## Testing Strategy

Run tests after each logical deletion:
```bash
cd ~/Documents/git/Authentication
./scripts/run-tests.sh
# ✅ Must show 70/70 passing
```

Stop and debug if tests fall below 70. All code paths have test coverage via:
- `tests/LoginTest.php` (login scenarios)
- `tests/Unit/AuthenticationTest.php` (core auth functions)
- `tests/Fixtures/MockLDAPServer.php` (LDAP mock)

## Branch & Commit Strategy

**All work on**: `refactor/authentication-modernization`

**Phase 1 Commit** (audit results):
```bash
git commit -m "docs: EngineAPI distillation audit (bloat identification)

88-file framework analyzed:
- X modules used (core functions for Authentication)
- Y modules unused (candidates for deletion)

Deletion plan: Remove Y unused modules from src/phpincludes/engineAPI/engine/
All 70 tests baseline passed before deletions."
```

**Phase 2 Commits** (deletion, one per module group):
```bash
git commit -m "refactor: Remove unused engineAPI modules (xyz/, abc/)

- Deleted engine/xyz/ directory (not used by Authentication)
- Deleted engine/abc/ directory (not used by Authentication)
- All 70 tests passing"

# Continue with more deletions...
```

**Final Commit** (summary):
```bash
git commit -m "refactor: Remove unused engineAPI framework bloat

- Deleted all unused engine/ modules
- Kept only core modules used by Authentication (LDAP, sessions, CSRF, ACL, temp accounts)
- Reduced 88-file framework to distilled version
- All 70 tests passing
- engineAPI stays in src/phpincludes/engineAPI/engine/, just with bloat removed"
```

## Long-Term Impact

- **Authentication**: Reduced engineAPI footprint (unused modules removed)
- **engineAPI**: Can be archived/deprecated once this task complete (no remaining active users after MFCS retirement)
- **Maintenance**: Smaller local codebase = easier to navigate and maintain
- **New developers**: Clearer engineAPI usage (only core modules present locally)

## Reference Docs

- Audit plan: `doc/ENGINEAPI_DISTILLATION_AUDIT.md` (will be created during Phase 1)
- Framework files: `src/phpincludes/engineAPI/engine/` (88 files → distilled)
- Current tests: `tests/LoginTest.php`, `tests/Unit/AuthenticationTest.php`
- Test runner: `scripts/run-tests.sh`
