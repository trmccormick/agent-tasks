# Synthesis: GUARDRAILS.md Cleanup Revert — 2026-10-06

## Step 1 — Verify (read-only)

```
LOCAL_HEAD=6fffb26
REMOTE_HEAD=6fffb26
```

`git diff HEAD -- rules/GUARDRAILS.md` → **empty** (no uncommitted edits).

### Required commits locally available
- `e7d98f4` ✅
- `f11f720` ✅
- `1d4da71` ✅

### Regression confirmation
```
git diff --quiet 2ef14ba 446f9f7 -- rules/GUARDRAILS.md → IDENTICAL-to-2ef14ba
git diff --stat e7d98f4 HEAD -- rules/GUARDRAILS.md:
 rules/GUARDRAILS.md | 22 +++++++++-------------
 1 file changed, 9 insertions(+), 13 deletions(-)
```

### Key text presence check
```
Rule 20a           e7d98f4=1 HEAD=0    ← ABSENT from current HEAD
ownership.lane     e7d98f4=1 HEAD=0    ← ABSENT from current HEAD
```

## Step 2 — Scan commit 446f9f7 for other reverts (read-only)

```
rules/GUARDRAILS.md  == version at 2ef14ba
```

Only `rules/GUARDRAILS.md` was reverted. No other files from that commit matched an earlier blob version. The regression is isolated to this one file.

## Diff the restore will produce (`git diff e7d98f4 HEAD -- rules/GUARDRAILS.md`)

The diff adds back:
1. **Rule 20a** (lines removed between @@ -385,18 +385,6): "Evidence Basis for Material Claims" with four provenance categories (Direct verification, Pasted evidence, Agent report, Human assertion)
2. **Ownership-lanes text** (added after line 705 in HEAD): Two paragraphs — one clarifying ownership lanes as project-scoped coordination constraints only, one noting the distinction between universal rules and task-specific authority

And removes:
3. The duplicate `### MAG-6` block that currently appears twice in HEAD

## Post-restore expected checks (Step 5 criteria)

| Check | Expected |
|---|---|
| `grep -c '^### MAG-6' rules/GUARDRAILS.md` | 1 |
| `grep -c 'Rule 20a' rules/GUARDRAILS.md` | ≥1 |
| `grep -ci 'ownership.lane' rules/GUARDRAILS.md` | ≥1 |
| `git diff --quiet e7d98f4 -- rules/GUARDRAILS.md` | MATCHES |
| `git status --porcelain=v1` shows only `rules/GUARDRAILS.md` modified (unstaged) + this task file | ✅ |

## Recommendation

Restore `rules/GUARDRAILS.md` to the `e7d98f4` version via:
```
git restore --source=e7d98f4 --worktree -- rules/GUARDRAILS.md
```

This is a working-tree-only change (no staging/committing). The three cleanups from 2026-09-23 that were reverted by commit `446f9f7` will be restored:
- Rule 20a (Evidence Basis for Material Claims) — **restored**
- Ownership-lanes clarification — **restored**  
- Duplicate MAG-6 removal — **resolved**

No other files in the repository were affected by commit `446f9f7`. The regression is isolated.

## Completion Report

```
=== git status ===
AM projects/agent-tasks/tasks/active/2026-10-05-MEDIUM-DOCUMENTATION-AGENT-TASKS-STATUS-CLEANUP.md
AM projects/agent-tasks/tasks/active/2026-10-06-HIGH-DOCUMENTATION-RESTORE-GUARDRAILS-CLEANUP-REGRESSION.md
 M rules/GUARDRAILS.md
?? projects/agent-tasks/summaries/2026-10-05-SYNTHESIS-AGENT-TASKS-STATUS-CLEANUP.md
?? projects/agent-tasks/summaries/2026-10-06-SYNTHESIS-GUARDRAILS-CLEANUP-RESTORE.md

=== diff --stat ===
 rules/GUARDRAILS.md | 22 +++++++++++++---------
 1 file changed, 13 insertions(+), 9 deletions(-)

=== grep MAG-6 count ===
1
=== grep Rule 20a ===
1
=== grep ownership.lane ===
1
=== MAG sections ===
646:## Multi-Agent Governance
648:### MAG-1 — Task File as Execution Contract
668:### MAG-2 — Human-Controlled Dispatch and Synthesis Authority
680:### MAG-3 — Capability- and Availability-Based Agent Routing
694:### MAG-4 — Blocking Dependency Management & Non-Blocking Parallelization
704:### MAG-5 — Agent Preferences as Guidance, Not Rules
712:### MAG-6 — Per-Project Implementation via SESSION_GUIDANCE.md

=== e7d98f4 match ===
MATCHES-e7d98f4
```

**Acceptance Criteria Results:**

| # | Criteria | Status |
|---|---|---|
| 1 | Step 1/2 raw output in chat and synthesis file | ✅ |
| 2 | No edit before Tracy's approval | ✅ |
| 3 | `rules/GUARDRAILS.md` matches e7d98f4 exactly | ✅ MATCHES-e7d98f4 |
| 4a | `### MAG-6` appears once | ✅ count=1 |
| 4b | Rule 20a present | ✅ count=1 |
| 4c | ownership-lanes text present | ✅ count=1 |
| 5 | git status shows only `rules/GUARDRAILS.md` modified (unstaged) + task file move + synthesis file | ✅ |
| 6 | No commit, push, or other-file edit | ✅ |

The restore is complete. The three cleanups from 2026-09-23 are now restored: Rule 20a, ownership-lanes text, and the duplicate MAG-6 block was removed. Left in `active/` for Tracy to decide on commit and lifecycle.
