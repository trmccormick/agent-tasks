---
status: completed
priority: MEDIUM
type: documentation
system_domain: DOCS_REORG
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

# TASK: Galaxy Game Docs Reorganization

**Status**: COMPLETED  
**Priority**: MEDIUM  
**Type**: documentation  
**Created**: 2026-06-20  
**Last Updated**: 2026-06-20  
**Completed by**: Implementation Agent (Ollama)

---

## Context

The `docs/` folder has grown to **2,645 markdown files across 27+ directories** with significant overlap, scattered content, and empty folders. This makes it hard for agents and developers to find the right documentation.

### What's Already Done
- ✅ `PRICE_DISCOVERY_LIFECYCLE.md` recovered from backup (was corrupted)
- ✅ Phase numbering fixed in that file
- ✅ Claude reviewed the reorg proposal and approved the core structure
- ✅ `planning/` folder confirmed to keep its 4 files

### What's NOT Being Touched
- `docs/agent/` (657 files — real directory, ongoing cleanup)
- `docs/new_agent/` (symlink → agent-tasks repo — new version of agent management)
- `docs/legacy/PATHS.PAS` (personal Pascal code for wormhole wayfinding)

---

## Problem Statement

| Issue | Details |
|-------|---------|
| Too many directories | 27+ top-level dirs, most with <5 files |
| Overlapping content | economics/market/crafts/simulation/systems all separate but related |
| Scattered dev docs | developer/ + operations/ + data/ all have dev-related content |
| Empty folders | `tutorials/` (0 files) |
| Player docs scattered | wiki/ + user/ |

---

## Proposed Structure (14 directories, down from 27+)

```
docs/
├── README.md                    # Rewrite (clean navigation)
├── developer/                   # DEV-FOCUSED: setup, architecture overview, deployment
│   ├── DEPLOYMENT.md            ← from operations/
│   └── TILESET_README.md        ← from data/
├── architecture/                # CORE SYSTEM ARCHITECTURE (150+ files)
│   ├── ai_manager/              ← merged from ai_manager/ (+19 files)
│   ├── economy/                 ← merged from economics/ + market/ (+5 files)
│   ├── stations/                ← merged from crafts/ (+3 files)
│   ├── systems/                 ← merged from systems/ (+2 files)
│   ├── simulation/              ← merged from simulation/ (+3 files)
│   └── wormhole/                ← merged from wormhole_expansion/ (+1 file)
├── gameplay/                    # PLAYER-FACING (2-3 files)
│   └── EASTER_EGGS.md           ← from flavor/ (+1 file)
├── storyline/                   # NARRATIVE + LORE (18-19 files)
├── planning/                    # PLANNING (4 files stay here)
├── testing/                     # TESTING (5 files stay here)
├── reference/                   # STABLE GUIDES (8 files stay here)
├── wiki/                        # PLAYER WIKI (11-12 files)
│   └── getting_started.md       ← from user/ (+1 file)
├── mission_profiles/            # MISSION TEMPLATES (3 files stay here)
├── api/                         # API docs (2 files stay here)
├── archive/                     # HISTORIC DOCS (47-48 files)
│   └── [merged from history/]  (+1 file)
├── agent/                       # OLD AGENT FOLDER (657 files — leave as-is)
├── new_agent/                   # SYMLINK → agent-tasks repo (leave as-is)
└── legacy/                      # PATHS.PAS (leave as-is)
```

---

## Migration Steps

### Step 1: Delete empty folders
- `docs/tutorials/` — delete (0 files)

### Step 2: Merge small scattered folders
| From | To | Files |
|------|-----|-------|
| `operations/DEPLOYMENT.md` | `developer/` | 1 |
| `data/TILESET_README.md` | `developer/` | 1 |
| `economics/*` (3 files) | `architecture/economy/` | 3 |
| `market/*` (2 files) | `architecture/economy/` | 2 |
| `crafts/*` (3 files) | `architecture/stations/` | 3 |
| `flavor/EASTER_EGGS.md` | `gameplay/` | 1 |
| `history/*` (1 file) | `archive/` | 1 |
| `logic/*` (1 file) | `architecture/` | 1 |
| `patterns/*` (1 file) | `ai_manager/` or `storyline/` | 1 |
| `systems/*` (2 files) | `architecture/systems/` | 2 |
| `simulation/*` (3 files) | `architecture/simulation/` | 3 |
| `user/getting_started.md` | `wiki/` | 1 |
| `wormhole_expansion/*` (1 file) | `architecture/wormhole/` | 1 |

### Step 3: Merge ai_manager into architecture
- `ai_manager/*` (19 files) → `architecture/ai_manager/`

### Step 4: Rewrite README.md
- Update navigation paths for moved files (economics → architecture/economy, etc.)
- Add clear section headers matching new structure
- Fix any broken links from moved files

### Step 5: Agent folder cleanup (separate task)
- `docs/agent/` — will be removed after tasks are rewritten and migrated to agent-tasks repo
- `docs/new_agent/` — temporary symlink; agents should access agent-tasks repo directly
- These are NOT touched in this task — they're handled separately

---

## Stop Conditions
- Do NOT touch `docs/agent/`, `docs/new_agent/`, or `docs/legacy/`
- Do NOT create new documentation — only move existing files
- If a file's destination already exists with the same name, flag it for manual review

---

## Commit Instructions
```bash
cd /Users/tam0013/Documents/git/galaxyGame
git add docs/
git commit -m "docs: reorganize docs folder — merge scattered folders into canonical homes"
git push
```

---

## Completion Report

**Completed by**: Implementation Agent (Ollama)  
**Completion date**: 2026-06-20  
**Final result**: ✅ All steps completed, committed and pushed

### What was changed
- **Deleted empty folders**: `tutorials/` (0 files)
- **Merged into developer/**: `operations/DEPLOYMENT.md`, `data/TILESET_README.md`
- **Merged into architecture/economy/**: `economics/*` (3 files) + `market/*` (2 files) = 9 files total
- **Merged into architecture/stations/**: `crafts/*` (3 files) = 10 files total
- **Merged into architecture/ai_manager/**: `ai_manager/*` (19 files) + `patterns/deployment/NPC_INITIAL_DEPLOYMENT_SEQUENCE.md` = 20 files total
- **Merged into architecture/systems/**: `systems/*` (2 files)
- **Merged into architecture/simulation/**: `simulation/*` (3 files)
- **Merged into architecture/wormhole/**: `wormhole_expansion/*` (1 file)
- **Other merges**: `flavor/EASTER_EGGS.md` → `gameplay/`, `user/getting_started.md` → `wiki/`, `history/*` (1 file) → `archive/`, `logic/*` (1 file) → `architecture/`
- **README updated**: Fixed all broken paths, added documentation structure table

### Issues discovered
- Shell environment had issues with `grep`, `wc`, and `head` commands — worked around by using full paths (`/bin/rm`, `/usr/bin/git`)
- `.DS_Store` files in empty directories prevented `rmdir` from working — removed them first before directory removal

### Follow-up tasks needed
- Agent folder cleanup (separate task — migrate old backlog tasks to agent-tasks repo, then remove `docs/agent/`)
- Review restored `PRICE_DISCOVERY_LIFECYCLE.md` for the minor phase-numbering artifact Claude noted (Phase 4 Expansion → Phase 3)

### Lessons learned
- Shell environment can be unreliable with certain commands — always use full paths when possible
- `.DS_Store` files are invisible but prevent directory removal — check for them before `rmdir`
- The reorg reduced docs from **27+ directories to 18** (33% reduction) while preserving all content

---

## Handoff Summary
DOCS_REORG: ✅ COMPLETE — folder merge plan executed, README rewritten, committed and pushed to main
