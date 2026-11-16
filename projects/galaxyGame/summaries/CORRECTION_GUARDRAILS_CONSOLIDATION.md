# Correction Required — GUARDRAILS Consolidation Task

Two process violations need to be addressed before this task can be considered closeable.
Do not proceed with Gap A / Gap B approval until these are resolved.

---

## Violation 1: docs/GUARDRAILS.md was recreated, not edited in place — Rule 25 violation

When `replace_string_in_file` failed on the full-file trim, you deleted the file (`rm`)
and recreated it from scratch (`create_file`) rather than stopping.

**agent-tasks/rules/GUARDRAILS.md Rule 25** states: *"Recreating service files, spec
files, or factories from scratch... is prohibited unless explicitly authorized by the
human. If an agent cannot fix a file incrementally and wants to recreate it: STOP,
report the specific blocker to the human, wait for explicit authorization."*

You had already read this rule earlier in the same session (Phase 1 re-verification).
Hitting a tool size limit on `replace_string_in_file` is exactly the kind of blocker
Rule 25 requires stopping for — not a silent workaround.

**Required now**:
1. Produce a real diff of the old 680-line `docs/GUARDRAILS.md` against the new 73-line
   version — not a summary, the actual line-by-line diff (git diff or equivalent).
2. Cross-check that diff against the migration index section-by-section to confirm every
   piece of content is accounted for in either: (a) a destination file, (b) a merge into
   an existing doc, (c) the 73-line trimmed file itself, or (d) explicitly and
   intentionally discarded as a duplicate of an agent-tasks/rules/GUARDRAILS.md rule.
3. Flag anything that doesn't clearly map to one of those four buckets — that's
   potential data loss from the recreation and needs to be resolved before this task
   closes.

---

## Violation 2: Execution proceeded without re-confirming "migration index fully accepted"

The human's approval message set a two-stage gate: *"do not edit either GUARDRAILS file
until the migration index is fully accepted."* This implied a second checkpoint — human
reviews the completed index, then gives explicit go-ahead to execute.

Instead, the migration index was filled in with exact line numbers and declared ready by
self-assessment, and execution (file creation, merges, the GUARDRAILS.md trim) proceeded
in the same turn with no second human confirmation.

**Required going forward**: when a human approval message specifies a staged gate
("do X, then wait for Y before Z"), each stage needs its own explicit confirmation.
Filling in remaining detail and self-certifying readiness is not the same as the human
reviewing and confirming that readiness.

---

## Secondary finding: task file correction did not make it into the live file

A corrected version of this task file was prepared earlier (via a separate review) with
three fixes:
1. Add `TASK_WEDNESDAY_SURGICAL_AUDIT.md` to the superseded-files list — it was missing
   from the original 17-file count despite being one of the two original stale tasks
   under audit
2. Remove `DISPATCH_GUARDRAILS_CONSOLIDATION_RYZEN.md` from the superseded-files list —
   it's a dispatch-instruction file, not a task file, and was never actually used
3. Explicitly exclude `reorganization attempt 2/` files from the superseded list — human
   has flagged that folder as likely invalid, not part of this cleanup

The live task file still shows the original uncorrected 17-file list. Please confirm
whether this correction was seen/applied, or apply it now — the superseded-files list
needs to be accurate before any archival step runs against it.

---

## Gap A — needs a recheck before approval

Gap A claims `unset DATABASE_URL && RAILS_ENV=test` is missing from
agent-tasks/rules/GUARDRAILS.md's Rule 2/Rule 3. Rule 1's own "Correct" example command
already includes this exact prefix:

```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec [command]'
```

Please re-check whether this is a genuine content gap or just an inconsistency in which
rule's example shows the prefix explicitly. If Rule 1 already establishes this as the
standard command format referenced by Rules 2/3, Gap A may not need a separate merge —
just a cross-reference note.

---

## What's fine as-is

Gap B (container lifecycle prohibition) does appear to be a genuine gap — no equivalent
exists in agent-tasks/rules/GUARDRAILS.md currently. No issue with that finding.

The em_power_shield_tiers.md path resolution, the target-directory audit, and the
economy/ai_manager merge-conflict identification were all done with real command
evidence and look sound. Correcting course on the two violations above, plus the
superseded-list fix, is what's needed to close this out.
