---
title: "Docker Modernization: MySQL 5.7 to 8.0 Migration"
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
---

# Task: Docker Modernization: MySQL 5.7 to 8.0 Migration

## Problem
**UPDATE (2026-06-18)**: DevOps confirmed production is running **MySQL 8.0.46** (not 5.7). Dev environment has been updated to match. This task now focuses on **schema compatibility verification** and **documentation of the upgrade path** rather than the upgrade itself.

MySQL 5.7 reached **end-of-support on October 21, 2023**. Running unsupported database versions creates:

- **Security vulnerabilities**: No security patches for newly discovered CVEs
- **Compatibility issues**: PHP 8.3 drivers may not have optimal support
- **Performance degradation**: Miss out on query optimization improvements
- **Production alignment**: Ensure dev environment matches production database versions

## Scope
**Changes already completed**:
- ✅ Updated `docker-compose.dev.yml` MySQL service from `mysql:5.7` to `mysql:8.0` (2026-06-18)

**Remaining work**:
1. Review and test database initialization SQL for MySQL 8.0 compatibility
2. Verify authentication schema creates cleanly without deprecation warnings
3. Test application connection with MySQL 8.0 native authentication plugin
4. Test application connection and query performance
5. Document any configuration adjustments needed
6. Create migration runbook for production VM (if not already documented)

**Compatibility checks**:
- [ ] SQL schema creates without deprecation warnings
- [ ] LDAP/authentication queries work as-is
- [ ] No character encoding issues (utf8 vs utf8mb4)
- [ ] Session handling unchanged
- [ ] Performance benchmarks vs. MySQL 5.7

## Success Criteria
- [x] Docker image updated to mysql:8.0 (completed 2026-06-18)
- [ ] All SQL files pass syntax checks for MySQL 8.0
- [ ] Database initializes cleanly from setup-docker.sql without warnings
- [ ] Login/LDAP auth functional tests pass (PHPUnit suite)
- [ ] No application errors in logs with MySQL 8.0
- [ ] Docker compose health checks pass
- [ ] Character encoding verified (utf8mb4 if applicable)
- [ ] No performance regressions vs. MySQL 5.7

## Related
- Frontend updates branch (contains docker-compose changes)
- Database deprecation audit (mysql_* functions)
- Production deployment (VM database upgrade needed)

## Notes
- MySQL 8.0 default authentication plugin is `mysql_native_password` (compatible with mysqli)
- May need to adjust max_connections, query cache settings for compatibility
- backup existing MySQL 5.7 volumes before testing upgrade
- Consider future: MySQL 8.0 will reach EOL in 2026; plan for 8.1 LTS later
