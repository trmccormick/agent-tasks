---
task_id: 2026-06-09-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-SPEC-KEYWORD-ARGS
type: bug_fix
priority: HIGH
status: active
created: 2026-06-09
last_updated: 2026-06-09
phase: LUNA_MVP
blocked_by: none
blocks: 2026-06-08-HIGH-BUG-FIX-IMPORT-REQUEST-GENERATOR-REMOVE-PLURAL-API-AND-LOGISTICS-SERVICE-COORDINATOR
---

# Bug Fix: ImportRequestGenerator Spec Keyword Arguments

## Problem

Full suite run on 2026-06-09 returned 4 failures. Two are known flaky specs (ignore).
Two are regressions in the ImportRequestGenerator spec:

```
rspec ./spec/services/logistics/import_request_generator_spec.rb:12
rspec ./spec/services/logistics/import_request_generator_spec.rb:20
```

**Root cause:** Phase 3 (commit 32b31f54) changed the canonical API signature from
positional arguments to keyword arguments:

```ruby
# OLD (positional — what specs currently call):
generate_import_request(settlement, shortage_data)

# NEW (keyword args — canonical API):
generate_import_request(source:, destination:, shortage:)
```

The implementation is correct. The specs were not updated to match the new signature.

## Fix Required

Update `spec/services/logistics/import_request_generator_spec.rb` only.
Do NOT touch the implementation file.

The specs must call the keyword argument API:

```ruby
described_class.generate_import_request(
  source: source_settlement,
  destination: destination_settlement,
  shortage: shortage_data
)
```

Review the spec file to understand what `source_settlement`, `destination_settlement`,
and `shortage_data` should be in each test context, then update the calls accordingly.

## Targets

- **Spec file (fix):** `spec/services/logistics/import_request_generator_spec.rb`
- **Implementation (read only — do not touch):** `app/services/logistics/import_request_generator.rb`

## Constraints

- Do NOT modify the implementation file
- Do NOT change what the specs are testing — only update the call signatures
- Do NOT touch any other spec files
- Do NOT run the full suite — run targeted spec only

## Testing

Run targeted spec after fix:

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/import_request_generator_spec.rb 2>&1 | tail -20'
```

**Expected result:** All examples passing, 0 failures.

Then confirm ai_manager suite still clean:

```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/ai_manager/ --format progress 2>&1 | tail -5'
```

**Expected result:** 740 examples, 0 failures, 4 pending.

## Stop Conditions

- STOP if implementation file needs changes to make specs pass — report to human
- STOP if spec changes require touching more than import_request_generator_spec.rb
- STOP if ai_manager suite count changes from 740

## Success Criteria

- `import_request_generator_spec.rb` passes with 0 failures
- ai_manager suite remains 740 examples, 0 failures, 4 pending
- Full suite regression count returns to 2 known flaky specs only
