# WVU Libraries Authentication — Project Status & Task Tracking
**Last Updated:** June 18, 2026

---

## Project Overview
WVU Libraries Authentication System — Centralized LDAP-based authentication gateway for `systems.lib.wvu.edu` and related internal library services. Handles staff login, temporary patron account creation, alumni account authentication, and authorization management via WVU Active Directory integration.

**Repository**: `/Users/tam0013/Documents/git/Authentication`  
**Production URL**: `https://systems.lib.wvu.edu`  
**Dev Docker**: `http://localhost:8080`  
**Stack**: PHP 8.3, Apache 2.4, MySQL 5.7, LDAP, EngineAPI framework

---

## Current Status
- **Status:** Functional (LDAP authentication operational post-kernel-patch)
- **Last Session:** 2026-06-18 (LDAP connectivity resolved, project setup initialization)
- **Known Issues:** None currently blocking; static asset symlinks available but not currently critical

---

## Active Tasks
- [None currently assigned]
- See `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_authentication/tasks/active/` for assigned work

---

## Backlog (8 tasks created 2026-06-18)
1. **HIGH-MAINTENANCE** — Deprecated MySQL Function Audit and Migration (3-4 weeks)
   - 12 files using mysql_* functions need conversion to mysqli/PDO
   - Core infrastructure work affecting EngineAPI modules

2. **HIGH-MAINTENANCE** — Review and Merge Frontend Updates Branch (2-3 weeks)
   - 120+ commits with CSS updates, Bootstrap 5, Docker config
   - Requires comprehensive testing and visual regression checks

3. **MEDIUM-RESEARCH** — EngineAPI End-of-Life Assessment (2 weeks)
   - Determine long-term strategy: stay on EngineAPI or migrate
   - Impacts Application and other WVU Libraries services

4. **MEDIUM-MAINTENANCE** — Docker Modernization: MySQL 5.7 to 8.0 (1-2 weeks)
   - MySQL 5.7 is EOL; upgrade to current stable version
   - Verify schema compatibility

5. **MEDIUM-MAINTENANCE** — PHP Security Audit: Deprecated Patterns (2-3 weeks)
   - Check for SQL injection, LDAP injection, XSS, session security
   - OWASP Top 10 review

6. **MEDIUM-FEATURE** — Login Functionality Test Suite (1-2 weeks)
   - PHPUnit tests with mock LDAP server
   - 20+ tests covering auth flows and error cases
   - CI/CD pipeline setup

7. **LOW-FEATURE** — Centralized Logging and Error Handling (1 week)
   - Audit trail for authentication attempts
   - JSON structured logging
   - Log rotation and retention

8. **LOW-MAINTENANCE** — Documentation Audit (1 week)
   - Architecture guide
   - LDAP flow documentation
   - Configuration reference

See `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_authentication/tasks/backlog/` for full details

---

## Completed Tasks
- Project setup and agent-tasks integration (2026-06-18)

---

## Task Order & Priority Notes
- All task management lives in `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_authentication/tasks/`
- Task lifecycle: `backlog/` → `active/` → `completed/`
- Follow agent workflow rules as documented in `/Users/tam0013/Documents/git/agent-tasks/` (README.md, TASK_TEMPLATE.md, etc.)

---

## Special Warnings & Conventions

### LDAP Connectivity
- **WVU-AD LDAP Server**: `ldap://wvu-ad.wvu.edu`  
- **Bind Format**: `username@wvu-ad.wvu.edu`  
- **DC**: `DC=wvu-ad,DC=wvu,DC=edu`  
- Recent production issue (2026-06-18): Kernel patching briefly disrupted LDAP connectivity (RESOLVED)

### EngineAPI Template Engine
- Centralized configuration in `/phpincludes/engineAPI/engine/config/default.php`
- Local variables control CSS/JS URLs and template rendering
- Do not hardcode LDAP URLs — always use `$engineVars['domains']['wvu-ad']` configuration

### Static Assets
- Bootstrap, jQuery, and print CSS should resolve via EngineAPI local variables
- Symlinks commented in `scripts/entrypoint.sh` — available if needed for future static asset CDN migration
- Current template paths point to `https://systems.lib.wvu.edu/css/2012/` and `https://systems.lib.wvu.edu/javascript/2012/`

### Environment Configuration
- **Never commit** `env.auth` or `env.dev.auth` files (contain secrets)
- Database credentials managed via env files
- MySQL connection uses `database.lib.wvu.edu.remote.php` in production

---

## Session Notes
- **2026-06-18**: Project initialization — LDAP issue was kernel-patching-related, not application code. Static asset 404s identified as cosmetic (missing CSS/JS references do not cause authentication failure). Project now integrated into agent-tasks workflow for formal task tracking and agent guidance.

- **2026-06-18**: Comprehensive task analysis completed. Repository review found:
  - **frontend-updates branch**: 120+ commits behind master, needs merge + testing (970 files changed, major CSS/UI updates, Docker config improvements)
  - **Code debt**: 12 files use deprecated mysql_* functions (needs migration to mysqli/PDO for PHP 9.0 compatibility)
  - **EngineAPI status**: 88 PHP files across 30 modules, minimally maintained, used by other WVU Libraries apps (needs strategic EOL assessment)
  - **Infrastructure**: MySQL 5.7 is EOL (supports MySQL 8.0), Docker config ready, PHP 8.3 is current
  - **Testing**: No automated test coverage for login/LDAP flows
  - **Security**: Full audit needed (LDAP injection, SQL injection vectors, session handling)
  - **Documentation**: Minimal comments, new devs need architecture guide
  
  **Recommendation**: Prioritize frontend-updates merge (unblocks UI improvements), then address deprecated MySQL functions (infrastructure stability), then research EngineAPI strategy (long-term planning).
