# EXECUTOR HANDOFF — Batch Edit Descriptions Formatting Fix

**Copy this entire message and paste into qwen chat to dispatch Implementation Agent.**

---

You are **Implementation Agent**.

**Project**: samvera_hyku
**Task**: /Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-06-23-HIGH-BUGFIX-BATCH-EDIT-DESCRIPTIONS-FORMATTING.md

**CSS FIX STATUS**: Already implemented in hyrax.scss and compiled. Needs visual verification + targeted tests.

---

## WORKFLOW (READ IN THIS ORDER)

**STEP 1**: Read workflow
- File: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
- Why: Understand how to synthesize + report before coding

**STEP 2**: Read project guides
- File: `/Users/tam0013/Documents/git/agent-tasks/agent_project_guides/samvera_hyku.md`
- Contains: default admin credentials, testing tenant convention, Stack Car commands, multi-tenant architecture

**STEP 3**: Read task file
- File: `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-06-23-HIGH-BUGFIX-BATCH-EDIT-DESCRIPTIONS-FORMATTING.md`
- Contains: CSS fix code, exact verification steps, acceptance criteria, specs to run

---

## LOGIN CREDENTIALS (Use These Immediately)

| Field | Value |
|-------|-------|
| System Email | `admin@example.com` |
| System Password | `testing123` |
| Admin Interface | `https://admin-hyku.localhost.direct` |

**NOTE**: This is a pre-seeded system account shared across all Hyku instances. You do NOT need to create a new user.

---

## CRITICAL ARCHITECTURE REMINDER

⚠️ **ADMIN DOMAIN ≠ TENANT DOMAIN**

- Admin domain (`admin-hyku.localhost.direct`): **ONLY** for tenant creation/system config. NO repository features.
- Tenant domain (`testing-hyku.localhost.direct`): **WHERE YOU TEST**. Full repository features, batch edit, works, etc.

**If you try to access batch edit on admin domain, you will get 404 or auth errors. This is correct.**

**Always use**: `https://testing-hyku.localhost.direct` for testing batch edit features.

---

## PRIORITY 1: Visual Verification

**URL to test**: `https://testing-hyku.localhost.direct/dashboard/my/works`

**Steps**:
1. Navigate to the testing tenant works dashboard
2. If no works exist, create a test work first (10 seconds)
3. Select one or more works (checkbox)
4. Click "Edit Selected" button
5. Click "Descriptions" tab
6. **VERIFY**: All metadata labels (Creator, Contributor, Description, etc.) render HORIZONTALLY
7. **COMPARE**: Should match the horizontal layout of Add/Edit Work pages
8. **SCREENSHOT**: Capture for documentation

**What you're looking for**: Labels should NOT render vertically (one char per line). They should be on one line, left-aligned, like this:

```
Creator: [input field]
Contributor: [input field]
Description: [input field]
```

NOT like this:
```
C
r
e
a
t
o
r
:
```

---

## PRIORITY 2: Run Targeted Specs

**Command**:
```bash
cd /Users/tam0013/Documents/git/hyku
sc exec bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/features/batch_edit* spec/views/hyrax/batch_edits/ -v 2>&1 | tail -50'
```

**Expected result**: All tests pass (0 failures)

**If any fail**: Document the failure details and include in task completion notes

---

## PRIORITY 3: Move Task to Completed

After visual verification + specs pass:

```bash
cd /Users/tam0013/Documents/git/agent-tasks

# Move task file from active/ to completed/
mv projects/samvera_hyku/tasks/active/2026-06-23-HIGH-BUGFIX-BATCH-EDIT-DESCRIPTIONS-FORMATTING.md \
   projects/samvera_hyku/tasks/completed/2026-06-23-HIGH-BUGFIX-BATCH-EDIT-DESCRIPTIONS-FORMATTING.md

# Commit
git add projects/samvera_hyku/tasks/completed/
git commit -m "task: Complete batch edit descriptions formatting fix verification

- Visual verification: Labels render horizontally on testing tenant
- Specs: All batch edit tests pass
- CSS fix: Implemented in hyrax.scss, validated working"

git push origin main
```

---

## STATUS SYNTHESIS (Required Before You Begin)

**Before you start testing, create a synthesis report that includes**:
1. Summary of what you're about to test (visual verification + specs)
2. Acceptance criteria from task file
3. File locations you'll be working with
4. Expected outcomes

**Post this synthesis to the chat** before navigating to any URLs or running any commands.

---

## CRITICAL GOTCHAS

- ❌ DO NOT use admin domain for batch edit testing — 404 is expected
- ❌ DO NOT run full test suite — only `spec/features/batch_edit*` and `spec/views/hyrax/batch_edits/`
- ❌ DO NOT re-investigate the CSS fix — it's already correct and in place
- ❌ DO NOT fabricate test results — actually run the specs and verify visually
- ✅ DO create testing tenant user if needed (ask admin to create it or do it yourself via admin panel)
- ✅ DO screenshot the passing visual verification for documentation
- ✅ DO read the README.md EXECUTOR role section first

---

## FILES YOU'LL REFERENCE

| File | Purpose |
|---|---|
| `/Users/tam0013/Documents/git/agent-tasks/README.md` | Workflow + role definition |
| `/Users/tam0013/Documents/git/agent-tasks/agent_project_guides/samvera_hyku.md` | Architecture, credentials, commands |
| `/Users/tam0013/Documents/git/agent-tasks/projects/samvera_hyku/tasks/active/2026-06-23-HIGH-BUGFIX-BATCH-EDIT-DESCRIPTIONS-FORMATTING.md` | Task details, CSS code, acceptance criteria |
| `/Users/tam0013/Documents/git/hyku/app/assets/stylesheets/hyrax.scss` | Where CSS fix is located (lines 53-73) |

---

## WHAT'S ALREADY DONE

✅ CSS fix implemented in hyrax.scss (lines 53-73)
✅ Asset precompile validated (0 errors)
✅ Container restarted with CSS changes
✅ Multi-tenant architecture documented
✅ Blocker resolved (admin ≠ tenant clarified)

**Your job**: Verify it works visually + run specs + move task to completed

---

## WHEN TO ESCALATE

Stop and escalate immediately if:
1. Labels still render vertically after visual verification (CSS fix not working)
2. Specs fail more than 2 times despite multiple runs
3. Need to make code changes beyond what's in the task file
4. Tenant domain returns errors other than 401 auth (routing problems)

---

**Ready to start? Read README.md EXECUTOR section first, then create synthesis report in chat.**
