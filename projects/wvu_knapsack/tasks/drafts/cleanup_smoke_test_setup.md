# Cleanup: Remove Broken Smoke Test Setup

**Status:** Deferred (Future Work)  
**Priority:** Low  
**Created:** 2026-11-18  
**Related Issue:** Data Persistence and IIF Viewer HTTPS Requirement

---

## Background

During the 7.2.0 upgrade validation (Nov 2026), we discovered that the smoke test setup (`docker-compose.prod.yml`) cannot properly display records because the IIF (Internet Image Interoperability) viewer requires HTTPS.

**Key Facts:**
- Production (`hykudev`) uses Stackcar + Traefik to provide HTTPS via self-signed certificates in dev mode
- The smoke test HTTP-only setup cannot serve IIF images over HTTP
- Records display blank thumbnails and fail to load properly in the smoke test environment
- The 7.2.0 upgrade was successfully validated on the main dev setup (stackcar + https), proving the upgrade itself is safe
- The smoke test setup limitation does **not block** production upgrade to 7.2.0

**Decision:** Remove the broken smoke test setup and rely on:
1. Main dev environment for local testing (stackcar + HTTPS)
2. `hykudev` VM for production-like testing before final prod update

---

## Task Description

Remove the smoke test docker compose configuration and any related documentation that references it as a viable testing path.

### Files to Remove/Modify

1. **Remove:**
   - `docker-compose.prod.yml` (or mark as deprecated)
   - Any associated smoke test scripts or configuration

2. **Update:**
   - `docs/wiki/known_issue_smoke_test_https_iif_viewer.md` → Can be archived
   - Any testing procedures that reference smoke test → Update to use main dev setup only

3. **Document:**
   - Create a brief note explaining why the smoke test was removed
   - Explain the standard testing workflow: local dev (stackcar) → hykudev VM → production

### Success Criteria

- [ ] Smoke test docker compose removed or deprecated
- [ ] No broken references in wiki or documentation
- [ ] Testing procedures clearly state: use stackcar (HTTPS) for local dev, not smoke test
- [ ] Commit message explains the removal decision

---

## Why This Is Deferred

The smoke test setup doesn't block current work:
- 7.2.0 upgrade is validated and working on the actual dev setup (with HTTPS)
- Production upgrade can proceed when ready, using hykudev for final validation
- Removing this cleanup task from today frees up capacity for the production upgrade planning

---

## Implementation Notes

**When ready to implement:**

1. Verify no other team members are using the smoke test setup
2. Document the removal (commit message should reference this task)
3. Update the team on the testing workflow:
   - Local development: Use stackcar + HTTPS (current main setup)
   - Production-like testing: Use hykudev VM (before final prod update)
   - Production: Deploy to hykudev VM then promote to prod
4. Archive `docs/wiki/known_issue_smoke_test_https_iif_viewer.md` to a `docs/wiki/archived/` folder

---

## Related Files

- `docs/wiki/known_issue_smoke_test_https_iif_viewer.md` — Detailed explanation of the HTTPS limitation
- `docs/wiki/test_7.2.0_upgrade_data_persistence.md` — Proof that 7.2.0 works on the main setup
- Previous session checkpoints — Full context on upgrade validation
