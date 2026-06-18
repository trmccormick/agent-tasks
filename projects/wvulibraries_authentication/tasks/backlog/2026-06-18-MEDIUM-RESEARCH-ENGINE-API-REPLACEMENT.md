---
title: "EngineAPI End-of-Life: Authentication is the Only Remaining User"
status: backlog
priority: MEDIUM
type: RESEARCH
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "1-2 weeks"
---

# Task: EngineAPI End-of-Life: Authentication is the Only Remaining User

## Problem
EngineAPI is an internal WVU Libraries template engine framework (88 PHP files, 30+ modules) that is no longer actively maintained. Strategic context:

- **Authentication**: Currently the ONLY application using EngineAPI
- **MFCS**: Separate legacy application (unrelated to EngineAPI) being retired; data migrating to WVU Knapsack via extraction process
- **EngineAPI status**: No other active dependents; once Authentication migrates off, framework can be fully retired

**This creates a clear deprecation path:**
1. Migrate Authentication away from EngineAPI (Phase 1)
2. Decommission EngineAPI entirely (Phase 2) — no other apps to support
3. Retire EngineAPI codebase as dead code (Phase 3)

This is no longer an open-ended "should we stay or go?" question — it's "how and when do we migrate" when Authentication is the only user.

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
- **Strategic clarity**: Authentication is the ONLY remaining EngineAPI user (no other active dependents)
- **MFCS context**: Separate legacy application (unrelated to EngineAPI); being retired with data extracted to WVU Knapsack
  - MFCS is NOT EngineAPI-dependent — it's a completely separate application replacement
  - Knapsack setup: https://github.com/wvulibraries/wvu_knapsack (already in agent-tasks)
  - See agent guide: `agent_project_guides/wvulibraries_knapsack.md`
- **Clear deprecation path**: Once Authentication is off EngineAPI, EngineAPI can be fully retired (no other apps to support)
- **Stakeholders**: Library Systems Office (libsys@mail.wvu.edu), DevOps, EngineAPI maintainer (if one exists)
- This is not a "stay or go" question anymore — it's a "how and when to migrate" question
- Consider: Can we build a temporary abstraction layer to decouple Authentication from EngineAPI incrementally?
