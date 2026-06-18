---
title: "Documentation Audit: Code Comments and Architecture Guide"
status: backlog
priority: LOW
type: MAINTENANCE
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "1 week"
---

# Task: Documentation Audit: Code Comments and Architecture Guide

## Problem
The Authentication codebase lacks documentation:

- EngineAPI modules have minimal/no comments
- LDAP flow is undocumented (developers guess at implementation)
- Database schema not documented
- Configuration parameters (env file, default.php) unexplained
- EngineAPI template system not explained
- New developers take weeks to understand flow

Current state:
- Only inline README in some files
- No architecture guide
- Comments often outdated or misleading
- Configuration options unknown

## Scope
**Documentation needed**:
1. **Architecture guide** (markdown)
   - System overview diagram (components: web, LDAP, DB, EngineAPI)
   - Data flow (login request -> LDAP -> session)
   - Module responsibilities
   - Key files and their purpose

2. **LDAP authentication documentation**
   - How login.php routes to ldap.php
   - What securityUserCheck() does
   - LDAP bind format and security
   - Group extraction logic

3. **EngineAPI template system guide**
   - How templates are loaded
   - Local variables system
   - Template inheritance
   - Common patterns and gotchas

4. **Database schema guide**
   - Tables and their purpose
   - Important queries
   - Session table structure

5. **Configuration reference**
   - env.auth and env.dev.auth parameters
   - default.php configuration options
   - LDAP domain configuration

6. **Inline code comments** (selective)
   - Complex functions (LDAP binding, group extraction)
   - Non-obvious logic
   - Maintain existing comments

## Success Criteria
- [ ] Architecture guide written (3-5 pages)
- [ ] LDAP flow documented
- [ ] Configuration reference complete
- [ ] Key files have module-level PHPDoc comments
- [ ] New developer onboarding guide created
- [ ] Published in project README or docs/ folder

## Related
- EngineAPI assessment (for architecture clarity)
- Code quality improvements

## Notes
- This is lower priority maintenance (doesn't fix bugs or add features)
- Focus on most-asked questions from new developers
- Keep documentation close to code (prefer markdown in repo)
- Link to this guide from project README
