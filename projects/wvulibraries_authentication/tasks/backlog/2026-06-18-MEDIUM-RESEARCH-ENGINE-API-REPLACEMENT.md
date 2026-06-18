---
title: "EngineAPI End-of-Life Assessment and Replacement Strategy"
status: backlog
priority: MEDIUM
type: RESEARCH
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "2 weeks"
---

# Task: EngineAPI End-of-Life Assessment and Replacement Strategy

## Problem
The Authentication system is built on **EngineAPI**, an internal WVU Libraries template engine framework (88 PHP files, 30+ modules). EngineAPI is minimally maintained and was designed for legacy PHP 4/5 applications. 

Current state creates risk:
- **Code debt**: Uses deprecated mysql_* functions throughout
- **No active maintenance**: Last EngineAPI commit in repo is 2+ years old
- **Framework obsolescence**: No PSR compliance, no Composer support, no modern architecture
- **Replacement uncertainty**: Unclear if Authentication should stay on EngineAPI or migrate to modern framework
- **Other WVU Libraries apps depend on it**: Changes to EngineAPI could break other services

## Scope
**Assessment questions**:
1. What other WVU Libraries applications depend on EngineAPI?
2. What is the EngineAPI GitHub repository status? (active? maintained? archived?)
3. What is the replacement timeline/strategy from WVU Libraries leadership?
4. Should Authentication migrate to modern framework (Laravel, Symfony, Rails) or stay on EngineAPI?
5. What is the cost of maintaining EngineAPI vs. replacement effort?

**Research deliverable**:
- Summary of EngineAPI status and maintenance burden
- List of dependent applications
- Recommendation: stay on EngineAPI (with modernization), or migrate to replacement
- Rough timeline and effort estimate for recommended path

## Success Criteria
- [ ] EngineAPI repository status documented
- [ ] Dependent applications identified
- [ ] Leadership alignment on long-term strategy
- [ ] Written recommendation with cost/benefit analysis
- [ ] Roadmap created for next steps (3-6 month horizon)

## Related
- Deprecated MySQL functions task (short-term maintenance)
- Docker modernization (MySQL 5.7 → 8.0)
- Phase 2 work (if staying on EngineAPI): systematic modernization

## Notes
- **Stakeholder**: Library Systems Office, libsys@mail.wvu.edu
- This is a strategic decision that affects multiple WVU Libraries projects
- Recommend surveying other EngineAPI users before making changes to core engine
- If migrating away from EngineAPI, plan for 3-6 month transition
