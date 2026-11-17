---
status: draft
resume: false
priority: MEDIUM
type: data
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.**

**Current State**: In `tasks/drafts/` committed to git  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## 🟡 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

After review agent has returned assessment:

- [ ] Acceptance Criteria are clear and measurable (no ambiguity)
- [ ] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [ ] Dependencies listed and verified against status.md (all prerequisites exist)
- [ ] No contradictions in the task specification
- [ ] Architecture aligns with current codebase patterns (review agent verified)
- [ ] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/[SUBFOLDER]/` and changing `status: draft → status: backlog`.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

- [ ] Agent Dispatch Interface section above is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

# TASK: Fix Bulkrax metadata separator — restrict to semicolons only
**Status**: DRAFT
**Priority**: MEDIUM
**Type**: data
**Created**: 2026-11-17
**Last Updated**: 2026-11-17

---

## Problem

CSV imports through Bulkrax split metadata fields (subject, keyword, people_represented, etc.) on **colons AND semicolons**, when they should only split on **semicolons**. Values containing colons within data (e.g., `"Democratic National Convention  (1936 : Philadelphia, Pa.)"`) get incorrectly broken into separate entries.

## Root Cause

Bulkrax gem default: `DEFAULT_MULTI_VALUE_ELEMENT_SPLIT_ON = /\s*[:;|]\s*/.freeze`

Fields with `"split": true` in Bulkrax field mappings fall back to this regex, treating colons as separators. No local override exists in the WVU Knapsack initializer (`hyrax-webapp/config/initializers/bulkrax.rb`).

## Affected Fields (16 total)

All currently set to `"split": true` — need to change to `"split": ";"`:

| Field | Field | Field | Field |
|-------|-------|-------|-------|
| `subject` | `keyword` | `people_represented` | `creator` |
| `contributor` | `description` | `abstract` | `date_created` |
| `identifier` | `language` | `publisher` | `resource_type` |
| `rights_notes` | `source` | `extent` | `policy_area` |

## Fields Already Correct (no change needed)

These already override with pipe-only: `accessibility_feature`, `degree_discipline`, `degree_grantor`, `degree_level`, `degree_name`, `rights_holder`, `table_of_contents`, `newer_version`, `previous_version`, `alternate_version`, `related_item`, `committee_member`, `education_level`, `library_catalog_identifier`, `oer_size`, `audience`.

## Acceptance Criteria

1. All 16 affected fields in Bulkrax field mappings changed from `"split": true` to `"split": ";"`
2. Re-import a test CSV with colons in values (e.g., subject field with date ranges like `"1936 : Philadelphia, Pa."`) — verify colons are preserved within entries
3. Verify existing semicolon-separated values still split correctly
4. No fields that should use pipe-only splitting were accidentally changed

## Implementation Steps

### Step 1: Identify which tenant(s) need the fix
- Check demo-wvu-knapsack (current local setup)
- Check production Hyku instance
- Determine if both need the same fix

### Step 2: Update Bulkrax field mappings per tenant
For each affected field, change in the Bulkrax Field Mappings UI (Settings → Bulkrax Field Mappings):
```json
// Before:
"subject": { "from": ["subject"], "split": true }

// After:
"subject": { "from": ["subject"], "split": ";" }
```

### Step 3: Verify the fix
- Re-import a test CSV with known colon-containing values
- Confirm values are NOT split on colons
- Confirm semicolons still work as separators

## Architecture Gotchas

1. **Per-tenant configuration**: Bulkrax field mappings are stored per tenant in the database, not in code. Each tenant needs its own update.
2. **No code changes needed**: This is purely a configuration change in the admin UI or via tenant settings API.
3. **Existing imports unaffected**: Only new imports after the fix will use the new separator behavior. Previously imported data with incorrect splits would need re-import.
4. **Congress archives project**: Same issue likely present in `hydra_acdap_portal_public` — check and fix there too.

## Related Documentation

- Wiki entry: `/wiki/BULKRAX_SEPARATOR_ISSUE.md` (in wvu_knapsack repo)
- ACDAP: Previously encountered and fixed in `hydra_acdap_portal_public`
- Congress Archives: Same pattern likely present — check field mappings there too

## Suggested Review Agent

**Claude** — can read GitHub repos to verify codebase patterns; this task is config-only but benefits from understanding the Bulkrax gem structure.
