# Task: Investigate Why Gemfile.lock Preservation Prevents hykudev Reboots

**Project:** WVU Knapsack  
**Type:** Investigation / Root Cause Analysis  
**Priority:** High  
**Assigned to:** Qwen  
**Created:** 2026-11-17  
**Status:** Ready for Execution

---

## Objective

Determine the specific gem conflicts that caused hykudev to reboot continuously during the Hyku 7.2.0 upgrade testing, and document why copying Gemfile.lock from production resolved the issue.

## Context

During the 7.2.0 upgrade testing on hykudev:
- **Without prod's Gemfile.lock:** Container kept crashing/rebooting
- **After copying Gemfile.lock from prod:** Container stayed stable
- **Same submodule SHA** as production, but resolution differed

## Specific Questions to Answer

1. **What exact gem versions conflict during fresh `bundle install` on 7.2.0?**
   - Run fresh bundle resolution (without lockfile) on 7.2.0 branch
   - Capture any resolution errors or incompatibilities
   - Compare versions with production's known-working set

2. **Why do these conflicts cause container reboots?**
   - Which gems have breaking API changes?
   - Which initializers crash due to missing constants/methods?
   - Document the boot sequence failure

3. **Why does production's Gemfile.lock solve it?**
   - Identify which specific pinned versions avoid the conflict
   - Explain how prod's historical rollout avoided this (they didn't start fresh)

4. **What's the root cause pattern?**
   - Is this a Hyku 7.2.0 issue (needs upstream PR)?
   - Is this an environmental divergence (local/VM resolution differs)?
   - Is this a knapsack-specific gem constraint issue?

## Deliverables

1. **Document: `gemfile_lock_hykudev_reboot_analysis.md`**
   - Exact gem versions that conflict
   - Specific error messages from failed boots
   - Which gems have breaking changes in 7.2.0
   - Why production's set works (specific versions)

2. **Comparison Table:**
   - Conflicting gem names
   - Version attempted (fresh resolution)
   - Version in prod's Gemfile.lock
   - Root cause (API change, dependency conflict, etc.)

3. **Recommendation:**
   - Should Gemfile.lock be committed to track dependency state?
   - Should we pin specific critical gems in Gemfile?
   - Should we file upstream bug with Hyku/samvera?

## Related Documentation

- [gemfile_lock_hykudev_reboot_investigation.md](/Users/tam0013/Documents/git/wvu_knapsack/docs/wiki/operations/gemfile_lock_hykudev_reboot_investigation.md) — Initial analysis (commit c8ccf24 shows NameError crash example)
- [gemfile_lock_root_cause.md](/Users/tam0013/Documents/git/wvu_knapsack/docs/wiki/operations/gemfile_lock_root_cause.md) — Docker build sequence explanation
- [Gem Version Drift](/Users/tam0013/Documents/git/wvu_knapsack/docs/wiki/operations/gem_version_drift.md) — Why versions diverge

## Resources

- **Backup Gemfile.lock:** `/Users/tam0013/.copilot/session-state/4e01e37b-32bd-427f-b787-c7a4cd5ff972/files/Gemfile.lock.backup` (checksum: `7b4ed53d782f8f0103bcd2537d0d6052`)
- **7.2.0 branch:** `feature/hyku-v7.2.0-upgrade`
- **7.2.1 branch:** `feature/hyku-v7.2.1-upgrade` (more fixes, recommended)
- **hykudev access:** Available for testing (coordinate with team for VM access)

## Procedure Outline

1. Checkout `feature/hyku-v7.2.1-upgrade` branch locally
2. Update submodule: `git submodule update --init --recursive`
3. Delete hyrax-webapp/Gemfile.lock (force fresh resolution)
4. Run `docker compose build --no-cache` and capture ALL errors
5. Compare error messages with production's successful deployment
6. Document findings in the analysis file
7. Test hypothesis: Does using prod's Gemfile.lock fix the errors?
8. Identify specific gems and versions that caused conflicts

## Success Criteria

✅ Investigation is complete when you can explain:
- The specific gem(s) that failed to resolve or were incompatible
- The exact error messages that caused reboots
- Why production avoided this issue
- What versions in the prod Gemfile.lock solve it
- Whether this is a Hyku 7.2 issue or WVU Knapsack issue

## Notes for Qwen

- You have local shell access and can run docker compose commands
- Refer to the team's testing best practices doc for environment setup
- If builds fail catastrophically, the backup Gemfile.lock is available to restore stability
- Document everything — even partial findings help narrow down the issue
- If you find this is an upstream (Hyku) bug, we may need to file a samvera issue
