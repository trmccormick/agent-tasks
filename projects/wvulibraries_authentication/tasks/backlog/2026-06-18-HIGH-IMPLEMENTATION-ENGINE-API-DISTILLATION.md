---
title: "EngineAPI Distillation: Extract Authentication Core & Remove Framework Dependency"
status: backlog
priority: MEDIUM
type: IMPLEMENTATION
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "2-3 weeks"
sequence: 6
blocked_by:
  - "2026-06-18-HIGHEST-SETUP-PHPUNIT-INTEGRATION"
  - "2026-06-18-HIGH-FEATURE-LOGIN-TEST-SUITE"
  - "2026-06-18-HIGH-MAINTENANCE-DEPRECATED-MYSQL-FUNCTIONS"
  - "2026-06-18-MEDIUM-MAINTENANCE-SECURITY-AUDIT"
branch: "refactor/authentication-modernization"
branch_strategy: "All work on single refactor branch; distilled code must be < 500 lines; full test suite must pass before merge"
---

# Task: EngineAPI Distillation: Extract Authentication Core & Remove Framework Dependency

## Problem
Authentication application currently depends on EngineAPI (88 PHP files, 30+ modules) — a full template engine framework. However, Authentication's actual needs are minimal:
1. LDAP authentication against WVU-AD
2. Temporary account database lookup (library visitor access)
3. Session management + CSRF tokens
4. ACL for network resource permissions

**Strategy**: Extract only what's needed, distill into a minimal standalone module, remove EngineAPI entirely.

**Preferred long-term approach**: Lightweight, purpose-built Authentication module is more maintainable than migrating to another heavy framework or staying on unmaintained EngineAPI.

## Scope

### Phase 1: Extract Core Functions
**Audit & Extract**:
1. LDAP authentication logic
   - Current: `src/phpincludes/engineAPI/engine/login/ldap.php`
   - Extract: `ldapLogin()`, `securityUserCheck()`, `ldapConnect()`, `ldapDisconnect()`, `getGroups()`
   - Dependencies: PHP LDAP extension, WVU-AD server config

2. Temporary account database lookups
   - Current: Database schema in `authentication.sql`
   - Extract: Query functions for temp account validation
   - Dependencies: MySQL/MySQLi connection

3. Session management
   - Current: EngineAPI session wrapper
   - Extract: PHP session handling + user context storage
   - Dependencies: PHP session functions (standard library)

4. CSRF token handling
   - Current: EngineAPI token generation/validation
   - Extract: Token functions (standard CSRF pattern)
   - Dependencies: Session, random byte generation (standard)

5. ACL/permission checking
   - Current: EngineAPI acl.php module
   - Extract: Permission validation logic
   - Dependencies: Database queries

6. Login form rendering
   - Current: EngineAPI template system + HTML
   - Extract: Simple PHP templates or HTML forms
   - Dependencies: None (static HTML/CSS)

**Output**: Single distilled PHP module (or 2-3 focused files) with only essential functionality

### Phase 2: Remove EngineAPI References
1. Replace all EngineAPI includes with extracted functions
2. Remove EngineAPI routing layer
3. Replace template engine calls with simple PHP includes
4. Update configuration paths
5. Remove unused EngineAPI files from production

### Phase 3: Testing & Validation
- [ ] LDAP login works with WVU-AD
- [ ] Temporary account lookup functional
- [ ] Session management persists across requests
- [ ] CSRF tokens validated correctly
- [ ] ACL permissions enforced
- [ ] Docker build succeeds
- [ ] No EngineAPI files in web root

## Success Criteria
- [ ] Authentication functions extracted into standalone module(s)
- [ ] EngineAPI completely removed from application
- [ ] All LDAP login flows working (single/multi-factor if applicable)
- [ ] Temporary account access verified
- [ ] Session persistence verified
- [ ] Production Docker image builds without EngineAPI
- [ ] No broken dependencies in deployment
- [ ] Code is < 500 lines PHP (distilled, not bloated)
- [ ] Configuration centralized (env-based or config.php)
- [ ] Documentation: "EngineAPI Removal" in application guide

## Implementation Notes
- **Start with LDAP module**: It's the most complex and self-contained
- **Database queries**: Convert any deprecated mysql_* functions to MySQLi prepared statements
- **Simplify routing**: Remove EngineAPI engine routing; use simple PHP entry point
- **Configuration**: Consolidate into single config.php or env-based
- **CSS/JS**: Verify static assets load without template engine
- **Sessions**: PHP session_start() with hardened settings (httponly, secure flags)

## Related Tasks
- 2026-06-18-HIGH-MAINTENANCE-DEPRECATED-MYSQL-FUNCTIONS.md (handle database queries)
- 2026-06-18-MEDIUM-RESEARCH-ENGINE-API-REPLACEMENT.md (alternatives if distillation proves impractical)
- Frontend-Updates branch promotion (ensure EngineAPI removal doesn't break login page)

## Stop Conditions
- If extraction reveals unexpected EngineAPI dependencies (architectural entanglement) → escalate to strategist before proceeding
- If LDAP module requires features we can't easily extract → consider hybrid approach (keep minimal EngineAPI wrapper)
- If distillation complexity exceeds 3 weeks → evaluate framework migration as alternative

## Notes
- **Long-term maintainability**: Minimal code is easier to maintain than EngineAPI framework + another dependency
- **Preferred approach**: Extract → remove → simple PHP (vs. migrate → add new framework)
- **EngineAPI context**: MFCS also uses EngineAPI (legacy, not maintained); this deprecates EngineAPI entirely when complete
- **Team capacity**: This is the highest-value work for Authentication long-term (vs. chasing framework hype)
- **Stakeholders**: Library Systems Office, DevOps
- This task REPLACES full framework migration if successful; keeps code lightweight and operationally simple
