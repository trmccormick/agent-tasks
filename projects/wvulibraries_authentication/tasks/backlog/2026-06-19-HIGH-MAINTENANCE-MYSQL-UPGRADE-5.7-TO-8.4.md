---
title: "Seq 5 - MySQL 5.7 → 8.4 LTS Upgrade"
status: backlog
priority: HIGH
type: MAINTENANCE
created: 2026-06-19
updated: 2026-06-19
estimated_effort: "1-2 hours"
sequence: 5
blocked_by: []
depends_on:
  - "R1 compatibility research (COMPLETE — no code changes needed)"
  - "Seq 4 security audit (COMPLETE — no blocking issues)"
---

## Objective

Upgrade MySQL from 5.7.40 (current) to 8.4 LTS (latest stable) in the Docker development environment. Verify full backward compatibility and all tests pass without modifications.

## Context

**R1 Research Findings (VERIFIED)**: Schema is 100% compatible with MySQL 8.4 LTS. Zero code changes required.

**Current State**:
- MySQL 5.7.40 in docker-compose.dev.yml
- 70/70 tests passing on current version
- All tables, indexes, and data types compatible with 8.4

**Why 8.4 LTS**:
- Long-term support (2028+)
- Modern query optimization
- Better performance
- Security updates for 5-year lifecycle

## Success Criteria

✅ Docker image updated to MySQL 8.4 LTS
✅ docker-compose.dev.yml reflects new version
✅ Database initializes successfully on first container startup
✅ All 70 PHPUnit tests pass without code changes
✅ No regressions in existing functionality
✅ Authentication app connects successfully
✅ Commit on refactor/authentication-modernization branch

## Implementation Steps

### Phase 1: Prepare & Backup
1. Read current docker-compose.dev.yml
2. Verify MySQL 5.7 container is running (`docker ps`)
3. Backup current database state (export schema and sample data)
4. Document current version and database state

### Phase 2: Update Docker Configuration
1. Update `docker-compose.dev.yml`:
   - Change MySQL image from `mysql:5.7` to `mysql:8.4`
   - Keep all environment variables identical (MYSQL_ROOT_PASSWORD, charset, etc.)
   - Preserve port mapping (3306)
   
2. Update `Dockerfile` if MySQL version is specified there:
   - Change any `mysql:5.7` references to `mysql:8.4`

3. Update `.gitignore` and related config files if version-pinned elsewhere

### Phase 3: Test Upgrade
1. Bring down old containers: `docker compose -f docker-compose.dev.yml down`
2. Start new containers with updated image: `docker compose -f docker-compose.dev.yml up -d`
3. Wait for MySQL to initialize (monitor logs: `docker logs db`)
4. Verify connection: `docker exec db mysql -uroot -p$MYSQL_ROOT_PASSWORD -e "SELECT VERSION();"`
5. Confirm database exists and contains expected tables

### Phase 4: Run Test Suite
1. Execute: `docker compose -f docker-compose.test.yml exec app php vendor/bin/phpunit`
2. Verify: All 70 tests pass
3. Check for any deprecation warnings or errors
4. Document any test output differences (if any)

### Phase 5: Verify Functionality
1. Test basic authentication flow manually if possible
2. Check database logs for errors: `docker logs db`
3. Confirm no error_log entries related to MySQL compatibility

### Phase 6: Commit & Document
1. Commit changes to refactor/authentication-modernization:
   - `git add docker-compose.dev.yml Dockerfile [any other files]`
   - Message: "chore: Upgrade MySQL 5.7 → 8.4 LTS (zero code changes required)"
2. Document any observations in commit message
3. Push to origin

## Target Files

- `docker-compose.dev.yml` — MySQL service definition
- `Dockerfile` — If MySQL version specified
- `docker-compose.test.yml` — May also reference MySQL version (update if needed)
- `config/mysql-config.cnf` — Verify no version-specific settings

## Known Issues & Mitigations

**Issue 1: MySQL 8.0+ password hashing**
- MySQL 8.0+ defaults to `caching_sha2_password` vs 5.7's `mysql_native_password`
- **Mitigation**: Already handled by charset/auth settings in docker-compose
- **Verification**: Test authentication with existing credentials

**Issue 2: InnoDB changes**
- Schema uses InnoDB (good), but file format may need review
- **Mitigation**: R1 confirmed compatible; no changes needed
- **Verification**: All tests pass

**Issue 3: Query behavior differences**
- Some MySQL 8.0+ strict SQL modes may affect queries
- **Mitigation**: Application uses prepared statements (Seq 3); safe
- **Verification**: 70/70 tests + no regressions

## Testing Checklist

- [ ] Old containers shut down cleanly
- [ ] New MySQL 8.4 image pulls successfully
- [ ] Container starts and initializes schema
- [ ] Database connection succeeds with existing credentials
- [ ] All 70 tests pass
- [ ] No new errors in test output
- [ ] No new deprecation warnings
- [ ] Manual auth test successful (if possible)
- [ ] Database logs clean (no errors)
- [ ] Git status clean after changes
- [ ] Commit message clear and descriptive

## Rollback Plan

If tests fail:
1. `docker compose -f docker-compose.dev.yml down`
2. Revert docker-compose.dev.yml to MySQL 5.7
3. `docker compose -f docker-compose.dev.yml up -d`
4. Re-run tests to confirm rollback successful
5. Document failure details for investigation

## Related Sequences

- **Seq 3**: MySQL functions already modernized (mysqli), compatible with 8.4
- **Seq 4**: Security audit completed; no MySQL version dependency
- **Seq 5 Phase 2**: (future) Performance tuning for 8.4 if needed
- **Seq 7**: Logging infrastructure (future, independent of MySQL version)

---

## Task Status Log

- **2026-06-19**: Created post-Seq 4 completion. Ready for implementation.
