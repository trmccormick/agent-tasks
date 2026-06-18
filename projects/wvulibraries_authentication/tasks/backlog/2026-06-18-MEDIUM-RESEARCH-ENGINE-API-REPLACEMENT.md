---
title: "EngineAPI End-of-Life: Authentication is Last Remaining User"
status: backlog
priority: MEDIUM
type: RESEARCH
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "1-2 weeks"
---

# Task: EngineAPI End-of-Life: Authentication is Last Remaining User

## Problem
EngineAPI is an internal WVU Libraries template engine framework (88 PHP files, 30+ modules) that is no longer actively maintained. Strategic context:

- **Only 2 applications used EngineAPI historically**: Authentication and MFCS
- **MFCS status**: Being replaced by WVU Knapsack (data extraction only, not staying on EngineAPI)
- **Authentication status**: Is the LAST remaining EngineAPI user
- **Framework obsolescence**: Uses deprecated mysql_* functions, no PSR compliance, no modern architecture

**This creates a clear deprecation path:**
1. Migrate Authentication away from EngineAPI (Phase 1)
2. Decommission EngineAPI entirely (Phase 2)
3. Retire both EngineAPI and MFCS as dead code (Phase 3)

This is no longer an open-ended "should we stay or go?" question — it's "how and when do we migrate off?"

## Scope
**Research questions**:
1. What are the core EngineAPI services Authentication relies on?
   - Template system (load, include, localVars)
   - LDAP login module
   - Database abstraction layer (mysql wrapper)
   - Session management
   - CSRF token handling
   - ACL (access control lists)

2. What are viable replacement architectures?
   - **Option A**: Standalone PHP (Slim or similar) — minimal dependency
   - **Option B**: Laravel 11+ — modern framework, larger ecosystem
   - **Option C**: Symfony — more heavyweight, enterprise-ready
   - **Option D**: Rails/Django + API — complete rewrite, modern ops

3. Migration effort estimate for each option
4. Timeline and phasing (can it be done in 2 quarter sprints?)
5. Operational impact (downtime needed? gradual migration?)

**Research deliverable**:
- Summary of EngineAPI's core functionality used by Authentication
- Pros/cons of each replacement architecture
- Migration effort estimate (in weeks/person-months)
- Recommended path based on effort vs. modernization goals
- Phased rollout plan (Phase 1: MVP, Phase 2: full migration, Phase 3: EngineAPI retirement)

## Success Criteria
- [ ] EngineAPI dependency audit completed
- [ ] 3-4 replacement options documented with cost/benefit
- [ ] Migration effort estimated for each option
- [ ] Leadership alignment on recommended path
- [ ] Phased implementation roadmap created
- [ ] Timeline identified (target completion date)
- [ ] Dependencies documented (what else needs to change?)

## Related
- Deprecated MySQL functions task (short-term maintenance)
- Docker modernization (MySQL 5.7 → 8.0)
- Phase 2 work (if staying on EngineAPI): systematic modernization

## Notes
- **Strategic clarity**: Authentication is the LAST remaining EngineAPI user (MFCS transitioning to Knapsack, not staying on EngineAPI)
- **Clear deprecation path**: Once Authentication is off EngineAPI, EngineAPI can be fully retired (no longer supporting multiple apps)
- **Stakeholders**: Library Systems Office (libsys@mail.wvu.edu), DevOps, EngineAPI maintainer (if one exists)
- **MFCS context**: Not a blocker — MFCS is being replaced by Knapsack for digital collections (data extraction only)
- This is not a "stay or go" question anymore — it's a "how and when to migrate" question
- Consider: Can we build a temporary abstraction layer to decouple Authentication from EngineAPI incrementally?
