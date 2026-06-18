---
title: "Establish frontend-updates as Main Production Branch"
status: backlog
priority: HIGH
type: MAINTENANCE
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "1 week"
---

# Task: Establish frontend-updates as Main Production Branch

## Problem
DevOps reports the production VM is currently pointing to the `frontend-updates` branch (not `master`). The `frontend-updates` branch should be renamed/promoted to `main` to make it the official production branch. Before promotion, the branch needs comprehensive testing and review to ensure:

- All frontend changes render correctly
- LDAP login flow still works with new CSS/HTML
- Bootstrap 5 integration doesn't break existing functionality
- Docker configuration is production-ready
- Legacy code cleanup (removed files) didn't break dependencies

## Scope
**Current situation:**
- DevOps has production VM pointing to `frontend-updates` branch (de facto main branch)
- `frontend-updates` contains 120+ commits of UI improvements, CSS updates, Docker improvements
- `master` branch is 4 commits ahead of `frontend-updates` (legacy state)
- Repository needs explicit promotion of `frontend-updates` to `main` status

**Git workflow**:
1. Rename `frontend-updates` → `main` (or set as default branch if GitHub supports)
2. Archive or delete `master` branch after verification
3. Update CI/CD pipelines to pull from `main`
4. Document migration for other developers

**Testing requirements** (before promotion):
1. Visual regression testing (login page, error pages, responsive design)
2. Functional testing (form submission, LDAP authentication, redirect handling)
3. Docker build and startup verification
4. Database initialization and connection testing
5. Cross-browser compatibility (Chrome, Firefox, Safari)
6. Code review of entrypoint.sh and configuration changes

## Success Criteria
- [ ] `frontend-updates` branch tested and verified as stable
- [ ] Branch renamed to `main` or set as default branch in GitHub
- [ ] `master` branch archived/deleted (after backup)
- [ ] CI/CD pipelines updated to pull from `main`
- [ ] All frontend pages render without CSS/JS errors
- [ ] Login form submits successfully
- [ ] LDAP authentication works with new styles
- [ ] Docker build completes without errors
- [ ] Health checks pass in docker-compose
- [ ] Database initializes from SQL files
- [ ] No console errors in browser dev tools
- [ ] Responsive design works on mobile (375px+)
- [ ] Code review approved
- [ ] Production deployment verified

## Related
- Application guide: wvulibraries_authentication.md
- Docker modernization (MySQL 5.7)
- Security audit before production deployment

## Notes
- **Stop condition**: If login page has regressions, do not promote until fixed
- Removed legacy modules (alumni, residentBorrowers, etc.) — verify no external dependencies still reference them
- Bootstrap version jump may need CSS customization
- Docker base image (php:8.3-apache) is current; verify Apache config compatibility
- **Git workflow**: Consult with DevOps on branch promotion process (GitHub default branch change vs. local rename)
- **Backup**: Keep `master` branch for reference or archive to `archive/master-legacy` before deletion
