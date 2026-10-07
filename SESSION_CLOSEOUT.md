# Session Closeout — Shared Procedure

Applicable to planning, research, implementation, review, or any combination.
Model-neutral. Applies regardless of which agent ran the session.

---

## 1. Status.md Maintenance (mandatory)

Append a concise dated entry describing:
- **Actual work done** — what was completed, not what was attempted
- **Verification** — how you confirmed it worked (test results, git hashes, manual checks)
- **Outcome** — the result in one sentence
- **Next action** — what should happen next, by whom
- **Commit references** — if applicable, include commit hashes

Condense older entries as needed:
- Fold entries older than roughly the last week into a short 2–4 line summary block
- Preserve: what shipped (commit hashes), still-open blockers/follow-ups, standing guardrails/lessons
- Drop routine step-by-step narration once it's no longer actionable
- If status.md is growing large enough that condensing doesn't keep it manageable, archive older condensed history to `status_archive_YYYY-MM.md` and leave a one-line pointer in status.md

Preserve unresolved blockers, follow-ups, standing constraints, and useful decision/evidence history. Leave an archive pointer when history is archived. No arbitrary requirement to remove more text than was added.

---

## 2. Task Lifecycle Reconciliation

Move canonical task files using `git mv`. Verify one canonical copy exists with no stale source copy.

- **Completed work**: record outcome and verification, update status, move to `completed/`
- **Held work**: record hold reason and restart condition, update status, move to `backlog/current/`
- **Genuinely continuing**: keep in `active/` with an accurate next action

**Do not**:
- Copy tasks — use `git mv` for tracked moves
- Change tasks owned by another running session
- Leave completed or held tasks in `active/`

---

## 3. Artifact Cleanup

Clean up relevant stale drafts, superseded reports, and duplicate artifacts:
- Archive useful history; remove confirmed disposable material
- Age alone is not grounds for deletion — ask when ownership or retention is genuinely uncertain
- Do not turn cleanup into a broad audit

---

## 4. Closeout Handoff

Leave a brief statement of:
- Maintenance performed (what was updated, moved, archived)
- Exceptions (tasks owned by another session, unresolved restrictions, anything left as-is with reason)

---

## Sessions Without Filesystem Access

If the session has no filesystem access:
- Draft closeout artifacts as text for an authorized local session to save
- Do not claim artifacts were saved when they were not
- Include full content in the handoff so a local session can apply it
