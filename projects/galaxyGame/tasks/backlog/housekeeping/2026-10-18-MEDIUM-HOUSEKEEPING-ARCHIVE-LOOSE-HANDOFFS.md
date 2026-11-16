# TASK: Archive loose handoff files from projects/galaxy_game/handoffs/ top level

**Status**: BACKLOG
**Priority**: MEDIUM
**Type**: documentation
**Created**: 2026-10-18
**Last Updated**: 2026-10-18

---

## Local Worker Triaging Report (Optional — for backlog review only)

- **Template Conformance**: PASS
- **Docker Wrapper Check**: N/A
- **MVP Alignment**: VALID — reduces clutter, no active tasks reference these files
- **MVP Impact Note**: Housekeeping improves signal-to-noise for future sessions
- **Action Line**: READY FOR LOCAL DISPATCH

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: [Claude session for agent-tasks repo]
**Why This Agent**: Needs full-file read/write and git mv operations
**Local attempts before cloud**: N/A
**Supervision Level**: watched carefully

---

## Prerequisites — READ FIRST (Sequential Order)

1. **This Task File**: Everything below
2. **Current state**: `projects/galaxy_game/handoffs/` — ~40 loose .md files at top level + subfolders
3. **Related**: Claude's session handoff (`session_handoff_2026-10-18_CATALOG-CONTAINMENT-AND-HOUSEKEEPING.md`) lists this as H5 housekeeping item

---

## Context

The `projects/galaxy_game/handoffs/` directory has ~40 loose `.md` files at its top level (not in any subfolder). These are session-by-session handoff logs from an early work period (June–July 2026) that ended 5+ months ago. No active tasks reference them. They total ~343KB and clutter the working directory.

### Inventory of loose files (~40 files, ~343KB total)

| Group | Count | Date Range | Size | Description |
|-------|-------|------------|------|-------------|
| Daily handoff chain | 11 | Jun 5–15 | ~90KB | Claude's daily session handoffs from an early work period |
| Session handoffs | 17 | Jun 16 – Jul 19 | ~180KB | Individual session records (Luna precursor, phase4, escalation service) |
| Special/summary files | 5 | Jun–Jul | ~30KB | Planning artifacts, checklists, workflow docs |
| Recent loose file | 1 | Oct 6 | small | `session_closeout_2026-10-06-startup-test.md` |

### Key decisions already made:
- All work from this period is captured in completed task files and architecture docs
- status.md preserves the key decisions and outcomes
- No active tasks reference these loose files
- Git history preserves everything — nothing is lost by moving to archive

---

## Problem Statement

Loose handoff files clutter the `handoffs/` directory, making it harder to find current/relevant handoffs. They serve no active purpose and should be archived.

---

## Scope of Fix

Move all loose `.md` files from `projects/galaxy_game/handoffs/` top level into `projects/galaxy_game/handoffs/archive/`. Do NOT touch files already inside subfolders (`chatgpt-free-web/`, `claude-free-web/`, etc.).

### What to move:
All `*.md` files directly in `projects/galaxy_game/handoffs/` (the ~40 loose files listed above).

### What to keep where:
- Files already in subfolders → leave untouched
- The `handoffs/` directory structure itself → leave as-is

---

## Acceptance criteria:
- [ ] All loose `.md` files moved from `projects/galaxy_game/handoffs/` top level to `projects/galaxy_game/handoffs/archive/`
- [ ] `archive/` subfolder created if it doesn't exist
- [ ] No files in subfolders were touched
- [ ] `git mv` used for all moves (preserves git history)
- [ ] Diff is reviewed and committed to agent-tasks repo
- [ ] Commit message clearly describes the archive operation

---

## Implementation Steps

### Step 1 — Inventory
Run `ls projects/galaxy_game/handoffs/*.md` and confirm the list of ~40 loose files. Verify none are referenced by active tasks or current handoffs.

### Step 2 — Create archive folder
```bash
mkdir -p projects/galaxy_game/handoffs/archive/
```

### Step 3 — Move all loose files
```bash
cd /Users/tam0013/Documents/git/agent-tasks
for f in projects/galaxy_game/handoffs/*.md; do
  git mv "$f" projects/galaxy_game/handoffs/archive/
done
```

### Step 4 — Verify
```bash
ls projects/galaxy_game/handoffs/*.md  # should show nothing (or only subdirectories)
git status --short projects/galaxy_game/handoffs/
```

### Step 5 — Review and commit
Show the diff. Wait for approval. Commit with message:
```
chore: archive loose handoff files from handoffs/ top level

Move ~40 session-by-session handoff logs (Jun–Jul 2026) from
projects/galaxy_game/handoffs/ to projects/galaxy_game/handoffs/archive/
to reduce clutter. All work is captured in completed task files and
status.md; git history preserves everything.
```

---

## Dependencies

- None — self-contained within agent-tasks repo
- After this, the `handoffs/` directory will only contain subfolders (one per agent) and the new `archive/` folder
