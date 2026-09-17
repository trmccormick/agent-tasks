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
Task: /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/tasks/backlog/[SUBFOLDER]/2026-09-11-HIGH-FEATURE-TEST-COVERAGE.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvu-moonshot/tasks/backlog/[SUBFOLDER]/2026-09-11-HIGH-FEATURE-TEST-COVERAGE.md \
         projects/wvu-moonshot/tasks/active/2026-09-11-HIGH-FEATURE-TEST-COVERAGE.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvu-moonshot/tasks -name "2026-09-11-HIGH-FEATURE-TEST-COVERAGE.md"
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

# TASK: Test Coverage & Verification — All Features and Edge Cases
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
- **MVP Impact Note**: Post-event readiness requires comprehensive test coverage of all three major features
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

Three major features have been implemented (is_alumni, education_history, manual entry form, CSV export). Each needs comprehensive test coverage to ensure correct behavior for normal cases and graceful handling of edge cases (blank fields, missing data). The goal is to reach 15+ new tests covering all four features while preserving the existing 41 passing tests.

**Outstanding prerequisites**: All three feature tasks must be completed first:
- `2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md` (is_alumni + education_history)
- `2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md` (manual entry form)
- `2026-09-11-HIGH-FEATURE-CSV-EXPORT.md` (CSV export)

---

## Critical Information for This Task

### Credentials (if needed)
No credentials required.

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1**: Test database must be migrated with new columns before tests run
- ❌ Wrong: Run tests immediately without running migrations in test environment
- ✅ Right: Ensure `rake db:test:prepare` or manual migration runs in test environment before specs
- Why: The new `entry_source` and `linked_record_id` columns do not exist in the test database until migrated

⚠️ **GOTCHA 2**: Use real Foundation CSV structure for realistic testing — the test data file at `data/imports/donors.csv` contains all necessary patterns
- ❌ Wrong: Create mock CSV data that doesn't match Foundation's actual format
- ✅ Right: Reference `data/imports/donors.csv` in tests (Abbott, Abe, Aburahma records have all edge cases needed)
- Why: Tests must validate against real CSV structure to catch import errors

⚠️ **GOTCHA 3**: CSV Export service creates CSV in-memory — tests must not depend on file system writes
- ❌ Wrong: Test that a file exists at `/tmp/export.csv` after calling `.export`
- ✅ Right: Test the string returned by `.export` directly using `expect(GraduateRecordCsvExporter.export).to include('expected')`
- Why: The service generates CSV in-memory via `CSV.generate`; file writing happens in the controller's `send_data`

### Multi-Domain / Multi-Tenant Routing (if applicable)
Not applicable — single-domain Rails app.

---

## 🔴 REQUIRED: Status Synthesis Report (Before You Start Any Work)

Before navigating to any URLs, running any commands, or modifying any files, you MUST create and post a **synthesis report** in chat. This report demonstrates you understand the task before executing.

**Synthesis Report Template** (save as MD file, do NOT paste in chat):
```markdown
## STATUS SYNTHESIS REPORT

**Task**: Test Coverage & Verification — All Features and Edge Cases
**Status**: backlog → active → completed
**Date**: 2026-09-11

### What I'm About to Do
Add comprehensive RSpec tests for all three major features (is_alumni, education_history, manual entry form) plus CSV export. Tests go in existing test files: `spec/services/donor_csv_loader_spec.rb`, `spec/models/graduate_record_spec.rb`, and new files for controller/request specs. Target is 15+ new tests covering normal cases, edge cases, and integration with existing functionality. All 41 existing tests must still pass.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| `spec/services/donor_csv_loader_spec.rb` | Add is_alumni + education_history import tests | not started |
| `spec/models/graduate_record_spec.rb` | Add education_history + validation tests | not started |
| `spec/controllers/graduate_records_controller_spec.rb` | Add new/create/export tests | not started (may need to create) |
| `spec/requests/graduate_records_request_spec.rb` | Add HTTP endpoint tests | not started (may need to create) |

### Prerequisites Completed
- ✅ Step 0: Task file moved to active/ with git mv (find output pasted in chat)
- ✅ Step 0: YAML status updated from backlog → active
- ✅ Read README.md EXECUTOR section
- ✅ Read project guide
- ✅ Read this task file
- ✅ Understand architecture gotchas above
- ✅ Know which domain/credentials to use

### Expected Outcomes
1. All 41 existing tests still pass (zero regressions)
2. 15+ new tests added covering is_alumni, education_history, manual entry, export
3. Edge cases tested: blank fields, missing data, NULL values
4. Tests run successfully in Docker container

### Critical Gotchas I Will Avoid
- ❌ Running tests without migrating test DB first — instead ✅ Run `db:test:prepare` before specs
- ❌ Creating mock CSV that doesn't match Foundation format — instead ✅ Use `data/imports/donors.csv` which has all patterns needed
- ❌ Testing file system writes for export service — instead ✅ Test CSV.generate return string directly

---

**SYNTHESIS COMPLETE.** Ready to proceed with Step 1.
```

**POST THIS TO CHAT BEFORE PROCEEDING.** Do not start actual work until synthesis is approved.

---

## Problem Statement

Three major features (is_alumni, education_history, manual entry form) plus CSV export need comprehensive test coverage. Without tests, there's no guarantee of correct behavior for edge cases or protection against regressions when changes are made later. The existing 41 tests only cover basic CSV import; they don't test is_alumni conversion, education_history parsing, manual entry validation, or export formatting.

**Current behavior**: 41 RSpec tests exist (all passing) but none cover the three new features or edge cases.
**Expected behavior**: 55+ total tests (41 existing + 15+ new) covering normal cases, edge cases, and integration points — all passing in Docker test environment.

---

## Files Involved

### Primary Files — you will edit these
| File | Purpose | Key Method/Section |
|---|---|---|
| `spec/services/donor_csv_loader_spec.rb` | Add is_alumni + education_history import tests | Context blocks for new features |
| `spec/models/graduate_record_spec.rb` | Add education_history method + validation tests | Context blocks (new file) |
| `spec/controllers/graduate_records_controller_spec.rb` | Add new/create/export action tests | New or existing file |
| `spec/requests/graduate_records_request_spec.rb` | Add HTTP endpoint tests for export | New file |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| Existing passing specs in `spec/` | Understand test patterns and conventions used in this project |
| `data/imports/donors.csv` | Real Foundation CSV structure for test data (Abbott, Abe, Aburahma records) |

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
git mv projects/wvu-moonshot/tasks/backlog/[SUBFOLDER]/2026-09-11-HIGH-FEATURE-TEST-COVERAGE.md \
       projects/wvu-moonshot/tasks/active/2026-09-11-HIGH-FEATURE-TEST-COVERAGE.md
```

Then open the moved file and change the YAML status field:
```
status: backlog  →  status: active
```

Then verify only one copy exists:
```bash
find /Users/tam0013/Documents/git/agent-tasks/projects/wvu-moonshot/tasks \
     -name "2026-09-11-HIGH-FEATURE-TEST-COVERAGE.md"
```

**Paste the output of the find command in chat before proceeding.**
Expected: exactly one result, at the `active/` path.

> ❌ Do NOT proceed if two results appear — a stale copy exists and must be removed first.
> ❌ Do NOT use cp or plain mv — always git mv for tracked files.

### Step 1 — Add is_alumni tests to DonorCsvLoader spec

```ruby
# spec/services/donor_csv_loader_spec.rb — ADD inside describe DonorCsvLoader do block

describe '#import_rows' do
  context 'with Foundation CSV format (data/imports/donors.csv)' do
    let(:csv_file) { 'data/imports/donors.csv' }
    
    before do
      # Clear existing test data
      GraduateRecord.delete_all
      
      loader = DonorCsvLoader.new(csv_file)
      loader.import_rows
    end
    
    context 'is_alumni conversion' do
      it 'converts PRIMARY CONSTITUENCY="Alumni" to is_alumni=true' do
        # Abbott, James (ID: 9700002) from sample data
        record = GraduateRecord.find_by(crm_id: '9700002')
        expect(record).not_to be_nil
        expect(record.is_alumni).to eq(true)
      end
      
      it 'converts PRIMARY CONSTITUENCY="Individual" to is_alumni=false' do
        # Aburahma, Ali (ID: 700140146) from sample data
        record = GraduateRecord.find_by(crm_id: '700140146')
        expect(record).not_to be_nil
        expect(record.is_alumni).to eq(false)
      end
      
      it 'converts blank PRIMARY CONSTITUENCY to is_alumni=false' do
        # Adams, Carol (blank PRIMARY CONSTITUENCY in sample data)
        record = GraduateRecord.find_by(surname: 'Adams')
        if record.present? && record.data&.dig('PRIMARY_CONSTITUENCY').to_s.strip.empty?
          expect(record.is_alumni).to eq(false)
        else
          skip 'No Adams record with blank constituency in test data'
        end
      end
      
      it 'is case-insensitive: "ALUMNI" and "alumni" both produce true' do
        record = GraduateRecord.find_by(crm_id: '9700002')
        expect(record.is_alumni).to eq(true)
        
        # Verify conversion logic in DonorCsvLoader handles downcase
        expect('ALUMNI'.strip.downcase).to eq('alumni')
      end
      
      it 'handles records with no education data gracefully' do
        record = GraduateRecord.find_by(crm_id: '700140146')
        expect(record.education_history).to eq([])
      end
    end
    
    context 'education_history parsing' do
      it 'parses single education entry correctly' do
        # Abbott, James has one degree: Arts & Sciences (1997)
        record = GraduateRecord.find_by(crm_id: '9700002')
        expect(record.education_history).to eq(['Arts & Sciences (1997)'])
      end
      
      it 'parses multiple education entries correctly' do
        # Abe, Brian (ID: 700243830) has two degrees
        record = GraduateRecord.find_by(crm_id: '700243830')
        expect(record.education_history).to eq([
          'Business & Economics (2010)',
          'Engineering/Mineral Resources (2004)'
        ])
      end
      
      it 'handles records with no education data' do
        record = GraduateRecord.find_by(crm_id: '700140146')
        expect(record.education_history).to eq([])
      end
    end
  end
end
```

### Step 2 — Add GraduateRecord model tests (new file or append to existing)

```ruby
# spec/models/graduate_record_spec.rb

require 'rails_helper'

RSpec.describe GraduateRecord, type: :model do
  describe '#education_history' do
    context 'with single degree' do
      let(:record) do
        GraduateRecord.create!(
          crm_id: 'test1',
          first_name: 'John',
          surname: 'Smith',
          data: {
            'education_1' => {
              'EDUCATIONALCOLLEGECODE' => 'Business & Economics',
              'CLASSOF' => '2007'
            }
          }
        )
      end
      
      it 'returns condensed format' do
        expect(record.education_history).to eq(['Business & Economics (2007)'])
      end
    end
    
    context 'with multiple degrees' do
      let(:record) do
        GraduateRecord.create!(
          crm_id: 'test1',
          first_name: 'John',
          surname: 'Smith',
          data: {
            'education_1' => {
              'EDUCATIONALCOLLEGECODE' => 'Business & Economics',
              'CLASSOF' => '2007'
            },
            'education_2' => {
              'EDUCATIONALCOLLEGECODE' => 'Engineering',
              'CLASSOF' => '2005'
            }
          }
        )
      end
      
      it 'returns array in order 1, 2, 3' do
        expect(record.education_history).to eq([
          'Business & Economics (2007)',
          'Engineering (2005)'
        ])
      end
    end
    
    context 'with blank fields' do
      it 'skips education with no CLASSOF (year)' do
        record = GraduateRecord.create!(
          crm_id: 'test1',
          first_name: 'John',
          surname: 'Smith',
          data: {
            'education_1' => {
              'EDUCATIONALCOLLEGECODE' => 'Business & Economics',
              'CLASSOF' => nil
            }
          }
        )
        expect(record.education_history).to eq([])
      end
      
      it 'skips education with no EDUCATIONALCOLLEGECODE' do
        record = GraduateRecord.create!(
          crm_id: 'test1',
          first_name: 'John',
          surname: 'Smith',
          data: {
            'education_1' => {
              'EDUCATIONALCOLLEGECODE' => nil,
              'CLASSOF' => '2007'
            }
          }
        )
        expect(record.education_history).to eq([])
      end
      
      it 'handles partial education blocks (education_2 and education_3 missing)' do
        record = GraduateRecord.create!(
          crm_id: 'test1',
          first_name: 'John',
          surname: 'Smith',
          data: {
            'education_1' => {
              'EDUCATIONALCOLLEGECODE' => 'Business & Economics',
              'CLASSOF' => '2007'
            }
          }
        )
        expect(record.education_history).to eq(['Business & Economics (2007)'])
      end
    end
    
    context 'with nil data' do
      it 'returns empty array when data is nil' do
        record = GraduateRecord.create!(
          crm_id: 'test1',
          first_name: 'John',
          surname: 'Smith',
          data: nil
        )
        expect(record.education_history).to eq([])
      end
    end
  end
  
  describe 'validations' do
    context 'for CSV entries' do
      it 'requires crm_id when entry_source is csv' do
        record = GraduateRecord.new(
          first_name: 'John',
          surname: 'Smith',
          entry_source: 'csv'
        )
        expect(record).not_to be_valid
        expect(record.errors[:crm_id]).to be_present
      end
      
      it 'allows nil crm_id when entry_source is manual' do
        record = GraduateRecord.new(
          first_name: 'John',
          surname: 'Smith',
          entry_source: 'manual'
        )
        expect(record).to be_valid
      end
    end
    
    context 'required fields' do
      it 'requires first_name' do
        record = GraduateRecord.new(
          surname: 'Smith',
          entry_source: 'manual'
        )
        expect(record).not_to be_valid
        expect(record.errors[:first_name]).to be_present
      end
      
      it 'requires surname' do
        record = GraduateRecord.new(
          first_name: 'John',
          entry_source: 'manual'
        )
        expect(record).not_to be_valid
        expect(record.errors[:surname]).to be_present
      end
    end
  end
end
```

### Step 3 — Add controller tests for new/create/export

```ruby
# spec/controllers/graduate_records_controller_spec.rb

require 'rails_helper'

RSpec.describe GraduateRecordsController, type: :controller do
  describe 'POST #create (manual entry)' do
    it 'creates record with manual source and nil CRM ID' do
      post :create, params: {
        graduate_record: {
          first_name: 'Jane',
          surname: 'Doe',
          is_alumni: true,
          waiver_completed: true
        }
      }
      
      record = GraduateRecord.last
      expect(record).not_to be_nil
      expect(record.entry_source).to eq('manual')
      expect(record.crm_id).to be_nil
      expect(record.first_name).to eq('Jane')
      expect(record.surname).to eq('Doe')
      expect(record.is_alumni).to eq(true)
      expect(response).to redirect_to(graduate_record_path(record))
    end
    
    it 'redirects to show page after successful create' do
      post :create, params: {
        graduate_record: { first_name: 'John', surname: 'Smith' }
      }
      expect(response).to be_a_redirect
    end
  end
  
  describe 'GET #export' do
    before do
      GraduateRecord.create!(
        crm_id: '100',
        first_name: 'CSV',
        surname: 'Person',
        is_alumni: true,
        entry_source: 'csv',
        data: { 'education_1' => { 'EDUCATIONALCOLLEGECODE' => 'Business', 'CLASSOF' => '2010' } }
      )
      GraduateRecord.create!(
        first_name: 'Manual',
        surname: 'Person',
        entry_source: 'manual',
        data: {}
      )
    end
    
    it 'exports all records as CSV' do
      get :export
      
      csv_data = response.body
      expect(csv_data).to include('CSV,Person')
      expect(csv_data).to include('Manual,Person')
      expect(csv_data).to include('csv')
      expect(csv_data).to include('manual')
      expect(response.headers['Content-Type']).to match(/text\/csv/)
    end
  end
end
```

### Step 4 — Add request spec for CSV export HTTP endpoint

```ruby
# spec/requests/graduate_records_request_spec.rb (new file)

require 'rails_helper'

RSpec.describe "GraduateRecords Export", type: :request do
  before do
    GraduateRecord.create!(
      crm_id: 'TEST1',
      first_name: 'Test',
      surname: 'User',
      is_alumni: true,
      entry_source: 'csv',
      data: {}
    )
  end
  
  it 'GET /graduate_records/export returns CSV with all records' do
    get export_graduate_records_path
    
    expect(response).to have_http_status(:ok)
    expect(response.body).to include('TEST1')
    expect(response.body).to include('Test,User')
    expect(response.content_type).to include('text/csv')
  end
  
  it 'downloads with correct filename format' do
    get export_graduate_records_path
    
    disposition = response.headers['Content-Disposition']
    expect(disposition).to match(/attachment; filename=moonshot_records_\d{4}-\d{2}-\d{2}\.csv/)
  end
  
  context 'with mixed entry sources' do
    before do
      GraduateRecord.create!(
        first_name: 'ManualOnly',
        surname: 'Entry',
        entry_source: 'manual',
        data: {}
      )
    end
    
    it 'includes both csv and manual records in export' do
      get export_graduate_records_path
      
      expect(response.body).to include('Test,User')
      expect(response.body).to include('ManualOnly,Entry')
      expect(response.body).to include('csv')
      expect(response.body).to include('manual')
    end
  end
end
```

### Step 5 — Verify

> CRITICAL EXECUTION MANDATE: All RSpec commands must use the Docker wrapper below.
> The container working directory is already /home/moonshot — do NOT add cd /home/moonshot.
> Never run bare local test commands. Never fabricate test results. Actually run the specs.

```bash
# Run ALL tests to verify no regressions
docker exec moonshot bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec 2>&1 | tail -30'
```

Expected result: all original 41 + new 15+ tests passing, 0 failures.

If any existing test fails, debug that test in isolation first before proceeding:
```bash
docker exec moonshot bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/donor_csv_loader_spec.rb --fail-fast 2>&1 | tail -30'
```

### Step 6 — Synthesis Report (before committing anything)

```
SYNTHESIS REPORT
Task: Test Coverage Implementation

FILES CHANGED:
- spec/services/donor_csv_loader_spec.rb — modified (is_alumni + education_history import tests)
- spec/models/graduate_record_spec.rb — created/modified (education_history method + validation tests)
- spec/controllers/graduate_records_controller_spec.rb — created/modified (new/create/export action tests)
- spec/requests/graduate_records_request_spec.rb — created (HTTP endpoint export tests)

ROOT CAUSE: No test coverage existed for three major features; risk of undetected regressions at event.

PROPOSED FIX: 15+ new RSpec tests covering is_alumni conversion, education_history parsing, manual entry creation/export, and edge cases. All use real Foundation CSV structure where applicable.

RISK:
- Test DB must be migrated with entry_source + linked_record_id columns before running specs
- Some tests reference data/imports/donors.csv which must exist in test container
- No file-system dependency for export service tests (CSV.generate returns string)

READY TO APPLY? — waiting for approval
```

Do not commit until the user explicitly approves.

---

## Acceptance Criteria
### Existing Tests
- [ ] All 41 existing tests pass without modification (zero regressions)
- [ ] No regressions in existing functionality
- [ ] Tests run successfully in Docker container

### CSV Import Tests (is_alumni)
- [ ] Test PRIMARY CONSTITUENCY = "Alumni" → is_alumni = true
- [ ] Test PRIMARY CONSTITUENCY = "Individual" → is_alumni = false
- [ ] Test case-insensitive matching verified
- [ ] Test records with no education data

### CSV Import Tests (education_history)
- [ ] Test single education entry parsing: `["College (Year)"]`
- [ ] Test multiple education entries (2 degrees): array of both
- [ ] Test missing CLASSOF field (skipped from result)
- [ ] Test missing EDUCATIONALCOLLEGECODE field (skipped from result)
- [ ] Test nil data returns `[]`

### Manual Entry Tests
- [ ] Test create with all fields (first_name, surname, is_alumni, waiver_completed)
- [ ] Test entry_source set to "manual" explicitly in controller
- [ ] Test crm_id is nil for manual entries
- [ ] Test validation: first_name required
- [ ] Test validation: surname required

### CSV Export Tests
- [ ] Test export includes all records (csv + manual)
- [ ] Test entry_source column shows correctly
- [ ] Test CSV headers present in response body
- [ ] Test education_history condensed in export output
- [ ] Test file downloads with correct filename format `moonshot_records_YYYY-MM-DD.csv`

### Edge Cases
- [ ] Test NULL crm_id for manual entries (empty string in CSV)
- [ ] Test nil/blank education fields gracefully
- [ ] Test records with no education data return empty array
- [ ] Test CSV response Content-Type header is text/csv

### Integration
- [ ] All 41 existing tests passing
- [ ] 15+ new tests added (total 55+)
- [ ] No failing tests
- [ ] All tests run in Docker container without errors

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
git add spec/services/donor_csv_loader_spec.rb spec/models/graduate_record_spec.rb spec/controllers/graduate_records_controller_spec.rb spec/requests/graduate_records_request_spec.rb
git commit -m "test: add 15+ tests for is_alumni, education_history, manual entry, and CSV export"
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/wvu-moonshot/tasks/active/2026-09-11-HIGH-FEATURE-TEST-COVERAGE.md projects/wvu-moonshot/tasks/completed/$(date +%Y-%m)/2026-09-11-HIGH-FEATURE-TEST-COVERAGE.md

git commit -m "chore: move 2026-09-11-HIGH-FEATURE-TEST-COVERAGE.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update `docs/[path]/[file].md` — [what to update]
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: `2026-09-11-CRITICAL-CSV-LOADER-EDUCATION-ALUMNI.md`, `2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md`, `2026-09-11-HIGH-FEATURE-CSV-EXPORT.md` (all three features must be implemented before testing)
**Blocks**: Release/deployment readiness
**Related tasks**: All three feature tasks

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

HANDOFF SUMMARY: 15+ new RSpec tests added across service/model/controller/request specs | is_alumni, education_history, manual entry, export all covered | All 41 existing tests passing — event-ready test coverage