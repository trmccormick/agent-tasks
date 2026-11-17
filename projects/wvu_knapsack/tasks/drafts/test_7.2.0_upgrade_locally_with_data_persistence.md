# Task: Test 7.2.0 Upgrade Locally with Data Persistence

**Project:** WVU Knapsack  
**Type:** Testing / Validation  
**Priority:** High  
**Assigned to:** Qwen  
**Created:** 2026-11-17  
**Status:** Ready for Execution  
**Depends On:** `investigate_gemfile_lock_hykudev_reboots.md` (investigation findings)

---

## Objective

Verify that switching to the Hyku 7.2.0 (or 7.2.1) upgrade branch locally **preserves imported data** and keeps it queryable in the UI. If successful on local, replicate on hykudev so guided imports can be tested on the new version before updating production.

## Context

**Current State:**
- Local has imported test data (visible in screenshot from earlier session)
- 7.2.0 upgrade branch has not been tested with real data persistence
- Goal is to validate data safety before promoting to hykudev/prod

**Why This Matters:**
- Hyku 7.2.0 has schema/model changes (Flexible metadata framework)
- Database migrations must run correctly
- Solr index must be rebuilt or reindexed
- Data must remain queryable after branch switch

## Specific Test Scenarios

1. **Data Persistence During Branch Switch**
   - Switch from main (7.1.3) to 7.2.0 branch
   - Container restarts with 7.2.0 code
   - **Question:** Are items still visible in Rails console? (Item.count, Item.first.title)
   - **Question:** Are items visible in UI? (/my/works page)

2. **Solr Index Status**
   - After branch switch, is Solr index populated?
   - If empty, can reindexing succeed without errors?
   - Are facets/search working?

3. **UI Functionality**
   - Do imported items display with correct metadata?
   - Do any partials fail to render? (e.g., ActionView::MissingTemplate errors)
   - Do search results show up?
   - Do facet filters work?

4. **Gem Conflicts During Build**
   - Does `docker compose build --no-cache` succeed?
   - If gem conflicts occur, does the backup Gemfile.lock resolve it?
   - Capture any resolution errors for the investigation task

## Detailed Test Procedure

### Phase 1: Baseline Documentation
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack

# 1. Record current state (7.1.3/main)
git branch -v
git log --oneline -1

# 2. Count items in database
docker compose exec web rails c
> Item.count
> Item.pluck(:id).sample(3)  # Sample IDs
> Item.first(3).map { |i| {id: i.id, title: i.title} }
> exit

# 3. Test UI visibility
# - Navigate to http://localhost:3000/my/works
# - Screenshot or describe what's visible
# - Note any items you can see

# 4. Test search/Solr
docker compose exec web rails c
> Blacklight.repository.search({q: '*:*'}).fetch(:response).fetch(:numFound)
> exit
```

### Phase 2: Switch to 7.2.1 Branch
```bash
# 5. Stash any uncommitted changes
git stash

# 6. Switch branch (data persists in Postgres volume)
git checkout feature/hyku-v7.2.1-upgrade

# 7. Update submodule
git submodule update --init --recursive

# 8. If you have the backup, optionally pre-stage it
# (or let the build attempt fresh resolution first to capture errors)
# cp /path/to/Gemfile.lock.backup hyrax-webapp/Gemfile.lock

# 9. Rebuild containers
docker compose down --volumes --remove-orphans
docker compose build --no-cache
# CAPTURE ALL OUTPUT — save to build.log if errors occur
sh up.sc.local.sh
```

### Phase 3: Monitor Container Startup
```bash
# 10. Watch web container logs
docker compose logs -f web

# 11. Wait for "Listening on" message
# 12. If errors occur during boot, check:
# - Are gems missing/conflicting? (look for NameError, MissingConstantError)
# - Are migrations failing?
# - Are initializers crashing?
# - Save full logs: docker compose logs web > web_7.2.1_logs.txt
```

### Phase 4: Data Persistence Check
```bash
# 13. Verify items still exist
docker compose exec web rails c
> Item.count
# COMPARE with baseline — should be SAME
> Item.first(3).map { |i| {id: i.id, title: i.title} }
# COMPARE with baseline — same items?
> exit

# 14. Check if Solr is populated
docker compose exec web rails c
> response = Blacklight.repository.search({q: '*:*'})
> response.fetch(:response).fetch(:numFound)
# COMPARE with baseline — should be SAME or close
> exit
```

### Phase 5: UI Verification
```bash
# 15. Access UI
# - Navigate to http://localhost:3000/my/works
# - Are items visible?
# - Try clicking on an item to view details
# - Try search box (search for a known item title)
# - Try facet filters

# 16. Check for errors
# - Open browser console (F12) for JavaScript errors
# - Check server logs: docker compose logs web | grep -i error
# - Look for ActionView::MissingTemplate errors
```

### Phase 6: Solr Reindexing (if needed)
```bash
# 17. If Solr is empty but items exist:
docker compose exec web rails c
> Hyrax::Indexer.index_remote_objects
# Monitor this — should complete without errors
> exit

# 18. Verify Solr populated after reindex
docker compose exec web rails c
> Blacklight.repository.search({q: '*:*'}).fetch(:response).fetch(:numFound)
> exit
```

### Phase 7: Error Capture & Documentation
```bash
# 19. If errors occur, capture:
docker compose logs web > /tmp/7.2.1_web_errors.log
docker compose logs solr > /tmp/7.2.1_solr_errors.log
docker compose logs db > /tmp/7.2.1_db_errors.log

# 20. If gem build failed, capture:
# docker compose build --no-cache 2>&1 | tee /tmp/7.2.1_build_errors.log

# 21. Upload logs/screenshots to session for analysis
```

## Success Criteria

✅ **Task passes if:**
1. Container builds successfully (or uses backup Gemfile.lock to stabilize)
2. Web container boots without crashing
3. Items exist in Postgres after branch switch (Item.count > 0)
4. Items are visible in UI (/my/works page)
5. Solr index is populated (or reindexing succeeds)
6. Search/facet queries work
7. No ActionView::MissingTemplate errors

❌ **Task fails if:**
1. Container fails to build (gem conflicts unresolvable)
2. Web container crashes on startup (NameError, uninitialized constant)
3. Items are lost after branch switch
4. Items exist but Solr is empty AND reindexing fails
5. UI shows "MissingTemplate" errors for key partials
6. Search/facet queries fail

## Expected Outcomes

### If Successful
- Document: "7.2.1 upgrade branch preserves data and UI functionality"
- Provide step-by-step procedure for replicating on hykudev
- Begin planning hykudev test with guided imports
- Plan prod update timeline

### If Partial Success (some errors but recoverable)
- Document specific errors and workarounds
- Note if backup Gemfile.lock was required
- Identify which fixes from investigation task are still needed
- Plan follow-up work before hykudev testing

### If Unsuccessful
- Document exact failure point (build, boot, data loss, UI rendering)
- Share logs with investigation task to correlate with gem conflicts
- Plan recovery steps
- Decide if more work needed before attempting hykudev

## Branches to Test

**Primary:** `feature/hyku-v7.2.1-upgrade`
- Includes GA4 security fix
- Includes PR #3369 facet limiting
- More fixes than 7.2.0
- **Recommended to test first**

**Secondary:** `feature/hyku-v7.2.0-upgrade`
- Initial 7.2.0 attempt
- Useful to understand what's improved in 7.2.1
- Only test if 7.2.1 issues warrant comparison

## Rollback Plan

If testing is catastrophic:
```bash
# 1. Stop containers
docker compose down

# 2. Reset to main
git checkout main
git submodule update --init --recursive

# 3. Rebuild clean
docker compose build --no-cache
sh up.sc.local.sh
```

**Data should persist** because Postgres volume is not removed.
If needed, database snapshots may be available from earlier sessions.

## Resources

- **Test plan details:** `/Users/tam0013/.copilot/session-state/4e01e37b-32bd-427f-b787-c7a4cd5ff972/files/TEST_PLAN_7.2.0_DATA_PERSISTENCE.md`
- **Gemfile.lock backup:** `/Users/tam0013/.copilot/session-state/4e01e37b-32bd-427f-b787-c7a4cd5ff972/files/Gemfile.lock.backup` (checksum: `7b4ed53d782f8f0103bcd2537d0d6052`)
- **Wiki docs:** `/Users/tam0013/Documents/git/wvu_knapsack/docs/wiki/operations/` (testing_best_practices, local_stack_car_environment_mismatch, etc.)
- **Investigation task:** `investigate_gemfile_lock_hykudev_reboots.md` (run first to understand gem conflicts)

## Notes for Qwen

- **Coordinate timing:** This should run AFTER investigation task findings are available
- **Data is precious:** Use care during branch switches; have rollback plan ready
- **Document everything:** Even partial success/failure helps de-risk hykudev testing
- **If Solr empties:** This is recoverable with reindexing — don't panic
- **If UI errors:** Capture full error messages — may be guide-able with wiki fixes
- **Report findings:** Even negative results (data loss, build failures) are valuable for planning
