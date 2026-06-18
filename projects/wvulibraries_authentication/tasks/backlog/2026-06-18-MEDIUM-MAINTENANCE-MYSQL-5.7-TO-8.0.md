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
The `docker-compose.dev.yml` specifies `mysql:5.7`, which reached **end-of-support on October 21, 2023**. Running unsupported database versions creates:

- **Security vulnerabilities**: No security patches for newly discovered CVEs
- **Compatibility issues**: PHP 8.3 drivers may not have optimal support
- **Performance degradation**: Miss out on query optimization improvements
- **Production risk**: Development environment diverges from production database versions

## Scope
**Changes required**:
1. Update docker-compose.yml MySQL service from `mysql:5.7` to `mysql:8.0`
2. Review and update database initialization SQL for MySQL 8.0 compatibility
3. Test authentication schema creation and migration
4. Update SQL connection parameters if needed (default authentication plugins changed)
5. Test application connection and query performance
6. Document migration path for production VMs

**Compatibility checks**:
- [ ] SQL schema creates without deprecation warnings
- [ ] LDAP/authentication queries work as-is
- [ ] No character encoding issues (utf8 vs utf8mb4)
- [ ] Session handling unchanged
- [ ] Performance benchmarks vs. MySQL 5.7

## Success Criteria
- [ ] Docker image updated to mysql:8.0 (latest stable)
- [ ] All SQL files pass syntax checks
- [ ] Database initializes cleanly from setup-docker.sql
- [ ] Login/LDAP auth functional tests pass
- [ ] No application errors in logs
- [ ] Docker compose health checks pass
- [ ] Performance is equal or better than MySQL 5.7

## Related
- Frontend updates branch (contains docker-compose changes)
- Database deprecation audit (mysql_* functions)
- Production deployment (VM database upgrade needed)

## Notes
- MySQL 8.0 default authentication plugin is `mysql_native_password` (compatible with mysqli)
- May need to adjust max_connections, query cache settings for compatibility
- backup existing MySQL 5.7 volumes before testing upgrade
- Consider future: MySQL 8.0 will reach EOL in 2026; plan for 8.1 LTS later
