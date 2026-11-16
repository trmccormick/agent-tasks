# Handoff 2026-10-11 — finish the open cleanup before any new work

Paste this at the start of a fresh review session. Nothing here authorizes dispatch, commit, or push.

## Freeze

No new work until the list below is done: no ChatGPT asset work, no `orbit_radius_km` follow-up task draft (it is wanted, not urgent). One planning session and at most one implementation session open at a time. A session closes out per `SESSION_CLOSEOUT.md` / `PLANNING_AGENT_CLOSEOUT.md` before another opens. Keep all scratch scripts and logs out of the repo root; `temp-files/` exists for that.

## State (REPORTED unless a command is named)

- Topology containment: DONE and pushed. galaxyGame `7b298e0f`, `129b1a20` on origin (VERIFIED: fetch + cat-file, 10-11). agent-tasks closeout is on origin as `d190fcf` (same message as local `7049035`). Task file is in `completed/2026-10/`. Follow-up not yet drafted: `orbit_radius_km` reads a symbol key from a string-keyed hash, so radius is 0.0 and eligible routes return 0 days (confirmed for EARTH-01 in the test DB only).
- C3 (RH-400 catalog image serving, Haiku session): reported complete. galaxyGame `d7894ca3` and agent-tasks `c2b0140` are LOCAL-ONLY (VERIFIED: origin lacks both). Not yet checked against the real commit. C3b (verify/correct the same image URL) is still open. Open questions: is the Docker mount itself fixed or only masked by the fallback; `assemble_rh400_entry` hardcodes `RH400_ASSET_ID`.
- agent-tasks working tree (10-11 13:58): ` M status.md` (holds the uncommitted C3 entry, so do NOT stage it with other work); ` D` backlog/asset-ui C2 copy; untracked: `2026-10-06-SPECS-ORIGINAL-ON-GUARDED.txt` (1.2 MB SQL log), `2026-10-11-C3B-TASK-VERIFICATION-REPORT.md`, `temp-files/`.
- `2026-10-06-IMPL-CHANGES-MIDPOINT.md` was overwritten twice by a stale session re-running an old prompt (69-byte stub). Restored 10-11. If it shows ` M` again, a session is still writing to it: find that window.
- status.md has two "In Flight — Open Sessions & Staged Tasks" headings (lines 9 and 207). No `resume: true` tasks on origin.
- On origin, C2 exists in three folders: `active/` (identical to `completed/2026-10/`, md5 `7d02b045...`), `backlog/asset-ui/` (older, `status: backlog`), `completed/2026-10/`. C1 exists only in `active/` with `status: completed`. GCC Mining Scheduler is in `active/` with `status: backlog` (a deliberate hold).
- Leftovers of mine to delete: `/tmp/transit_engine_spec_orig.rb` on the host and in the `web` container.

## Steps, one at a time

1. Close every window tied to the topology task (no command).
2. C1/C2 cleanup (Tracy approves; the guard stops it if copies differ):
```
cd ~/Documents/git/agent-tasks && T=projects/galaxy_game/tasks && C2A=$T/active/2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md && C2C=$T/completed/2026-10/2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md && C2B=$T/backlog/asset-ui/2026-08-31-HIGH-FEATURE-ASSET-UI-C2-implement-catalog-data-wiring.md && C1=$T/active/2026-08-31-HIGH-FEATURE-ASSET-UI-C1-implement-asset-registry-mapping.md && [ "$(md5 -q $C2A)" = "$(md5 -q $C2C)" ] && grep -q '^status: completed' $C1 && git rm -q -- $C2B $C2A && git mv $C1 $T/completed/2026-10/ && git status --short -- $T && find $T -name '*ASSET-UI-C[12]-implement*'
```
Expect staged deletions of both stale C2 copies, a staged C1 rename, and exactly two files from `find`. Commit those three explicit paths only. Leave `status.md` out.
3. C3: save and review the commit before any push:
```
cd ~/Documents/git/galaxyGame && { date; git log -1 --stat d7894ca3; git show d7894ca3; } > ~/c3-commit.md 2>&1
```
Check it against the summary's claims (traversal guard, glob patterns, 21 tests), reconcile with C3b, record C3 as REPORTED until pushed and verified, then decide on pushing `d7894ca3` and `c2b0140`.
4. Planning agent pass (read-only inventory, then ONE proposal table; use PLANNING_AGENT_CLOSEOUT.md sections 0-4 and 6). Give it these answers and corrections: commit the C3b report; approve the C2 cleanup; look at `temp-files/` (`ls`) before deciding; delete or leave untracked the 1.2 MB SQL log; push the two local commits only after step 3. Corrections to its earlier output: C3 "VERIFIED" must be REPORTED (local-only); the midpoint file was a stale-session overwrite; add the C1/C2/GCC lifecycle items, the two Open Sessions headings, and "no resume: true tasks" to its proposal table.
5. Only then pick the next work: `orbit_radius_km` follow-up task (HIGH, backlog, paths marked [FILL IN]) and ChatGPT's asset work.

## Suggestions for Tracy (optional)

- GUARDRAILS: shared-working-tree rule (no reset, stash, restore, checkout of files, clean, or plain pull on a dirty tree in any session, not only closeout).
- WEB_AGENT_SESSION_START: read TASK_TEMPLATE.md before drafting or reviewing any task file.
- AGENT_NOTES: dated entry on the loop/context-loss overwrite failure mode.
- GUARDRAILS: scratch scripts and logs go in `temp-files/` only, and are deleted or listed under "Not committed" at closeout.
