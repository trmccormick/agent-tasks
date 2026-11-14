---
status: backlog
priority: HIGH
type: architecture
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [x] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [x] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/backlog/[FILENAME].md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/[FILENAME].md \
         projects/wvulibraries_knapsack/tasks/active/[FILENAME].md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvulibraries_knapsack/tasks -name "[FILENAME].md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

---

# TASK: Simplify Facet Limiter to Clean Upstream-Compatible Solution
**Status**: BACKLOG
**Priority**: HIGH
**Type**: architecture
**Created**: 2026-09-30
**Last Updated**: 2026-09-30

---

## Local Worker Triage Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **MVP Alignment**: VALID — core Hyku/Hyrax facet behavior affects all instances
- **Action Line**: NEEDS MANUAL REVIEW — requires understanding of current merged state vs. remaining issues

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires file system access to grep/inspect current codebase and verify merged state
**Local attempts before cloud**: N/A — first dispatch
**Supervision Level**: watched carefully

---

## Context

The PR #19 (`fix/hide-type-facet-add-show-more-facets`) was merged to `main` on GitHub (commit `39b1d7c`). It includes the Collections button fix and facet pagination improvements. However, **the fix is not fully working**: three facets in the catalog view still do not show "more" links even though they have 6+ items.

The current solution uses a multi-layer approach across multiple files:
- `config/initializers/facet_limits.rb` — Sets limit on all facets via to_prepare hook
- `app/search_builders/catalog_search_builder.rb` — Extends search builder, sets Solr-level limits
- `config/initializers/catalog_search_builder.rb` — Overrides search builder class

This is overly complex and unlikely to upstream cleanly to Hyku or Hyrax.

An alternative simpler approach was proposed during session review:

```ruby
# OVERRIDE: Hyrax registers profile facets with no limit, which Blacklight reads as show every value
def limit_unlimited_facets(config)
  config.facet_fields.each_value do |facet|
    facet.limit = DEFAULT_FACET_LIMIT if facet.limit.nil? && !facet.range
  end
end

# OVERRIDE: profile facets exist only once the controller is built, so the limit is applied per instance
def initialize
  super
  CatalogControllerDecorator.limit_unlimited_facets(blacklight_config)
end
```

This approach has merit but is **incomplete** — setting `facet.limit` alone does not signal to Blacklight that more results exist. The fix needs to also request `limit + 1` from Solr.

## Current State

- ✅ PR #19 merged to GitHub main (`39b1d7c`)
- ⚠️ **Known issue**: Three catalog facets missing "more" links despite having 6+ items
- 📍 Location of known issue: User reported checking the catalog view and seeing this behavior
- 🔍 Specific facets not working: Unknown (needs investigation during task execution)

---

## Critical Information for This Task

### Credentials (if needed)
| Field | Value | Notes |
|-------|-------|-------|
| hykudev SSH | `ssh tam0013@hykudev.lib.wvu.edu` | Dev VM with testing instance |
| Test URL | `https://demo-hykudev.lib.wvu.edu/` | Demo tenant for verification |

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Blacklight "More" link heuristic
- ❌ Wrong: Setting `facet.limit = 5` alone — Blacklight thinks it has all results when Solr returns exactly 5 items
- ✅ Right: Must also set Solr query param to request `limit + 1` (e.g., `f[field].facet.limit=6`)
- Why: Blacklight's template logic checks `paginator.last_page?`. If Solr returns exactly the limit count, there's no "more" link regardless of `show_more = true`

⚠️ **GOTCHA 2**: M3 flexible-metadata facets registered late
- ❌ Wrong: Setting limits in `blacklight_config` directly — M3 profile facets are registered AFTER config is built
- ✅ Right: Must use `to_prepare` hook or per-instance override to catch late-registered facets
- Why: Hyrax loads the M3 profile dynamically; facets don't exist until after controller initialization

⚠️ **GOTCHA 3**: Current codebase may be on a diverged main
- ❌ Wrong: Assuming local main matches GitHub main
- ✅ Right: Run `git fetch && git log --oneline -3 origin/main` to verify before starting
- Why: PR #19 was merged on GitHub but local branch may have diverged

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Simplify Facet Limiter to Clean Upstream-Compatible Solution
**Status**: [backlog → active → completed]
**Date**: YYYY-MM-DD

### What I'm About to Do
[2-3 sentences: investigate current facet limiter code, identify why 3 facets miss "more" links, design a simplified single-method solution compatible with M3 flexible-schema profiles and upstream contribution targets]

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `config/initializers/facet_limits.rb` | Current facet limiter — inspect for remaining code | [pending / done] |
| `app/search_builders/catalog_search_builder.rb` | Search builder — check Solr limit logic | [pending / done] |
| `[other files as discovered]` | [description] | [pending / done] |

### Prerequisites Completed
- ✅ Verify local main is synced with origin/main
- ✅ Inspect what initializers currently exist (facet_limits.rb may have been removed in PR #19)
- ✅ Identify the 3 facets missing "more" links and their field names
- ✅ Understand why current multi-layer approach fails for these facets

### Expected Outcomes
Exact description of "done":
1. A single, clean initializer or catalog_controller override that handles facet limiting
2. Works with M3 flexible-schema profiles (catches late-registered facets)
3. Properly requests `limit + 1` from Solr to signal Blacklight for "more" links
4. Documented as potential upstream contribution pattern

### Critical Gotchas I Will Avoid
- ❌ Setting only facet.limit without Solr-level limit+1 request
- ❌ Using hardcoded field names (must work dynamically with M3 profiles)
- ❌ Assuming initializers still exist — PR #19 may have changed them

---

**SYNTHESIS COMPLETE.** Ready to proceed with investigation.
```

---

## Problem Statement

After PR #19 merged to main, the facet pagination fix is not fully working. Three facets in the catalog view do not show "more" links even though they have more than 5 items available in Solr. The current multi-layer solution (initializers + search builder override) is complex and unlikely to be upsteam-able to Hyku/Hyrax.

**Current behavior**: Catalog page shows 5 facet items but no "more" link for fields that have 6+ values
**Expected behavior**: All facets with 6+ values show exactly 5 items + a clickable "more" link

---

## Files Involved (Likely — verify during investigation)

### Primary Files — may need editing
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/controllers/catalog_controller_decorator.rb` | Main catalog config entry point | facet field registration |
| `config/initializers/facet_limits.rb` | Facet limit enforcement (may exist from PR #19) | to_prepare hook |
| `app/search_builders/catalog_search_builder.rb` | Search builder Solr params | `add_facetting_to_solr` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `_facet_limit.html.erb` in hyrax-webapp | Blacklight template that renders "more" links |
| Hyrax/CatalogController (gem) | Understand base facet registration flow |

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

### Step 1 — Investigation

```bash
# On local machine:
cd /Users/tam0013/Documents/git/wvu_knapsack

# Sync main with origin
git fetch origin
git log --oneline -3 origin/main   # Verify PR #19 merge is present

# Check what initializers exist now
ls config/initializers/facet*
ls app/search_builders/*catalog* 2>/dev/null || echo "no catalog search builder"

# Inspect current facet limiting logic (grep for key patterns)
grep -rn "facet.limit\|facet_limit\|limit_unlimited" config/ initializers/ app/

# If on hykudev, verify which facets are missing "more" links:
# ssh tam0013@hykudev.lib.wvu.edu
# cd /app/samvera/wvu_knapsack
# docker-compose exec web bash -c 'RAILS_ENV=production rails console'
# Then in Rails console:
#   CatalogController.blacklight_config.facet_fields.each { |k,v| puts "#{k}: limit=#{v.limit}, show_more=#{v.show_more}" }
```

**Deliverable**: List of the 3 facets missing "more" links with their field names and current config values. Identify which part of the chain fails (config set? search builder sends correct Solr params?).

### Step 2 — Design Simplified Solution

Draft a single method pattern inspired by the proposed approach but WITH the critical `limit + 1` fix:

```ruby
# Pseudocode for design session (verify syntax during implementation)
def limit_unlimited_facets(config)
  config.facet_fields.each_value do |facet|
    # Only apply to facets that lack a limit and are not range facets
    next if facet.limit
    next if facet.range

    facet.limit = DEFAULT_FACET_LIMIT        # Display limit (5 in UI)
    facet.query = { params: { limit: DEFAULT_FACET_LIMIT + 1 } }  # Request N+1 for "more" signal
  end
end
```

**Deliverable**: Write this as a single clean initializer or catalog_controller override. Must handle M3 flexible-schema (late registration).

### Step 3 — Implementation

Place the simplified solution in the appropriate location:
- Option A: `config/initializers/facet_limits.rb` (if it survived PR #19)
- Option B: `app/controllers/catalog_controller_decorator.rb` (override blacklight_config block)
- Option C: New file with clear name following wvu_knapsack conventions

**Rules**:
- Use `to_prepare` hook to catch late facet registration
- Must handle both Hash and non-Hash `facet_fields` config
- Must work with or without existing limit values (defensive)
- Document as potential upstream pattern in comment header

### Step 4 — Test on hykudev

```bash
# On hykudev:
cd /app/samvera/wvu_knapsack
git pull
sh up.sh

# Then verify:
# Navigate to https://demo-hykudev.lib.wvu.edu/catalog?search_field=all_fields&q=
# Check that ALL facets with 6+ items now show "more" links
```

**Acceptance Criteria**: All 3 previously broken facets now display "more" link. No regression on other working facets.

---

## Acceptance Criteria
- [ ] All catalog facets with 6+ values show "more" links (no more partial behavior)
- [ ] Solution is a single method or minimal set of files (not multi-layer)
- [ ] Works dynamically with M3 flexible-schema profiles (no hardcoded field names)
- [ ] No regressions on existing functionality (Collections button, type facet hiding, etc.)
- [ ] Documented as potential upstream contribution pattern in code comments

## Stop Conditions — escalate to user immediately if:
- Investigation reveals the 3 failing facets have a different root cause than expected
- The simplified approach cannot be made compatible with M3 late registration
- A database migration or Hyrax core change is required

---

## Commit Instructions
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack
git checkout -b fix/simplify-facet-limiter [or use existing branch]
# Edit files, test on hykudev
git add [specific files only]
git commit -m "refactor: simplify facet limiter to clean upstream-compatible solution"
git push
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update task file with findings — what was broken and why
- [ ] Document simplified pattern as potential upstream contribution note in task summary

---
