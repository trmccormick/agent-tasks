---
status: completed
priority: HIGH
type: documentation
system_domain: AGENT_WORKFLOW
mvp_alignment: CORE_OPERATIONS
local_worker_safe: true
cross_project_impact: true
---

> **Draft authored by Claude (review tier) on 2026-10-06 from GitHub `main` at `6fffb26`.** Claude could not see Tracy's local checkout. Facts below were checked against GitHub history; Step 0 and Step 1 re-verify them locally. This task is a draft for Tracy and is NOT dispatched.

## 🔴 Task Readiness Checklist (Human — before dispatching)

- [ ] Tracy has read the Context and agrees the file should be restored
- [ ] No other session has uncommitted edits to `rules/GUARDRAILS.md`
- [ ] Tracy has confirmed dispatch for this task

---

## 🔴 Agent Dispatch Interface (copy EXACTLY to send to agent)

```
You are **Implementation Agent**.

Project: agent-tasks
Task: projects/agent-tasks/tasks/backlog/2026-10-06-HIGH-DOCUMENTATION-RESTORE-GUARDRAILS-CLEANUP-REGRESSION.md
(Paths are relative to the agent-tasks repo root on the host. Run all commands from that root.)

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE:
  New/untracked file: mv it to projects/agent-tasks/tasks/active/ then git add that path.
  Tracked file: git mv. Then change YAML: status: backlog → status: active.
  Paste command output and: find projects/agent-tasks/tasks -name "2026-10-06-HIGH-DOCUMENTATION-RESTORE-GUARDRAILS-CLEANUP-REGRESSION.md"
  (exactly ONE result).

READ FIRST (after Step 0): the task file contains scope, steps, and stop conditions.

CRITICAL: Save the synthesis as an MD file before any edit:
  projects/agent-tasks/summaries/2026-10-06-SYNTHESIS-GUARDRAILS-CLEANUP-RESTORE.md
  In chat post only the path and a 3-line summary, then WAIT for Tracy's approval.
```

---

# TASK: Restore the GUARDRAILS.md cleanup reverted by a bulk commit

## Context (verified against GitHub history; re-verify locally)

Three governance cleanup commits on 2026-09-23 changed `rules/GUARDRAILS.md`:
- `1d4da71` removed the duplicate MAG-6 block
- `f11f720` added the project ownership-lanes clarification
- `e7d98f4` added Rule 20a, Evidence Basis for Material Claims (four provenance categories)

Commit `446f9f7` (2026-10-05 21:50, message "commit: 22 files — .gitignore update, GUARDRAILS.md, galaxy_game status.md, ...") then changed `rules/GUARDRAILS.md` by +9/−13 lines. The resulting file is **byte-identical to the version at `2ef14ba`** (before the cleanup). Effect on current `main`:
- Rule 20a is absent
- Ownership-lanes text is absent
- `### MAG-6` appears twice

`e7d98f4` is the last version of the file with all three cleanups. On GitHub, no commit other than `446f9f7` touched this file after `e7d98f4`.

The cause and intent of the revert are unknown. This task restores the file only if Tracy approves after seeing the diff.

## Scope

1. Verify the regression locally (Step 1).
2. Report, read-only, whether the same commit reverted any of its other files (Step 2).
3. After Tracy's approval, restore `rules/GUARDRAILS.md` in the working tree to the `e7d98f4` version (Step 4).

## Out of Scope — DO NOT

- Fix or restore any file other than `rules/GUARDRAILS.md`, even if Step 2 finds more. Report only.
- Edit anything else in `GUARDRAILS.md`, add rules, or renumber.
- Stage, commit, or push (Rule 26). Restore into the working tree only.
- Overwrite uncommitted changes (see Stop Conditions).
- Touch gitignored paths (Rule 28) or run git inside Docker.

## Steps

**Step 1 — Verify (read-only).** Paste raw output of:
```
git fetch origin -v
git rev-parse --short HEAD origin/main
git status --porcelain=v1
git diff HEAD -- rules/GUARDRAILS.md
git log --format='%h %ad %s' --date=short -- rules/GUARDRAILS.md | head -8
git diff --quiet 2ef14ba 446f9f7 -- rules/GUARDRAILS.md && echo IDENTICAL-to-2ef14ba || echo differs
git diff --stat e7d98f4 HEAD -- rules/GUARDRAILS.md
for k in 'Rule 20a' 'ownership.lane'; do printf "%s e7d98f4=%s HEAD=%s\n" "$k" "$(git show e7d98f4:rules/GUARDRAILS.md | grep -ci "$k")" "$(grep -ci "$k" rules/GUARDRAILS.md)"; done
```
Expected: `git diff HEAD -- rules/GUARDRAILS.md` is empty, and the e7d98f4→HEAD diff is exactly the revert (+9/−13). Anything else is a Stop Condition.

**Step 2 — Scan the same commit for other reverts (read-only, report only).** For each file changed by `446f9f7` (`git show --name-only --format= 446f9f7`), report whether its content at `446f9f7` equals the blob from any earlier commit other than its parent:
```
for f in $(git show --name-only --format= 446f9f7); do
  b=$(git rev-parse "446f9f7:$f" 2>/dev/null) || continue
  for c in $(git log --format=%h 446f9f7^ -- "$f"); do
    [ "$(git rev-parse "$c:$f" 2>/dev/null)" = "$b" ] && { echo "$f  == version at $c"; break; }
  done
done
```
Paste the output. Do not act on any hit.

**Step 3 — Synthesis and approval gate.** Save the synthesis file (path in the Dispatch Interface) containing Step 1 and Step 2 output, the exact diff the restore will produce (`git diff --stat` and full `git diff e7d98f4 HEAD -- rules/GUARDRAILS.md`), and the post-restore checks. Post path plus a 3-line summary. WAIT for Tracy.

**Step 4 — Restore (only after approval).**
```
git restore --source=e7d98f4 --worktree -- rules/GUARDRAILS.md
```
Do not use `--staged`. Do not stage the file.

**Step 5 — Verify and report.** Paste:
```
git status --porcelain=v1
git diff --stat -- rules/GUARDRAILS.md
grep -c '^### MAG-6' rules/GUARDRAILS.md        # expect 1
grep -c 'Rule 20a' rules/GUARDRAILS.md          # expect ≥1
grep -ci 'ownership.lane' rules/GUARDRAILS.md   # expect ≥1
grep -n '^## Multi-Agent Governance\|^### MAG-[1-6]' rules/GUARDRAILS.md
git diff --quiet e7d98f4 -- rules/GUARDRAILS.md && echo MATCHES-e7d98f4
```
Add a Completion Report below. Leave the task in `active/`; Tracy decides on commit and on moving it to `completed/`.

## Gotchas

- Do not assume why `446f9f7` changed the file. Report facts; Tracy decides.
- Another Qwen session may be editing other files in the same checkout. Touch only `rules/GUARDRAILS.md` and this task's own summary file.
- Rule 30: the synthesis file goes in this repo's `summaries/`, never a code repo.

## Acceptance Criteria

1. Step 1 and Step 2 raw output is in chat and in the synthesis file.
2. No edit before Tracy's approval of the diff.
3. After Step 4, `rules/GUARDRAILS.md` matches the `e7d98f4` version exactly.
4. `### MAG-6` appears once; Rule 20a and ownership-lanes text are present; MAG-1 to MAG-6 each appear once.
5. `git status --porcelain=v1` shows only `rules/GUARDRAILS.md` modified (plus this task's own move and synthesis file); nothing staged beyond the task move.
6. No commit, push, or other-file edit occurred.

## Stop Conditions

Stop and report, without improvising, if:
- `git diff HEAD -- rules/GUARDRAILS.md` is not empty (uncommitted edits would be overwritten).
- `git diff --stat e7d98f4 HEAD` shows anything beyond the revert (for example intended additions after `e7d98f4`).
- Local `HEAD` lacks `e7d98f4`, `f11f720` or `1d4da71`.
- Two copies of this task file exist in different lifecycle folders.

## Completion Report

```
255e9e7 2026-10-06 docs(governance): restore GUARDRAILS.md cleanups reverted by 446f9f7 (Rule 20a, ownership lanes, MAG-6 dedupe)

 rules/GUARDRAILS.md | 22 +++++++++++++---------
 1 file changed, 13 insertions(+), 9 deletions(-)
```

Committed and pushed as `255e9e7` (2026-10-06). The restore brought working tree to byte-identical with `e7d98f4`: Rule 20a evidence-basis convention present, ownership-lanes text present, duplicate MAG-6 block removed. Post-restore verification: MATCHES-e7d98f4; grep count for `### MAG-6` = 1; grep counts for `Rule 20a` and `ownership.lane` = ≥1.
