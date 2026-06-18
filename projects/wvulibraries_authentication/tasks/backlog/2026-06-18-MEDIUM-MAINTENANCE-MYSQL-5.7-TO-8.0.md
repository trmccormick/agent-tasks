---
title: "Docker Modernization: MySQL 8.0 to 8.4 LTS Upgrade"
status: backlog
priority: MEDIUM
type: MAINTENANCE
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "1-2 weeks"
sequence: 5
blocked_by:
  - "2026-06-18-HIGH-MAINTENANCE-DEPRECATED-MYSQL-FUNCTIONS"
blocks:
  - "2026-06-18-HIGH-IMPLEMENTATION-ENGINE-API-DISTILLATION"
branch: "refactor/authentication-modernization"
branch_strategy: "All work on single refactor branch; verify schema compatibility; full test suite must pass before merge"
target_mysql_version: "8.4 LTS"
---

# Task: Docker Modernization: MySQL 8.0 to 8.4 LTS Upgrade

## Problem
**CRITICAL UPDATE (2026-06-18)**: 
- MySQL 8.0.46 reached **end-of-life on April 2024** (no longer receiving security updates)
- DevOps confirmed target: **MySQL 8.4 LTS** (next stable, long-term support)
- Current state:
  - Production: 8.0.46 (EOL, unsupported)
  - Dev: 8.0 (temporarily aligned for testing; will be upgraded)

Running EOL database versions creates:
- **Critical security vulnerabilities**: No patches for new CVEs
- **Compliance risk**: Unsupported versions violate many organizational policies
- **Performance gaps**: Miss optimization improvements in 8.4 LTS
- **PHP 8.3 driver optimization**: 8.4 has better mysqli/PDO support

## Scope
**Changes already completed**:
- ✅ Dev environment updated from 8.0 to 8.4 LTS target

**Upgrade process** (this task):
1. Obtain database dump from production (8.0.46) — *pending from DevOps*
2. Update docker-compose.dev.yml: `mysql:8.0` → `mysql:8.4` (for accurate testing)
3. Test schema migration: Restore 8.0.46 dump into 8.4 instance
4. Verify authentication schema creates cleanly without warnings
5. Test application connection with 8.4 authentication plugin
6. Test LDAP/authentication queries against 8.4
7. Performance benchmarks and query analysis
8. Document migration runbook for production VM upgrade
9. Prepare deployment plan (backup, migration, validation, rollback steps)

**Schema compatibility checks**:
- [ ] Authentication dump restores without errors
- [ ] MyISAM tables (tempAccounts) migrate to appropriate engine
- [ ] Character encoding verified (utf8mb4 for 8.4 compatibility)
- [ ] No SQL mode incompatibilities
- [ ] All stored procedures/functions compatible (if any)
- [ ] LDAP/authentication queries work as-is
- [ ] Session handling unchanged
- [ ] Performance benchmarks vs. MySQL 8.0

## Success Criteria
- [ ] Docker image updated to mysql:8.4 (when database dump received)
- [ ] Database dump from 8.0.46 production successfully restored
- [ ] All SQL files pass syntax checks for MySQL 8.4
- [ ] Schema compatibility verified (no errors, no deprecation warnings)
- [ ] MyISAM engine conversion strategy documented (if applicable)
- [ ] Database initializes cleanly from setup-docker.sql
- [ ] Login/LDAP auth functional tests pass (PHPUnit suite against 8.4)
- [ ] No application errors in logs with MySQL 8.4
- [ ] Docker compose health checks pass
- [ ] Character encoding verified (utf8mb4 compliance)
- [ ] Performance benchmarks completed (no regressions)
- [ ] Migration runbook created (backup, restore, validation steps)
- [ ] DevOps approval before production upgrade

## Related
- Production database: database.lib.wvu.edu (8.0.46, needs upgrade)
- Deprecated MySQL functions migration (Seq 3 — prerequisite)
- PHPUnit test suite (Seq 1 — must pass against 8.4 before prod upgrade)
- EngineAPI distillation (Seq 6 — blocked until upgrade complete)
- Database deprecation audit (mysql_* functions must be removed first)

## Notes
- **MySQL 8.4 is LTS**: Long-term support version (recommended for production)
- **DevOps owns production upgrade**: This task prepares testing/validation; DevOps executes prod deployment
- **Backup critical**: Create full backup before any upgrade
- **Rollback plan**: Document rollback procedure in case of issues
- **Post-upgrade**: Monitor application logs for any 8.4-specific issues
- **Future planning**: Track when 8.4 approaches EOL for next upgrade cycle
