---
title: "EngineAPI Replacement Options: Research (Backup to Distillation Approach)"
status: backlog
priority: LOW
type: RESEARCH
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "1-2 weeks"
sequence: 99
blocked_by:
  - "2026-06-18-HIGH-IMPLEMENTATION-ENGINE-API-DISTILLATION"
notes: "BACKUP TASK: Only start this if distillation task is found to be architecturally infeasible. Do not start unless distillation is blocked."
---

# Task: EngineAPI Replacement Options: Research (Backup to Distillation Approach)

## Problem
EngineAPI is an internal WVU Libraries template engine framework (88 PHP files, 30+ modules) that is no longer actively maintained. Strategic context:

- **Authentication**: Currently the ONLY application using EngineAPI
- **MFCS**: Separate legacy application (unrelated to EngineAPI) being retired; data migrating to WVU Knapsack via extraction process
- **EngineAPI status**: No other active dependents; once Authentication migrates off, framework can be fully retired

**Primary approach: Extract & Distill** (see separate task: `2026-06-18-HIGH-IMPLEMENTATION-ENGINE-API-DISTILLATION.md`)
- Extract only what Authentication needs from EngineAPI
- Remove EngineAPI entirely
- Maintain as lightweight, purpose-built module (preferred long-term strategy)

**Secondary approach: Framework Migration** (this task — research if distillation proves impractical)
- Full rewrite using external framework (Slim, Laravel, Symfony, etc.)
- Use only if EngineAPI has unexpected architectural entanglement
- More complex, requires heavier operational footprint

This is no longer an open-ended "should we stay or go?" question — it's "how and when do we remove EngineAPI" when Authentication is the only user and can be distilled to minimal code.

## Scope
**Research questions** (if EngineAPI distillation is not feasible):

1. What are the core EngineAPI services Authentication relies on?
   - Template system (load, include, localVars)
   - LDAP login module
   - Database abstraction layer (mysql wrapper)
   - Session management
   - CSRF token handling
   - ACL (access control lists)

2. What are viable FRAMEWORK replacement architectures?
   - **Option A**: Standalone PHP (Slim or similar) — minimal dependency
   - **Option B**: Laravel 11+ — modern framework, larger ecosystem
   - **Option C**: Symfony — more heavyweight, enterprise-ready
   - **Option D**: Rails/Django + API — complete rewrite, modern ops

3. Migration effort estimate for each option (vs. distillation cost)
4. Timeline and phasing (can it be done in 2 quarter sprints?)
5. Operational impact (downtime needed? gradual migration?)

**Research deliverable** (Backup Plan):
- IF distillation proves architecturally infeasible: document replacement frameworks
- Pros/cons of each architecture as alternative to distillation
- Migration effort estimate (in weeks/person-months)
- Recommended fallback path based on effort vs. modernization goals
- Phased rollout plan (if needed)

## Success Criteria
**ONLY if EngineAPI Distillation (primary task) is NOT feasible**:
- [ ] EngineAPI dependency audit completed (why distillation failed)
- [ ] 3-4 framework replacement options documented with cost/benefit
- [ ] Migration effort estimated for each option
- [ ] Comparison vs. distillation approach documented
- [ ] Leadership alignment on recommended fallback path
- [ ] Phased implementation roadmap created (if needed)
- [ ] Timeline identified (target completion date)
- [ ] Dependencies documented (what else needs to change?)

**If distillation succeeds**: This task is deprioritized (no longer needed)

## Related
- **PRIMARY**: EngineAPI Distillation task (`2026-06-18-HIGH-IMPLEMENTATION-ENGINE-API-DISTILLATION.md`) — preferred approach
- Deprecated MySQL functions task (short-term maintenance)
- Docker modernization (MySQL 5.7 → 8.0)
- Phase 2 work (if framework migration chosen): systematic modernization

## Notes
- **STRATEGY SHIFT**: Distillation (extract minimal code, remove EngineAPI) is NOW the preferred long-term approach
  - Rationale: Authentication is simple (LDAP + temp account lookup + sessions); doesn't need a heavy framework
  - Long-term maintainability: < 500 lines of distilled PHP vs. adding another framework dependency
- **This task is SECONDARY**: Only do this research if distillation proves architecturally infeasible
- **Strategic clarity**: Authentication is the ONLY ACTIVE remaining EngineAPI user
- **MFCS context**: Legacy application using EngineAPI; no longer updated; being retired
  - MFCS does use EngineAPI (inherited from legacy codebase)
  - MFCS is NOT actively maintained — data extraction to WVU Knapsack already happening
  - Data extraction process is independent/unrelated to Authentication/EngineAPI decisions
- **Stakeholders**: Library Systems Office (libsys@mail.wvu.edu), DevOps
- **Operational simplicity**: Keeping Authentication lightweight (distillation) is preferred to adding new framework overhead
