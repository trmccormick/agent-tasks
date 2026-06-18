---
title: "Deprecated MySQL Function Audit and Migration"
status: backlog
priority: HIGH
type: MAINTENANCE
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "3-4 weeks"
---

# Task: Deprecated MySQL Function Audit and Migration

## Problem
The Authentication application contains 12 PHP files using deprecated `mysql_*` functions (mysql_query, mysql_fetch_array, mysql_escape_string, etc.). These functions were removed in PHP 7.0 and later versions. While the application currently runs on PHP 8.3 via the mysql compatibility layer within EngineAPI, this creates:

- **Compatibility risk**: PHP 9.0+ will not support these functions at all
- **Security debt**: mysql_* functions lack prepared statement support
- **Maintenance burden**: Any code using these functions is difficult to audit and extend
- **Performance impact**: No query optimization or connection pooling available

## Scope
**Files affected** (12 total):
- `src/public_html/engineIncludes/emailSendBulk.php`
- `src/phpincludes/engineAPI/engine/accessControl/mysql.php`
- `src/phpincludes/engineAPI/engine/stats.php`
- `src/phpincludes/engineAPI/engine/modules/snippets.php`
- `src/phpincludes/engineAPI/engine/modules/permissionObject.php`
- `src/phpincludes/engineAPI/engine/modules/mediaSiteEdasClient.php`
- `src/phpincludes/engineAPI/engine/modules/fileManagement.php`
- `src/phpincludes/engineAPI/engine/modules/listmanagement.php`
- `src/phpincludes/engineAPI/engine/modules/mysql.php`
- `src/phpincludes/engineAPI/engine/modules/searchObject.php`
- (2 more files TBD)

**Approach**:
- Replace `mysql_*` functions with mysqli or PDO equivalents
- Maintain backward compatibility with existing EngineAPI interface where possible
- Add prepared statement support for SQL injection prevention
- Write unit tests for each migrated module

## Success Criteria
- [ ] All 12 files converted to mysqli/PDO
- [ ] No `mysql_*` function calls remain in codebase
- [ ] All login and LDAP auth tests pass
- [ ] Database connection tests pass (mysql.php module)
- [ ] Code review from architect (EngineAPI implications)

## Related
- EngineAPI EOL Assessment (task: wvulibraries_authentication-RESEARCH-ENGINE-API-REPLACEMENT)
- Docker modernization (MySQL 5.7 → 8.0)

## Notes
- This is core infrastructure work; proceed carefully with testing
- EngineAPI is a shared framework; changes may affect other WVU Libraries applications
- Focus on minimal changes first to reduce regression risk
