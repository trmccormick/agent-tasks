---
status: backlog
priority: HIGH
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

**STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

```
You are **Implementation Agent**.

Project: wvu-moonshot
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/tasks/backlog/[SUBFOLDER]/2026-09-11-HIGH-FEATURE-CSV-EXPORT.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvu-moonshot/tasks/backlog/[SUBFOLDER]/2026-09-11-HIGH-FEATURE-CSV-EXPORT.md \
         projects/wvu-moonshot/tasks/active/2026-09-11-HIGH-FEATURE-CSV-EXPORT.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvu-moonshot/tasks -name "2026-09-11-HIGH-FEATURE-CSV-EXPORT.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: CSV Export — Include Manual Entries for Foundation Reconciliation
**Status**: BACKLOG | ACTIVE | BLOCKED | COMPLETED
**Priority**: HIGH
**Type**: feature
**Created**: 2026-09-11
**Last Updated**: 2026-09-11

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: PASS | FAIL — [note missing sections]
- **Docker Wrapper Check**: PASS | FAIL | N/A
- **MVP Alignment**: VALID | STALE | OBSOLETE
- **MVP Impact Note**: Foundation reconciliation requires export of all records (csv + manual) with entry_source tracking
- **Action Line**: READY FOR LOCAL DISPATCH | NEEDS MANUAL REVIEW | OBSOLETE — ARCHIVE

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary) | Cloud fallback agent
**Why This Agent**: [one line — if cloud agent, state why local failed]
**Local attempts before cloud**: [N/A | 1 | 2 — cloud only dispatched after 2 local failures]
**Supervision Level**: [watched carefully | standard | autonomous OK]

**Supervision Legend**:
- Watched carefully = all agents on first dispatch of a task
- Standard = local Qwen (Copilot) on well-specified repeat task types
- Autonomous OK = not currently used — all tasks require human approval before commit

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.
> If assigning to cloud, document which local attempts failed and why.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/README.md`
3. **This Task File**: Everything below

> Agent MUST read in this order. Do not skip. Synthesis report goes in chat BEFORE starting work.

---

## Context

After the October event, WVU Libraries needs to export all attendee/check-in data back to Foundation for reconciliation. The export must include:
1. **CSV-imported records** (from Foundation data)
2. **Manually-entered records** (deans, staff, "plus ones")

The export distinguishes which records came from CSV vs. manual entry so Foundation can reconcile accordingly. This requires a new service (`GraduateRecordCsvExporter`), an export controller action, and an index button.

**Outstanding prerequisites**: `entry_source` column must exist (from task `2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md`) and `education_history` method must exist (from CSV Loader task).

---

## Critical Information for This Task

### Credentials (if needed)
No credentials required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: CSV header must match Foundation expectations exactly — column names matter for reconciliation
- ❌ Wrong: Create custom headers that don't match Foundation's format expectations
- ✅ Right: Use the exact column names listed in Acceptance Criteria and export code block below
- Why: Foundation uses this data for donor tracking; inconsistent headers break their import pipelines

⚠️ **GOTCHA 2**: `education_history` must handle nil/blank gracefully — manual entries have no education data
- ❌ Wrong: Call `record.education_history.join(", ")` without checking if method or result is nil
- ✅ Right: Use safe navigation: `(record.education_history || []).join(", ")` 
- Why: Manual entries may not call the `education_history` method, and even CSV records with no degrees return an empty array

⚠️ **GOTCHA 3**: CRM ID is nil for manual entries — CSV export must handle nil values correctly (empty string in CSV, NOT "null")
- ❌ Wrong: Let Ruby serialize nil as "null" in CSV output
- ✅ Right: Explicitly convert nil to empty string: `(record.crm_id || "")`
- Why: Foundation expects blank cell for records without CRM ID

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — single-domain Rails app.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: CSV Export — Include Manual Entries for Foundation Reconciliation
**Status**: backlog → active → completed
**Date**: 2026-09-11

### What I'm About to Do
Create a new service `GraduateRecordCsvExporter` that generates a downloadable CSV file of all GraduateRecords (both csv-imported and manually-entered). Add an export action to the controller, configure routes, and add an "Export Data" button to the index page. The CSV includes entry_source column so Foundation can distinguish record origins.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `app/services/graduate_record_csv_exporter.rb` | New: CSV generation service | not started |
| `app/controllers/graduate_records_controller.rb` | Add export action | not started |
| `config/routes.rb` | Add export route | not started |
| `app/views/graduate_records/index.html.erb` | Add "Export Data" button | not started |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
1. `GraduateRecordCsvExporter.export` generates CSV with all records
2. `/graduate_records/export` action sends downloadable CSV file
3. CSV includes: ID, FIRST NAME, LAST NAME, IS_ALUMNI, WAIVER_COMPLETED, EDUCATION_HISTORY, ENTRY_SOURCE
4. Index page shows "Export Data" button that triggers download

### Critical Gotchas I Will Avoid
- ❌ Serializing nil CRM IDs as "null" in CSV — instead ✅ Convert to empty string with `(record.crm_id || "")`
- ❌ Calling education_history without safe navigation for manual entries — instead ✅ Use `(record.education_history || []).join(", ")`
- ❌ Using wrong column headers that don't match Foundation expectations — instead ✅ Follow exact header spec in Acceptance Criteria

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

WVU Libraries needs to export all attendee/check-in data back to Foundation for post-event reconciliation. Currently, no export functionality exists — only CSV import via DonorCsvLoader. The export must include both CSV-imported and manually-entered records with an `entry_source` column to distinguish origins.

**Current behavior**: No export button; no export controller action; no export service; no way to send data back to Foundation.
**Expected behavior**: An "Export Data" button on the index page that triggers a CSV download containing all GraduateRecords with proper headers including entry_source for reconciliation.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `app/services/graduate_record_csv_exporter.rb` | Create new: CSV generation service | `.export`, `.headers` (new file) |
| `app/controllers/graduate_records_controller.rb` | Add export action | `def export` |
| `config/routes.rb` | Add collection route | `collection { get :export }` |
| `app/views/graduate_records/index.html.erb` | Add "Export Data" button | header-actions div |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `app/services/donor_csv_loader.rb` | Reference for CSV generation patterns (headers, escaping) |
| `wvu-moonshot/app/models/graduate_record.rb` | Understand education_history method and data structure |

### Migration (if needed)
- [ ] No migration needed

**If migration needed: follow GUARDRAILS Rule 2 before proceeding.**

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.
- Debug prints OK for complex callbacks — add temporary `puts` statements, remove after verification.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
# From inside agent-tasks repo root:
git mv projects/wvu-moonshot/tasks/backlog/[SUBFOLDER]/2026-09-11-HIGH-FEATURE-CSV-EXPORT.md \
       projects/wvu-moonshot/tasks/active/2026-09-11-HIGH-FEATURE-CSV-EXPORT.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/tasks \
     -name "2026-09-11-HIGH-FEATURE-CSV-EXPORT.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Create GraduateRecordCsvExporter service (new file)

```ruby
# app/services/graduate_record_csv_exporter.rb
require 'csv'

class GraduateRecordCsvExporter
  def self.export(records = nil)
    records ||= GraduateRecord.all
    
    CSV.generate(headers: true, encoding: CSV::Encoding::UTF_8) do |csv|
      csv << headers
      
      records.each do |record|
        csv << build_row(record)
      end
    end
  end
  
  def self.headers
    [
      'ID',
      'FIRST NAME',
      'LAST NAME',
      'IS_ALUMNI',
      'WAIVER_COMPLETED',
      'EDUCATION_HISTORY',
      'ENTRY_SOURCE'
    ]
  end
  
  def self.build_row(record)
    [
      record.crm_id || '',                        # Empty string for nil (manual entries)
      record.first_name || '',
      record.surname || '',
      record.is_alumni ? 'true' : 'false',
      record.waiver_completed ? 'true' : 'false',
      (record.education_history || []).join(', '), # Safe navigation for manual entries
      record.entry_source || 'csv'                 # Fallback default
    ]
  end
end
```

### Step 2 — Add export action to controller

```ruby
# app/controllers/graduate_records_controller.rb

def export
  records = GraduateRecord.all
  csv_data = GraduateRecordCsvExporter.export(records)
  
  send_data csv_data, 
    filename: "moonshot_records_#{Date.today}.csv",
    type: 'text/csv; charset=utf-8'
end
```

### Step 3 — Add export route

```ruby
# config/routes.rb — INSIDE resources :graduate_records block

resources :graduate_records do
  collection do
    get :export
  end
end
```

### Step 4 — Add "Export Data" button to index view

```erb
<!-- app/views/graduate_records/index.html.erb — ADD inside .header-actions div -->
<%= link_to "Export Data", export_graduate_records_path, 
    class: "btn btn-secondary", 
    data: { turbo_frame: "_top" } %>
```

> **Note**: `turbo_frame: "_top"` ensures download works through Turbo/Hotwire (prevents Turbo from intercepting the response).

### Step 5 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/moonshot — do NOT add cd /home/moonshot.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec moonshot bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec 2>&1 | tail -20'
```

Expected result: all existing tests passing.

### Step 6 — Manual verification

```bash
# Start app and navigate to index
docker exec moonshot bundle exec rails s -b 0.0.0.0
```

Then visit `http://localhost:3000/graduate_records` in your browser:
1. Verify "Export Data" button appears next to "Add Person" button
2. Click "Export Data" — verify CSV file downloads with filename `moonshot_records_YYYY-MM-DD.csv`
3. Open CSV in spreadsheet app and verify:
   - Headers present: ID, FIRST NAME, LAST NAME, IS_ALUMNI, WAIVER_COMPLETED, EDUCATION_HISTORY, ENTRY_SOURCE
   - CSV records show CRM IDs for imported records, blank cells for manual entries
   - ENTRY_SOURCE shows "csv" vs "manual" correctly
   - Education history appears as "College Name (Year)" or is blank

### Step 7 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Task: CSV Export Implementation

FILES CHANGED:
- app/services/graduate_record_csv_exporter.rb — created (CSV generation service)
- app/controllers/graduate_records_controller.rb — modified (export action added)
- config/routes.rb — modified (export route added)
- app/views/graduate_records/index.html.erb — modified (Export Data button added)

ROOT CAUSE: No export functionality existed; Foundation required post-event data reconciliation.

PROPOSED FIX: Service-based CSV exporter with controller action and index button for easy download.

RISK:
- Export service must handle nil values gracefully (crm_id, education_history)
- Turbo/Hotwire requires turbo_frame: "_top" for file downloads to work correctly
- Large datasets (500+ records) should still generate in <1 second (Ruby CSV is fast)

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] GraduateRecordCsvExporter service created at `app/services/graduate_record_csv_exporter.rb`
- [ ] Export action added to GraduateRecordsController
- [ ] Route `/graduate_records/export` works (accessible via GET)
- [ ] CSV download triggered with correct filename (`moonshot_records_YYYY-MM-DD.csv`)
- [ ] CSV includes all records (both csv-imported and manually-entered)
- [ ] CSV header row includes: ID, FIRST NAME, LAST NAME, IS_ALUMNI, WAIVER_COMPLETED, EDUCATION_HISTORY, ENTRY_SOURCE
- [ ] CSV correctly shows CRM ID for csv-imported records, blank for manual entries
- [ ] `entry_source` column correctly identifies "csv" vs "manual"
- [ ] `education_history` exported as condensed format ("College Name (Year)") or blank
- [ ] CSV is properly formatted (UTF-8 encoding, correct escaping for special characters)
- [ ] Export button visible on index page next to "Add Person"
- [ ] Clicking export downloads file immediately (not Turbo-intercepted)
- [ ] All existing RSpec tests still pass

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Same failure persists after two attempts
- Root cause is in a shared concern, base class, or factory used across many specs
- A database migration is needed that wasn't anticipated
- Any architectural decision is required
- Fix requires changing more files than the task specifies

---

## Commit Instructions
Run git commands on **host only** — never inside the Docker container:
```bash
git add app/services/graduate_record_csv_exporter.rb app/controllers/graduate_records_controller.rb config/routes.rb app/views/graduate_records/index.html.erb
git commit -m "feature: add CSV export service with entry_source tracking for Foundation reconciliation"
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/wvu-moonshot/tasks/active/2026-09-11-HIGH-FEATURE-CSV-EXPORT.md projects/wvu-moonshot/tasks/completed/$(date +%Y-%m)/2026-09-11-HIGH-FEATURE-CSV-EXPORT.md

git commit -m "chore: move 2026-09-11-HIGH-FEATURE-CSV-EXPORT.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update `docs/[path]/[file].md` — [what to update]
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: `2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md` (entry_source column must exist), CSV Loader task (education_history method)
**Blocks**: Post-event data reconciliation with Foundation
**Related tasks**: Manual Entry Form, all three major features

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: GraduateRecordCsvExporter service + export controller action + routes + index button implemented | CSV includes entry_source column for Foundation reconciliation | CSV Export task complete, Test Coverage ready for dispatch