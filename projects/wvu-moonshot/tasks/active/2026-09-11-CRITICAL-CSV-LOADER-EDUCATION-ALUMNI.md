---
status: active
priority: CRITICAL
type: feature
system_domain: CSV_IMPORT | DATA_TRANSFORMATION
mvp_alignment: OCTOBER_2026_EVENT_READINESS
local_worker_safe: true
---

# ⚠️ CRITICAL: Task Readiness Checklist (Human — before dispatching)

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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/tasks/backlog/2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvu-moonshot/tasks/backlog/2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md \
         projects/wvu-moonshot/tasks/active/2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvu-moonshot/tasks -name "2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0): Task file contains all prerequisites, credentials, gotchas, and verification steps.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/summaries/
  Filename pattern: YYYY-MM-DD-CRITICAL-CSV-LOADER-SYNTHESIS.md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

## Local Worker Triage Report (Optional — for backlog review only)

*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: ✅ PASS
- **Docker Wrapper Check**: ✅ PASS — RSpec commands use correct `docker exec moonshot bundle exec rspec` format
- **MVP Alignment**: ✅ VALID — CSV import is critical path for October 2026 event
- **MVP Impact Note**: Event depends on accurate alumni tracking and education history display for check-in workflow
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: CSV loader implementation is straightforward data transformation with existing test suite; local Qwen optimal for this scope
**Local attempts before cloud**: N/A (first dispatch)
**Supervision Level**: Standard (well-specified repeat task type for this codebase)

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

This task implements two critical CSV import features needed for the October 2026 donor event:

1. **Alumni Status Tracking** (`is_alumni` boolean column) — parsed from Foundation CSV's PRIMARY CONSTITUENCY field
2. **Education History** (condensed display format) — extracted from repeating education columns in CSV

The Foundation CSV contains donor data with household relationships and education history. The system must extract these into queryable database columns while preserving raw data in jsonb for future reference.

**Why this matters**: Event staff need to segment check-ins by alumni status and display education without overwhelming the UI. This is the critical blocker for manual entry and CSV export features.

**Relevant Architecture Docs** — read before starting:
- `docs/SCHEMA_DESIGN.md` — explains flexible jsonb-first schema (lines 40-120)
- `docs/ARCHITECTURE.md` — CSV import workflow and manual entry patterns (lines 80-150)
- `docs/MEETING_NOTES_2026-09-11.md` — full context from WVU Libraries meeting

---

## Critical Information for This Task

### Credentials
| Field | Value | Notes |
|-------|-------|-------|
| Docker container | `moonshot` | Development environment |
| App path | `/home/moonshot` | Inside container |
| Test database | Rails test (auto-managed) | Run with `docker exec moonshot` |

> No external credentials needed — local Docker development only.

---

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: is_alumni must be a database COLUMN, not just jsonb data**
- ❌ Wrong: Store `is_alumni` in jsonb only (`data['is_alumni']`)
- ✅ Right: Create migration adding `is_alumni :boolean` column to `graduate_records` table
- Why: Event staff need fast queries like `GraduateRecord.where(is_alumni: true).count` without scanning jsonb. SQL boolean columns are indexed.

⚠️ **GOTCHA 2: PRIMARY CONSTITUENCY field name varies in CSV**
- ❌ Wrong: Hard-code column name "PRIMARY CONSTITUENCY" without checking for variations
- ✅ Right: Use the existing COLUMN_MAP flexibility in DonorCsvLoader (already handles "crm id" vs "CRM ID" vs "id")
- Why: Foundation may send the field as "Primary Constituency", "PRIMARY_CONSTITUENCY", etc. Use case-insensitive lookup.

⚠️ **GOTCHA 3: Education repeating columns have NO guarantees of order or count**
- ❌ Wrong: Assume exactly 3 education blocks in specific column positions
- ✅ Right: Loop through `education_1`, `education_2`, `education_3` keys in jsonb; skip if blank; stop at first blank (some people have 1 degree, some 3)
- Why: Sample data shows examples from 0-3 degrees per person. Code must handle all gracefully.

⚠️ **GOTCHA 4: Raw education data must be preserved in jsonb for audit trail**
- ❌ Wrong: Parse only the condensed format, discard raw columns
- ✅ Right: Store INSTITUTIONNAME, CLASSOF, EDUCATIONALCOLLEGECODE raw in jsonb; also make model method return condensed display format
- Why: Future billing/reconciliation may need raw data. Flexible schema advantage.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any files, running any commands, or modifying code, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: CSV Loader: Parse is_alumni and Education History
**Status**: backlog → active
**Date**: YYYY-MM-DD

### What I'm About to Do
Add `is_alumni` boolean column to graduate_records table and parse it from Foundation CSV's PRIMARY CONSTITUENCY field. Add `education_history` method to GraduateRecord model that returns condensed format (e.g., ["Business & Economics (2010)", "Engineering (2004)"]) from repeating education columns. Update DonorCsvLoader to handle conversion during import. Update tests to verify all three features work with real Foundation sample data.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `wvu-moonshot/db/migrate/[timestamp]_add_is_alumni_to_graduate_records.rb` | Create migration | new file |
| `wvu-moonshot/app/services/donor_csv_loader.rb` | Add is_alumni conversion, education parsing | modify |
| `wvu-moonshot/app/models/graduate_record.rb` | Add education_history method | modify |
| `wvu-moonshot/spec/services/donor_csv_loader_spec.rb` | Add tests for is_alumni + education | modify |
| `wvu-moonshot/data/imports/donors.csv` | Real Foundation format sample data | reference |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand all 4 architecture gotchas above
- ✅ Located migration, loader service, model, and test files
- ✅ Reviewed sample data structure in data/imports/donors.csv

### Expected Outcomes
Migration runs cleanly. is_alumni column added to database. DonorCsvLoader extracts PRIMARY CONSTITUENCY and converts to boolean during import. GraduateRecord#education_history returns array of strings in format "College Name (Year)". All 41 existing RSpec tests still pass. New test added verifying is_alumni conversion and education_history parsing with real Foundation data.

### Critical Gotchas I Will Avoid
- ❌ Storing is_alumni only in jsonb — instead ✅ Create migration for database column
- ❌ Hard-coding "PRIMARY CONSTITUENCY" column name — instead ✅ Use COLUMN_MAP flexibility
- ❌ Assuming exactly 3 education blocks in specific positions — instead ✅ Loop through education_1/2/3 keys and skip blanks
- ❌ Discarding raw education data — instead ✅ Preserve in jsonb while providing condensed accessor

---

**SYNTHESIS COMPLETE.** Ready to proceed with CRITICAL path CSV Loader implementation.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.


---

## Problem Statement

**Current behavior**: 
- DonorCsvLoader imports Foundation CSV into graduate_records table
- PRIMARY CONSTITUENCY field stored in jsonb `data` object only
- No `is_alumni` column for fast queries or event segmentation
- Education data (repeating columns) stored raw in jsonb
- No condensed display format for education history in views
- Tests don't cover Foundation CSV structure with multiple education blocks

**Expected behavior**:
- DonorCsvLoader extracts PRIMARY CONSTITUENCY → converts to `is_alumni` boolean column
- "Alumni" → true, all others → false
- New `is_alumni` column queryable and indexed
- GraduateRecord model provides `education_history` method returning ["College Name (Year)", ...] array
- Raw education data preserved in jsonb for audit trail
- Tests verify is_alumni conversion and education_history parsing with real Foundation format
- All 41 existing tests continue passing

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `wvu-moonshot/db/migrate/[timestamp]_add_is_alumni_to_graduate_records.rb` | Add is_alumni column to schema | full file (new) |
| `wvu-moonshot/app/services/donor_csv_loader.rb` | Extract PRIMARY CONSTITUENCY and convert to is_alumni boolean | `import_rows` method, `COLUMN_MAP` constant |
| `wvu-moonshot/app/models/graduate_record.rb` | Add education_history accessor method | new `education_history` method |
| `wvu-moonshot/spec/services/donor_csv_loader_spec.rb` | Add tests for is_alumni + education history with Foundation CSV | add test case |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `wvu-moonshot/data/imports/donors.csv` | Real Foundation CSV format with 20 sample records (Alumni/Individual mix, 1-3 education blocks per record) |
| `wvu-moonshot/docs/SCHEMA_DESIGN.md` | Explains flexible jsonb pattern and why raw data is preserved |
| `wvu-moonshot/docs/ARCHITECTURE.md` | System overview and CSV import workflow context |
| `wvu-moonshot/docs/MEETING_NOTES_2026-09-11.md` | Requirements from WVU Libraries meeting, real examples (Kevin & Lisa Adrian, education history patterns) |

### Migration
- [x] Migration needed: Add `is_alumni :boolean, default: false` column to `graduate_records` table

---

## Implementation Steps

> ⚠️ **BEFORE YOU START**: Complete Step 0 first. Then complete and post your STATUS SYNTHESIS REPORT.
> Do not proceed to Step 1 until both are done and approved.

All agents: follow these steps exactly in order.
- Do not skip steps or reorder them.
- Do not proceed to the next step if the current step has not produced a clean result.
- All RSpec commands must use the Docker wrapper format specified in Step 4.

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
# From inside agent-tasks repo root:
cd /Users/tam0013/Documents/git/agent-tasks

git mv projects/wvu-moonshot/tasks/backlog/2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md \
       projects/wvu-moonshot/tasks/active/2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/tasks \
     -name "2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

---

### Step 1 — Create migration to add is_alumni column

Navigate to the wvu-moonshot app directory:

```bash
cd /Users/tam0013/Documents/git/wvu-moonshot/wvu-moonshot
```

Generate a new migration file:

```bash
docker exec moonshot bundle exec rails generate migration AddIsAlumniToGraduateRecords is_alumni:boolean
```

Expected output: Migration file created at `db/migrate/[timestamp]_add_is_alumni_to_graduate_records.rb`

Verify the migration file was created:

```bash
ls -la db/migrate/ | grep is_alumni
```

Open the migration file and verify it has this structure (should auto-generate correctly):

```ruby
# db/migrate/[timestamp]_add_is_alumni_to_graduate_records.rb
class AddIsAlumniToGraduateRecords < ActiveRecord::Migration[8.1]
  def change
    add_column :graduate_records, :is_alumni, :boolean, default: false
  end
end
```

If the migration structure looks correct, proceed to Step 2. If not, edit the file to match the structure above.

---

### Step 2 — Implement is_alumni conversion logic in DonorCsvLoader

**Before: Current DonorCsvLoader#import_rows**
```ruby
# app/services/donor_csv_loader.rb (current state)
COLUMN_MAP = {
  'crm id' => :crm_id,
  'crm_id' => :crm_id,
  'id' => :crm_id,  # Added in Sept 11 update
  'first name' => :first_name,
  'first_name' => :first_name,
  'last name' => :surname,
  'last_name' => :surname,
  'surname' => :surname,
}.freeze

def import_rows
  CSV.foreach(@csv_file, headers: true) do |row|
    record = GraduateRecord.find_or_initialize_by(crm_id: row['ID'])
    # ... stores all data in jsonb, no is_alumni conversion
  end
end
```

**After: Updated DonorCsvLoader#import_rows with is_alumni conversion**

Add this helper method to the DonorCsvLoader class:

```ruby
# app/services/donor_csv_loader.rb (add this new method)
def convert_is_alumni(primary_constituency_value)
  # Convert Foundation CSV PRIMARY CONSTITUENCY field to boolean
  # "Alumni" -> true, anything else -> false
  primary_constituency_value.to_s.strip.downcase == 'alumni'
end
```

Update the `import_rows` method to extract and convert PRIMARY CONSTITUENCY:

```ruby
# app/services/donor_csv_loader.rb (modify import_rows method)
def import_rows
  CSV.foreach(@csv_file, headers: true) do |row|
    record = GraduateRecord.find_or_initialize_by(crm_id: row['ID'])
    
    # Map explicit columns
    record.first_name = row['FIRST NAME']
    record.surname = row['LAST NAME']
    
    # NEW: Convert PRIMARY CONSTITUENCY to is_alumni boolean
    is_alumni = convert_is_alumni(row['PRIMARY CONSTITUENCY'])
    record.is_alumni = is_alumni
    
    # Store all other CSV fields in jsonb data
    record.data = extract_data_fields(row)
    
    record.save!
  end
end
```

**Test data**: Use real Foundation CSV records:
```
ID 9700002 (Abbott, James): PRIMARY CONSTITUENCY = "Alumni" → is_alumni = true
ID 700243830 (Abe, Brian): PRIMARY CONSTITUENCY = "Alumni" → is_alumni = true
ID 700140146 (Aburahma, Ali): PRIMARY CONSTITUENCY = "Individual" → is_alumni = false
```

---

### Step 3 — Add education_history accessor method to GraduateRecord model

**Before: Current GraduateRecord model**
```ruby
# app/models/graduate_record.rb (current state)
class GraduateRecord < ApplicationRecord
  # ... validations, no education accessor
end
```

**After: Add education_history method**

Add this method to the GraduateRecord class:

```ruby
# app/models/graduate_record.rb (add this new method)
def education_history
  educations = []
  
  # Loop through up to 3 education blocks (education_1, education_2, education_3)
  (1..3).each do |i|
    edu_data = data&.dig("education_#{i}")
    
    if edu_data.present?
      college = edu_data['EDUCATIONALCOLLEGECODE']
      year = edu_data['CLASSOF']
      
      # Only add if both college name and year are present
      if college.present? && year.present?
        educations << "#{college} (#{year})"
      end
    end
  end
  
  educations
end
```

**Test data examples**:
```
Abbott, James (ID: 9700002):
  data['education_1'] = { 'INSTITUTIONNAME' => 'West Virginia University', 'CLASSOF' => '1997', 'EDUCATIONALCOLLEGECODE' => 'Arts & Sciences' }
  Expected: education_history returns ["Arts & Sciences (1997)"]

Abe, Brian (ID: 700243830):
  data['education_1'] = { 'INSTITUTIONNAME' => 'West Virginia University', 'CLASSOF' => '2010', 'EDUCATIONALCOLLEGECODE' => 'Business & Economics' }
  data['education_2'] = { 'INSTITUTIONNAME' => 'West Virginia University', 'CLASSOF' => '2004', 'EDUCATIONALCOLLEGECODE' => 'Engineering/Mineral Resources' }
  Expected: education_history returns ["Business & Economics (2010)", "Engineering/Mineral Resources (2004)"]

Aburahma, Ali (ID: 700140146):
  data['education_1'], data['education_2'], data['education_3'] all blank
  Expected: education_history returns []
```

---

### Step 4 — Run migration and verify schema change

Apply the migration to the development database:

```bash
docker exec moonshot bundle exec rake db:migrate
```

Expected output: Shows migration applied successfully
```
== [timestamp] AddIsAlumniToGraduateRecords: migrating =========================
-- add_column(:graduate_records, :is_alumni, :boolean, {:default=>false})
   -> [duration] s
== [timestamp] AddIsAlumniToGraduateRecords: migrated ([duration] s)
```

Verify the column was added:

```bash
docker exec moonshot bundle exec rails dbconsole -p <<EOF
\d graduate_records
EOF
```

Look for this in the output:
```
 is_alumni | boolean | default false
```

---

### Step 5 — Add test case for is_alumni + education_history with Foundation CSV

**Before: Current test structure** (from `spec/services/donor_csv_loader_spec.rb`)
```ruby
# spec/services/donor_csv_loader_spec.rb (current state)
describe DonorCsvLoader do
  # ... existing tests for CSV import
end
```

**After: Add test for Foundation CSV with is_alumni + education_history**

Add this test to the `donor_csv_loader_spec.rb` file:

```ruby
# spec/services/donor_csv_loader_spec.rb (add this new test)
describe 'Foundation CSV import with is_alumni and education_history' do
  let(:csv_file) { 'data/imports/donors.csv' }  # Real Foundation format sample data
  let(:loader) { DonorCsvLoader.new(csv_file) }
  
  it 'converts PRIMARY CONSTITUENCY to is_alumni boolean' do
    loader.import_rows
    
    # Verify Alumni → is_alumni = true
    abbott = GraduateRecord.find_by(crm_id: '9700002')
    expect(abbott.first_name).to eq('James')
    expect(abbott.surname).to eq('Abbott')
    expect(abbott.is_alumni).to be(true)
    
    # Verify Individual → is_alumni = false
    aburahma = GraduateRecord.find_by(crm_id: '700140146')
    expect(aburahma.first_name).to eq('Ali')
    expect(aburahma.surname).to eq('Aburahma')
    expect(aburahma.is_alumni).to be(false)
  end
  
  it 'parses education_history from repeating education columns' do
    loader.import_rows
    
    # Single degree
    abbott = GraduateRecord.find_by(crm_id: '9700002')
    expect(abbott.education_history).to eq(['Arts & Sciences (1997)'])
    
    # Multiple degrees (Brian Abe has 2)
    abe = GraduateRecord.find_by(crm_id: '700243830')
    expect(abe.education_history).to eq(['Business & Economics (2010)', 'Engineering/Mineral Resources (2004)'])
    
    # No education data
    aburahma = GraduateRecord.find_by(crm_id: '700140146')
    expect(aburahma.education_history).to eq([])
  end
  
  it 'preserves raw education data in jsonb for audit trail' do
    loader.import_rows
    
    abbott = GraduateRecord.find_by(crm_id: '9700002')
    expect(abbott.data['education_1']).to include(
      'INSTITUTIONNAME' => 'West Virginia University',
      'CLASSOF' => '1997',
      'EDUCATIONALCOLLEGECODE' => 'Arts & Sciences'
    )
  end
end
```

---

### Step 6 — Run tests to verify all functionality

First, run only the new test you just added:

```bash
docker exec moonshot bundle exec rspec spec/services/donor_csv_loader_spec.rb:12 -v
```

Expected result: Your new test case passes
```
Foundation CSV import with is_alumni and education_history
  converts PRIMARY CONSTITUENCY to is_alumni boolean
  parses education_history from repeating education columns
  preserves raw education data in jsonb for audit trail

3 examples, 0 failures
```

Now run the full CSV Loader test suite to verify no regressions:

```bash
docker exec moonshot bundle exec rspec spec/services/donor_csv_loader_spec.rb -v
```

Expected result: All donor_csv_loader tests pass (including your new ones)

Finally, run the FULL test suite to verify all 41 existing tests still pass:

```bash
docker exec moonshot bundle exec rspec 2>&1 | tail -50
```

Expected result: X examples, 0 failures (X should be ~41 or more)

**Stop Conditions met?** Before proceeding to Step 7:
- ✅ Migration applied cleanly
- ✅ is_alumni column exists in database
- ✅ is_alumni conversion logic works (test passes)
- ✅ education_history method returns correct format (test passes)
- ✅ All 41+ existing tests pass (no regressions)

If any test fails, do NOT proceed. Debug the issue and re-run before moving forward.

---

### Step 7 — Synthesis Report (before committing anything)

Create a new synthesis report documenting the implementation:

```markdown
## IMPLEMENTATION SYNTHESIS REPORT

**Task**: CSV Loader: Parse is_alumni and Education History
**Date**: YYYY-MM-DD
**Agent**: [your name]

### What Was Completed
- Added migration to create is_alumni boolean column (default: false)
- Implemented convert_is_alumni helper method in DonorCsvLoader
- Updated import_rows to extract PRIMARY CONSTITUENCY and convert to boolean
- Added education_history accessor method to GraduateRecord model
- Added comprehensive test case for is_alumni conversion and education_history parsing
- Verified all tests pass (41 existing + 3 new = 44 total)
- Verified raw education data preserved in jsonb

### Files Changed
- `db/migrate/[timestamp]_add_is_alumni_to_graduate_records.rb` (new)
- `app/services/donor_csv_loader.rb` (modified: added convert_is_alumni method, updated import_rows)
- `app/models/graduate_record.rb` (modified: added education_history method)
- `spec/services/donor_csv_loader_spec.rb` (modified: added test case)

### Test Results
```
44 examples, 0 failures
```

All 41 existing tests continue passing. 3 new tests for is_alumni and education_history all pass.

### Issues Discovered
[Any problems found during implementation — or "None" if smooth]

### Follow-up Tasks Needed
[Any new tasks identified — or "None, proceed to next task in sequence"]

### Lessons Learned
[What worked well, what would improve future similar tasks]
```

Save this file to: `/Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/summaries/2026-09-11-CRITICAL-CSV-LOADER-SYNTHESIS.md`

---

## Acceptance Criteria

- [ ] Migration file created: `db/migrate/[timestamp]_add_is_alumni_to_graduate_records.rb`
- [ ] Migration runs without errors
- [ ] is_alumni column added to graduate_records table with default: false
- [ ] DonorCsvLoader extracts PRIMARY CONSTITUENCY field
- [ ] DonorCsvLoader converts to is_alumni boolean (Alumni → true, else → false)
- [ ] GraduateRecord#education_history method implemented
- [ ] education_history returns array of strings: ["College Name (Year)", ...]
- [ ] education_history handles 0, 1, 2, or 3 degrees gracefully
- [ ] Raw education data preserved in jsonb data field
- [ ] Test case added: "imports Foundation CSV with is_alumni and education_history"
- [ ] Test verifies: is_alumni = true for "Alumni" constituency (Abbott, Abe examples)
- [ ] Test verifies: is_alumni = false for non-Alumni (Aburahma example)
- [ ] Test verifies: education_history returns correct condensed format
- [ ] Test verifies: education_history handles multiple degrees (Abe: 2 degrees)
- [ ] Test verifies: education_history handles no education data (Aburahma: empty array)
- [ ] All 41 existing RSpec tests still pass (no regressions)
- [ ] Full test suite runs cleanly: `docker exec moonshot bundle exec rspec 2>&1 | tail -50`

---

## Stop Conditions — escalate to user immediately if:
- Migration fails to apply or schema change is not visible
- is_alumni conversion logic creates new test failures
- Same test failure persists after two attempts at debugging
- Root cause is in a shared concern, base class, or factory
- Any architectural decision differs from gotchas section above
- Need to modify more files than specified in Files Involved section
- Existing tests fail due to unforeseen schema dependency

---

## Commit Instructions

Run git commands on **host only** — never inside the Docker container:

```bash
cd /Users/tam0013/Documents/git/wvu-moonshot/wvu-moonshot

# Add the modified and new files
git add db/migrate/[timestamp]_add_is_alumni_to_graduate_records.rb
git add app/services/donor_csv_loader.rb
git add app/models/graduate_record.rb
git add spec/services/donor_csv_loader_spec.rb

# Commit with clear message
git commit -m "feat: add is_alumni column and education_history parsing to CSV loader

- Add is_alumni boolean column (default: false) to graduate_records table
- Convert PRIMARY CONSTITUENCY to is_alumni during CSV import (Alumni → true)
- Add education_history method to GraduateRecord returning condensed format
- Preserve raw education data in jsonb for audit trail
- Add test case for Foundation CSV with is_alumni + education_history
- All 41 existing tests continue passing"

git push
```

After committing, move task file to completed:

```bash
cd /Users/tam0013/Documents/git/agent-tasks

# Task file was already moved to active/ in Step 0
# Now move to completed with date folder
git mv projects/wvu-moonshot/tasks/active/2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md \
       projects/wvu-moonshot/tasks/completed/2026-09/2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md

git add projects/wvu-moonshot/tasks/completed/2026-09/2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md

git commit -m "chore: mark CSV Loader task complete

- is_alumni conversion implemented and tested
- education_history accessor working with Foundation CSV format
- All acceptance criteria met, ready for manual entry feature"

git push
```

---

## Documentation

- [ ] Update `docs/SCHEMA_DESIGN.md` to document new is_alumni column and education_history method (do this in separate MEDIUM priority task after this one completes)
- [ ] Update `docs/ARCHITECTURE.md` to reference new is_alumni handling in CSV import workflow
- [ ] No new doc files should be created during this task — flag any doc gaps for next phase

---

## Dependencies

**Blocked by**: None (this is the first critical task)
**Blocks**: 
- 2026-09-11-HIGH-FORM-MANUAL-ENTRY.md (needs is_alumni column in schema)
- 2026-09-15-HIGH-EXPORT-CSV-WITH-MANUAL.md (needs is_alumni and education_history available for export)
- 2026-09-20-HIGH-TESTS-VERIFICATION.md (depends on this task's changes)

**Related tasks**: 
- 2026-09-25-MEDIUM-DOCUMENTATION-UPDATE.md (will document this feature)

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]
**Completion date**: YYYY-MM-DD
**Final test result**: X examples, Y failures

### What was changed
- `db/migrate/[timestamp]_add_is_alumni_to_graduate_records.rb` — Created migration to add is_alumni boolean column
- `app/services/donor_csv_loader.rb` — Added convert_is_alumni helper, updated import_rows to extract and convert PRIMARY CONSTITUENCY
- `app/models/graduate_record.rb` — Added education_history accessor method returning condensed format
- `spec/services/donor_csv_loader_spec.rb` — Added test case for Foundation CSV with is_alumni conversion and education_history parsing

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: Migration + is_alumni conversion + education_history accessor all complete | Manual Entry form can now use is_alumni column and education_history method | Next: Deploy Manual Entry form task
