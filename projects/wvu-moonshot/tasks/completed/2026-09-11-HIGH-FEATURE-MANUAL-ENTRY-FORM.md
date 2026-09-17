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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/tasks/backlog/[SUBFOLDER]/2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvu-moonshot/tasks/backlog/[SUBFOLDER]/2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md \
         projects/wvu-moonshot/tasks/active/2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvu-moonshot/tasks -name "2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md"
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

# TASK: Manual Entry Form — Add People Not on Foundation CSV
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
- **MVP Impact Note**: One-off event intake for deans, staff, "plus ones" not on Foundation CSV
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

The WVU Moonshot October 2026 event will have attendees not listed on the Foundation CSV — deans, staff members, and "plus ones." These people need to be added to the check-in system quickly during the event. Currently, only CSV import is supported; no manual entry exists.

This task creates a minimal quick-entry form for deans/staff/invitees ("plus ones") that captures only First Name, Last Name, Alumni checkbox, and Waiver checkbox — nothing more. Manual entries appear in search and check-in workflow alongside CSV records with `entry_source = "manual"` and blank `crm_id`.

**Outstanding prerequisite**: `is_alumni` column must exist on `graduate_records` table (from task `2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md`).

---

## Critical Information for This Task

### Credentials (if needed)
No credentials required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: `entry_source` must be a string column with default `'csv'` — NOT handled by migration default alone
- ❌ Wrong: Set migration default to `'csv'` and rely on it for manual entries
- ✅ Right: Migration defaults to `'csv'`; controller explicitly sets `@record.entry_source = 'manual'` per-record
- Why: The CSV import path creates records with `entry_source = 'csv'` via the loader; the form must explicitly override this

⚠️ **GOTCHA 2**: CRM ID is NULL for manual entries, NOT an empty string — Rails validation must distinguish `'csv'` vs `'manual'`
- ❌ Wrong: `validates :crm_id, presence: true` on all records
- ✅ Right: `validates :crm_id, presence: true, if: -> { entry_source == 'csv' }` (conditional validation)
- Why: Manual entries have no CRM ID; they are not in the Foundation database

⚠️ **GOTCHA 3**: Migration adds TWO columns in ONE file — `entry_source` AND `linked_record_id` — must be atomic
- ❌ Wrong: Create two separate migrations (risk of partial failure)
- ✅ Right: Single migration adds both columns and foreign key together
- Why: Both columns are needed for the manual entry feature; partial migration breaks functionality

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — single-domain Rails app.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Manual Entry Form — Add People Not on Foundation CSV
**Status**: backlog → active → completed
**Date**: 2026-09-11

### What I'm About to Do
Create a minimal manual entry form for the WVU Moonshot check-in system. This adds a new database migration (entry_source + linked_record_id columns), updates the GraduateRecord model validation, implements new/create controller actions, creates the form view, and adds an "Add Person" button to the index page.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `db/migrate/*_add_entry_source_to_graduate_records.rb` | Create migration for 2 new columns | not started |
| `app/models/graduate_record.rb` | Update validations, add scopes | not started |
| `app/controllers/graduate_records_controller.rb` | Add new/create actions | not started |
| `app/views/graduate_records/new.html.erb` | Create form view | not started |
| `app/views/graduate_records/index.html.erb` | Add "Add Person" button | not started |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
1. Migration adds `entry_source` (string, default 'csv') and `linked_record_id` (bigint, optional FK) columns
2. Form renders at `/graduate_records/new` with only: First Name, Last Name, Alumni checkbox, Waiver checkbox, Plus-One dropdown
3. POST to create saves record with `entry_source = "manual"`, `crm_id = nil`
4. Index page shows "Add Person" button

### Critical Gotchas I Will Avoid
- ❌ Setting crm_id presence validation unconditionally — instead ✅ Use conditional: `if: -> { entry_source == 'csv' }`
- ❌ Relying on migration default for entry_source manual entries — instead ✅ Controller explicitly sets it per-record
- ❌ Creating two separate migrations — instead ✅ Single migration adds both columns atomically

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

The WVU Moonshot event needs a way to add people who are NOT on the Foundation CSV during check-in. Currently, only CSV import exists — there is no manual entry form or database support for it.

**Current behavior**: No `entry_source` column; no `linked_record_id` column; no manual entry form; GraduateRecord requires CRM ID for all records.
**Expected behavior**: A simple form at `/graduate_records/new` that accepts First Name, Last Name, Alumni checkbox, Waiver checkbox (optional Plus-One link). Records are saved with `entry_source = "manual"` and `crm_id = NULL`. Manual entries appear in search results alongside CSV records.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `db/migrate/*_add_entry_source_to_graduate_records.rb` | Create migration for entry_source + linked_record_id | — (new file) |
| `app/models/graduate_record.rb` | Update validations, add scopes | `validates :crm_id`, new scopes |
| `app/controllers/graduate_records_controller.rb` | Add new/create actions | `def new`, `def create`, `record_params` |
| `app/views/graduate_records/new.html.erb` | Create form view | `form_with` for GraduateRecord (new file) |
| `app/views/graduate_records/index.html.erb` | Add "Add Person" button | header-actions div |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `wvu-moonshot/app/models/graduate_record.rb` | Understand current structure and existing validations |
| `app/views/graduate_records/show.html.erb` | Understand what fields the show view expects |
| `config/routes.rb` | Verify routes already support new/create (they should) |

### Migration (if needed)
- [x] Migration needed: Add `entry_source` (string, default 'csv') and `linked_record_id` (bigint, optional foreign key to graduate_records)

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
git mv projects/wvu-moonshot/tasks/backlog/[SUBFOLDER]/2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md \
       projects/wvu-moonshot/tasks/active/2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/tasks \
     -name "2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Create migration for entry_source + linked_record_id columns

```ruby
# db/migrate/[timestamp]_add_entry_source_and_linked_record_to_graduate_records.rb
class AddEntrySourceAndLinkedRecordToGraduateRecords < ActiveRecord::Migration[8.1]
  def change
    add_column :graduate_records, :entry_source, :string, default: 'csv'
    add_column :graduate_records, :linked_record_id, :bigint, null: true
    add_foreign_key :graduate_records, :graduate_records, column: :linked_record_id, on_delete: :nullify
  end
end
```

Run migration:
```bash
docker exec moonshot bundle exec rake db:migrate 2>&1 | tail -10
```

### Step 2 — Update GraduateRecord model (validations + scopes)

```ruby
# app/models/graduate_record.rb

# ADD after existing validates line:
validates :crm_id, presence: true, if: -> { entry_source == 'csv' }

# ADD belongs_to for plus-one linking:
belongs_to :linked_record, class_name: 'GraduateRecord', optional: true

# ADD scopes at end of model:
scope :csv_imported, -> { where(entry_source: 'csv') }
scope :manually_entered, -> { where(entry_source: 'manual') }
```

### Step 3 — Add new/create controller actions

```ruby
# app/controllers/graduate_records_controller.rb

def new
  @record = GraduateRecord.new
end

def create
  @record = GraduateRecord.new(record_params)
  @record.entry_source = 'manual'
  @record.crm_id = nil

  if @record.linked_record_id.present?
    linked = GraduateRecord.find(@record.linked_record_id)
    @record.data ||= {}
    @record.data['PRIMARY_ADDRESSEE'] = linked.data&.dig('PRIMARY_ADDRESSEE')
  end

  if @record.save
    redirect_to @record, notice: 'Person added successfully'
  else
    render :new, status: :unprocessable_entity
  end
end

private

def record_params
  params.require(:graduate_record).permit(:first_name, :surname, :is_alumni, :waiver_completed, :linked_record_id)
end
```

### Step 4 — Create form view (app/views/graduate_records/new.html.erb)

```erb
<div class="form-container">
  <h1>Add Person (Manual Entry)</h1>

  <%= form_with(model: @record, local: true) do |form| %>
    <% if @record.errors.any? %>
      <div id="error_explanation">
        <h2><%= pluralize(@record.errors.count, "error") %> prohibited this record:</h2>
        <ul>
          <% @record.errors.full_messages.each do |message| %>
            <li><%= message %></li>
          <% end %>
        </ul>
      </div>
    <% end %>

    <div class="field">
      <%= form.label :first_name %>
      <%= form.text_field :first_name, placeholder: "First Name", required: true %>
    </div>

    <div class="field">
      <%= form.label :surname %>
      <%= form.text_field :surname, placeholder: "Last Name", required: true %>
    </div>

    <div class="field">
      <%= form.label :is_alumni, "Alumni?" %>
      <%= form.check_box :is_alumni %>
    </div>

    <div class="field">
      <%= form.label :waiver_completed, "Waiver Signed?" %>
      <%= form.check_box :waiver_completed %>
    </div>

    <div class="field">
      <%= form.label :linked_record_id, "Plus-One For (Optional)" %>
      <%= form.collection_select :linked_record_id,
          GraduateRecord.all,
          :id,
          ->(record) { "#{record.first_name} #{record.surname} (#{record.crm_id || 'Manual'})" },
          { include_blank: "No link" } %>
    </div>

    <div class="actions">
      <%= form.submit "Add Person", class: "btn btn-primary" %>
      <%= link_to "Cancel", graduate_records_path, class: "btn btn-secondary" %>
    </div>
  <% end %>
</div>
```

### Step 5 — Add "Add Person" button to index view

```erb
<!-- app/views/graduate_records/index.html.erb — ADD inside .header-actions div -->
<%= link_to "Add Person", new_graduate_record_path, class: "btn btn-primary" %>
```

### Step 6 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/moonshot — do NOT add cd /home/moonshot.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
docker exec moonshot bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec 2>&1 | tail -20'
```

Expected result: all existing tests passing, plus any new tests added.

### Step 7 — Manual verification

```bash
# Start app and navigate to form
docker exec moonshot bundle exec rails s -b 0.0.0.0
```

Then visit `http://localhost:3000/graduate_records/new` in your browser:
1. Fill in First Name: "Test", Last Name: "User", Alumni: checked, Waiver: unchecked
2. Click "Add Person"
3. Verify redirect to show page with record details
4. Verify `entry_source = "manual"` and `crm_id = NULL` in database

```bash
# Verify in Rails console
docker exec moonshot bundle exec rails runner 'puts GraduateRecord.last.inspect'
```

### Step 8 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Task: Manual Entry Form Implementation

FILES CHANGED:
- db/migrate/*_add_entry_source_to_graduate_records.rb — created
- app/models/graduate_record.rb — modified (validations + scopes)
- app/controllers/graduate_records_controller.rb — modified (new/create + record_params)
- app/views/graduate_records/new.html.erb — created
- app/views/graduate_records/index.html.erb — modified (button added)

ROOT CAUSE: No manual entry support existed; Foundation CSV only path blocked event check-in for non-listed guests.

PROPOSED FIX: Minimal form + migration + controller actions that save records with entry_source="manual" and crm_id=NULL.

RISK:
- Existing CSV import unaffected (uses DonorCsvLoader, not this form)
- linked_record_id FK on_delete: nullify prevents orphan records
- Conditional crm_id validation only triggers for csv entries

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
- [ ] Migration creates `entry_source` (string, default 'csv') and `linked_record_id` (bigint FK) columns without errors
- [ ] GraduateRecord validates CRM ID only when `entry_source == 'csv'`
- [ ] GET `/graduate_records/new` renders form correctly with First Name (required), Last Name (required), Alumni checkbox, Waiver checkbox, Plus-One dropdown
- [ ] POST to create saves record with `entry_source = "manual"` and `crm_id = NULL`
- [ ] When linked_record_id provided, `PRIMARY_ADDRESSEE` is copied from linked record's jsonb data
- [ ] Index page shows "Add Person" button that links to form
- [ ] Created record appears in index search results
- [ ] Manually added records searchable by name alongside CSV records
- [ ] All existing RSpec tests still pass
- [ ] Manual verification: form submits, creates record, redirects, shows correct data

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
git add db/migrate/*_add_entry_source_to_graduate_records.rb app/models/graduate_record.rb app/controllers/graduate_records_controller.rb app/views/graduate_records/new.html.erb app/views/graduate_records/index.html.erb
git commit -m "feature: add manual entry form with entry_source column and linked records"
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/wvu-moonshot/tasks/active/2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md projects/wvu-moonshot/tasks/completed/$(date +%Y-%m)/2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md

git commit -m "chore: move 2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update `docs/[path]/[file].md` — [what to update]
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: `2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md` (is_alumni column must exist)
**Blocks**: CSV export task, test coverage task
**Related tasks**: Manual entry records appear in CSV export

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

HANDOFF SUMMARY: migration + model validations + controller new/create actions + form view + index button implemented | entry_source/linked_record_id columns added to DB | Manual Entry Form task complete, CSV Export ready for dispatch