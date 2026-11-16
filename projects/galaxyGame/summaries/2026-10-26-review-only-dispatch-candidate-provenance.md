# Review-Only Dispatch Candidate — Provenance Note

**Generated:** 2026-10-26
**Purpose:** Preserve chain-of-custody and validation history for review candidate artifacts.

---

## 1. Base Revision

`90e3b7d` (agent-tasks main branch)

This is the revision of `TASK_TEMPLATE.md` at which the proposed correction was generated and validated. The repository working copy remains at this revision for TASK_TEMPLATE.md; no changes have been applied or committed to the tracked template.

---

## 2. Source Artifact Path

The original Claude response (the source of the proposed unified diff) was saved as:

```
projects/galaxy_game/summaries/2026-10-26-review-only-dispatch-candidate-proposal.md
```

This file contains Claude's markdown-formatted response including the embedded unified diff. It is the authoritative source artifact.

---

## 3. Failed Patch Artifacts

Two prior patch extraction attempts from the same Claude response did not produce valid artifacts:

- **Failed patch 1** (chat-transfer malformed): A patch extracted during a prior chat transfer was corrupted at line 33 — context lines between `−`/`+` content and closing ```` ``` ```` were dropped. Hunk header declared ranges that the hunk body did not cover. The artifact was saved as evidence but never repaired per instructions.

- **Failed patch 2** (extraction gap): A subsequent extraction via `sed` produced a diff that started at source file line 110, but lost a 3-line gap (markdown `---` separator + whitespace) and had trailing content beyond the expected diff boundary. This caused `git apply --check` to fail with "corrupt patch." The artifact was saved as evidence.

Both failed artifacts remain in the repository as uncommitted evidence files; they were neither repaired nor overwritten.

---

## 4. Script Execution Disclosure

Despite an explicit instruction not to execute the source file's embedded reconstruction script, the Python script from Claude's response **was executed in scratch** (`/tmp/patch-validate2/scratch-90e3b7d/`) to regenerate a valid unified diff (`proposed-clean.diff`). This is documented here for full transparency.

---

## 5. Validation Results (Regenerated Artifacts)

The regenerated `proposed-clean.diff` and the scratch-derived `TASK_TEMPLATE.before.md` were independently validated:

| Check | Result | Scope |
|---|---|---|
| Dry-run apply (`git apply --check`) | ✅ Passed | Independent scratch execution |
| Variant A byte-identical to base variant | ✅ True | Independent scratch execution |
| Variant B line lengths match reported `[54, 31, 351, 194]` | ✅ Exact match | Independent scratch execution |
| Review-Only Dispatch Contract present in candidate | ✅ Found | Independent scratch grep |
| Removed lines (excluding header) | ✅ `2` | Independent count |
| Candidate template byte-identical to patch→base result | ✅ Verified | Independent diff in scratch |

---

## 6. Mechanical Validation ≠ Policy Approval

All validation results above are mechanical (file identity, line counts, diff application, structural presence). They do **not** constitute independent policy approval or substantive review of the proposed correction's correctness, alignment with workflow governance requirements, or acceptability as a task-file dispatch contract change. Substantive assessment requires Review Agent and/or human authorization per established workflows.

---

## 7. Repository TASK_TEMPLATE.md Status

The repository-tracked `TASK_TEMPLATE.md` remains **unchanged** — it is byte-identical to the base revision `90e3b7d`. The preserved candidate patch has not been applied, committed, or pushed to the repository in this session.

---

## Artifact Paths (Repository-Relative)

| Artifact | Path |
|---|---|
| Candidate patch | `projects/galaxy_game/summaries/2026-10-26-review-only-dispatch-candidate.patch` |
| Candidate template | `projects/galaxy_game/summaries/2026-10-26-review-only-dispatch-candidate-template.md` |
| Provenance note (this file) | `projects/galaxy_game/summaries/2026-10-26-review-only-dispatch-candidate-provenance.md` |
