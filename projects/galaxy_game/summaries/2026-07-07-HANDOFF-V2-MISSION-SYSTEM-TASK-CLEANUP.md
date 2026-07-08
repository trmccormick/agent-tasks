# Session Handoff — V2 Mission System Task Cleanup

**Date**: 2026-07-07
**Session Type**: Planning Agent (Claude)
**Next Session**: Implementation Agent (Qwen via Copilot)

---

## What Was Done This Session

### 1. Audited `missions_v2/` Directory Structure
Verified what Claude created as canonical examples:

| Item | Status |
|------|--------|
| `profiles/luna_base_profile_v2.json` | ✅ Exists |
| `phases/power_comms_v2.json` | ✅ Exists |
| `phases/isru_deployment_v2.json` | ✅ Exists |
| `phases/gas_processing_v2.json` | ✅ Exists |
| `phases/robot_logistics_v2.json` | ✅ Exists |
| `mission_plans/luna_precursor_mission_plan_v2.json` | ✅ Exists |
| `task_index/all_tasks.json` | ✅ Exists (maps 16 Luna tasks) |
| `tasks/*_v2.json` (17 files) | ✅ All present with template v2.1 |
| `README.md` (schema docs) | ✅ Exists |
| `manifests/` | ✅ Exists (empty — not yet populated) |
| `migration_studies/venus_foundry/` | ✅ Exists |

**Conclusion**: The canonical V2 examples are complete. Both task files reference work that's already done.

### 2. Updated status.md
Added V2 Mission System backlog section documenting both tasks need clean rewrites before dispatch.

---

## What Needs to Happen in Next Session

### Task A: Rewrite `2026-07-06-HIGH-PROFILES-V2-CANONICAL-IMPLEMENTATION.md`

**Problem**: File has two contradictory implementation sections pasted on top of each other:
- **First section (correct)**: Claude-approved scope — rake path resolution only, verify 17 tasks
- **Second section (rejected)**: Old engine-scope — TaskExecutionEngineV2 changes, LegacyPortAdapter bridge, DAG execution

**What to do**:
1. Remove the entire second implementation section (lines ~108-end of file)
2. Keep only Claude-approved scope: rake path resolution + 17-task verification
3. Fix YAML header: `status: active` → `status: backlog`
4. Fix handoff path: `tasks/active/design/` → `backlog/current/`
5. Fix output location reference: `profiles_v2/` → `missions_v2/`

**Final scope should be**:
- Update `luna_mission.rake` with `missions_v2/` path resolution + legacy fallback
- Verify 17 V2.1 tasks still execute correctly (no regressions)
- No engine changes, no LegacyPortAdapter, no DAG execution

### Task B: Clarify and Rewrite `2026-07-05-HIGH-PHASE-NORMALIZATION-REGISTRY-CREATION.md`

**Problem**: 
- References non-existent `profiles_v2/` folder everywhere (moved to `missions_v2/`)
- 4 phase files already exist in `missions_v2/phases/`
- 17 tasks already converted and present in `missions_v2/tasks/`
- Remaining scope unclear

**What to do**:
1. Determine remaining scope — likely just creating `phase_registry.json` lookup index for AI Manager
2. Update all `profiles_v2/` references → `missions_v2/`
3. Fix YAML header: `status: active` → `status: backlog`
4. Fix handoff path: `tasks/active/design/` → `backlog/current/`

**Questions to answer before rewriting**:
- Is the remaining work just `phase_registry.json`?
- Do the existing 4 phase files need schema normalization (they may not match V2 spec)?
- Is there any task mapping work beyond what's in `task_index/all_tasks.json`?

### Task C: Commit Updates to agent-tasks Repo

After rewriting both task files and updating status.md:
```bash
cd /Users/tam0013/Documents/git/agent-tasks
git add projects/galaxy_game/status.md
git add projects/galaxy_game/tasks/backlog/current/2026-07-06-HIGH-PROFILES-V2-CANONICAL-IMPLEMENTATION.md
git add projects/galaxy_game/tasks/backlog/current/2026-07-05-HIGH-PHASE-NORMALIZATION-REGISTRY-CREATION.md
git commit -m "chore: reset V2 mission system tasks to backlog, clean up conflicting content"
git push origin main
```

---

## Key Files Referenced

| File | Purpose |
|------|---------|
| `docs/new_agent/projects/galaxy_game/status.md` | Project status (updated this session) |
| `data/json-data/missions_v2/README.md` | V2 schema documentation |
| `data/json-data/missions_v2/profiles/luna_base_profile_v2.json` | Luna base profile v2.1 |
| `data/json-data/missions_v2/phases/*_v2.json` | 4 phase files (power_comms, isru_deployment, gas_processing, robot_logistics) |
| `data/json-data/missions_v2/tasks/*_v2.json` | 17 V2.1 task files |
| `data/json-data/missions_v2/task_index/all_tasks.json` | Task-to-phase mapping index |
| `galaxy_game/lib/tasks/luna_mission.rake` | Target for path resolution update |

---

## Stop Conditions for Next Session

- If any canonical file is missing or malformed — document and stop
- If rake task requires breaking changes beyond path resolution — escalate before proceeding
- If phase files don't match V2 schema — flag as separate normalization task

---

**Handoff prepared by**: Planning Agent (Claude)
**Next action**: Implementation Agent (Qwen) rewrites both task files, clarifies remaining scope, commits to agent-tasks repo
