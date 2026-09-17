# Manual Entry Form — Implementation Synthesis

**Date**: 2026-09-15
**Task**: 2026-09-11-HIGH-FEATURE-MANUAL-ENTRY-FORM.md
**Status**: active

## What I'm About to Do

Add manual entry support for the WVU Moonshot check-in system. The migration (entry_source + linked_record_id columns) is already applied. This task adds: conditional CRM ID validation, belongs_to association, scopes, new/create controller actions, routes update, form view, "Add Person" button on index, and tests.

## Files to Change

| File | Purpose | Status |
|---|---|---|
| `app/models/graduate_record.rb` | Conditional crm_id validation, belongs_to, scopes | not started |
| `app/controllers/graduate_records_controller.rb` | new/create actions, record_params | not started |
| `config/routes.rb` | Add :new to resource | not started |
| `app/views/graduate_records/new.html.erb` | Form view (new file) | not started |
| `app/views/graduate_records/index.html.erb` | Add "Add Person" button | not started |
| `spec/models/graduate_record_spec.rb` | Update validation test for conditional crm_id | not started |
| `spec/factories/graduate_records.rb` | Add manual entry trait | not started |
| `spec/requests/graduate_records_spec.rb` | Add new/create request specs | not started |

## Prerequisites Completed

- ✅ Task file already in tasks/active/ (no move needed)
- ✅ YAML status updated: backlog → active
- ✅ Read task file with all gotchas and implementation steps
- ✅ Migration already applied (entry_source + linked_record_id columns exist)
- ✅ Understood architecture gotchas:
  - Conditional crm_id validation only for CSV entries
  - Controller explicitly sets entry_source = 'manual' per-record
  - linked_record_id FK with on_delete: nullify already in place

## Expected Outcomes

1. GET `/graduate_records/new` renders form (First Name, Last Name, Alumni checkbox, Waiver checkbox, Plus-One dropdown)
2. POST to create saves record with entry_source = "manual", crm_id = nil
3. When linked_record_id provided, PRIMARY_ADDRESSEE copied from linked record's data
4. Index page shows "Add Person" button
5. All existing tests still pass

## Critical Gotchas I Will Avoid

- ❌ Unconditional crm_id presence validation → ✅ Use conditional: `if: -> { entry_source == 'csv' }`
- ❌ Relying on migration default for manual entries → ✅ Controller explicitly sets entry_source per-record
- ❌ Forgetting to update routes → ✅ Add :new to resource

## Implementation Plan

### Step 1: Update GraduateRecord model
- Change `validates :crm_id, presence: true` to conditional based on entry_source
- Add `belongs_to :linked_record, class_name: 'GraduateRecord', optional: true`
- Add scopes: `csv_imported`, `manually_entered`

### Step 2: Update routes
- Change `resources :graduate_records, only: %i[ index show update ]` to include `:new` and `:create`

### Step 3: Update controller
- Add `new` action (builds new record)
- Add `create` action (sets entry_source = 'manual', crm_id = nil, handles linked_record copy)
- Update `record_params` to permit first_name, surname, is_alumni, waiver_completed, linked_record_id

### Step 4: Create form view
- new.html.erb with form_for fields matching task spec

### Step 5: Update index view
- Add "Add Person" button in header-actions area

### Step 6: Update tests
- Model spec: change crm_id validation test to conditional
- Factory: add `:manual_entry` trait
- Request spec: add new/create specs for manual entries

### Step 7: Run tests and verify

## Risks

- Existing CSV import unaffected (uses DonorCsvLoader, not this form)
- Conditional validation change may affect factory — need :manual_entry trait
- linked_record_id FK already exists in schema, no migration needed
