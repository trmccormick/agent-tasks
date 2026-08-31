# Planning Summary for Claude (Coordination Agent) — 2026-08-29

**Author:** Qwen (planning agent)
**Purpose:** Verified state-of-the-world for a fresh Claude coordination session.
**Method:** Every claim below was re-verified against the live filesystem and git state on 2026-08-29. Where a handoff's claim conflicts with disk, the **disk state wins** and the conflict is flagged. Do not treat the 08-27/08-28/08-29 handoffs as ground truth — several of their "done/committed" claims are not actually true on disk.

---

## 🔴 TOP PRIORITY — Verify Before Anything Else

### 1. The `can_harvest_locally?` fix is NOT committed in galaxyGame
The 08-27/08-28 handoffs state this fix (CO2 atmospheric case + O2 ISRU gate) is "genuinely implemented, tested (49 passing specs), and **committed in galaxyGame**."

**Verified reality:** It is present in the galaxyGame **working tree but UNCOMMITTED and UNSTAGED.**
- `git status` shows `M galaxy_game/app/services/ai_manager/escalation_service.rb` and `M galaxy_game/spec/services/ai_manager/escalation_service_spec.rb` (modified, not staged).
- `git diff` confirms the exact CO2 case + O2 ISRU-gate code is in the working tree.
- galaxyGame is `0 ahead / 0 behind origin/main` — i.e. **nothing** about this fix has been committed or pushed. The last galaxyGame commits (`588de210`, `fc1a50c3`) are about `data/` gitignore, unrelated to this fix.

**Action for Claude:** Decide whether to commit this fix (it appears correct and matches the spec additions) or investigate why it was left uncommitted. Do not assume it's safe because a handoff says it's committed.

### 2. agent-tasks is 12 commits AHEAD of origin (unpushed)
`git rev-list --left-right --count origin/main...HEAD` → `0 12`. The can_harvest_locally closeout commits (`c2eba47`, `d1e1100`, `5d0122e`, `d3fcdaf`, `5e3d65c`, `7c5484a`, `0153e2e`, …) are **local only**. A handoff's "confirmed committed in agent-tasks" is true for the local repo but **not pushed to remote**.

**Action for Claude:** Push agent-tasks to origin, or confirm the push is intentionally deferred.

### 3. The entire `tasks/active/` folder is UNTRACKED in agent-tasks
`git ls-files projects/galaxy_game/tasks/active/` → empty. The one active task file (`2026-07-30-MEDIUM-REFACTOR-LOOKUP-SERVICE-CACHING-PATTERN.md`) plus a `.DS_Store` are **not under git control**. If this machine is lost, the active task is lost.

**Action for Claude:** `git add` the active task file (explicit path, per the git-discipline rule) and commit, or confirm it's intentionally untracked.

---

## 🟡 DISCREPANCIES — Handoff Claims vs. Verified Disk State

### 4. `docker-compose.dev.yml` still mounts `../docs` into Docker — FORBIDDEN by the ChatGPT handoff
- Verified: line 21 of `docker-compose.dev.yml` (uncommitted `M`) reads `- ../docs:/home/docs # Mount docs directory for GalaxyGame::Paths::DOCS_PATH`.
- The 08-29 ChatGPT handoff (§7) states explicitly: **"Do not mount `docs/` into Docker just to support asset generation."**
- This is a direct, uncommitted violation of the stated runtime boundary.

**Action for Claude:** Revert this line (or get explicit sign-off that it's needed for a different reason). It is uncommitted, so reverting is low-risk.

### 5. Asset-generation SPECS are still inside Rails, even though the services were moved out
- Verified: `galaxy_game/app/services/asset_generation/` is **gone** (services correctly moved to `tools/asset_generation/` — `composition_refinery.rb`, `profile_resolution_engine.rb`, `prompt_compiler.rb`).
- **But** `galaxy_game/spec/services/asset_generation/` still contains 3 spec files (`composition_refinery_spec.rb`, `profile_resolution_engine_spec.rb`, `prompt_compiler_spec.rb`), and `tools/asset_generation/` has **no** specs.
- The ChatGPT handoff (§7, §8) requires authoring tooling to live **outside Rails**. The services obey this now; the specs do not.

**Action for Claude:** Move the 3 spec files out of `galaxy_game/spec/` (to `tools/asset_generation/` or a non-Rails spec location) so the whole authoring tooling is consistently outside the Rails runtime.

### 6. `status.md` in the working tree is STALE (reverted to 2026-08-21)
- The **committed** `status.md` (HEAD) is dated **2026-08-28** and correctly reflects the can_harvest_locally closeout + cleanup pass.
- The **working-tree** `status.md` (uncommitted `M`) has been reverted to **2026-08-21** and still lists the oxygen task as "READY FOR DISPATCH" — which is **wrong**, since that task is now in `completed/`.
- In other words, someone has an uncommitted edit that *rolls back* the status file to an older, inaccurate state.

**Action for Claude:** Discard the stale working-tree `status.md` (restore the committed 08-28 version) or reconcile it. Do not commit the 08-21 version.

### 7. Duplicate oxygen task file in `completed/` (two different versions)
- `completed/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md` (flat)
- `completed/2026-08/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md` (nested)
- Both are tracked in git, and `diff` shows they are **different** (the flat one has a "Verified Root Cause" section; the nested one has a 10-point design rationale). This is a real duplicate that should be de-duplicated to a single canonical file.

**Action for Claude:** Pick the canonical version, delete the other, commit.

---

## 📋 VERIFIED TASK STATE (agent-tasks, as of 2026-08-29)

### Active (1 file, UNTRACKED)
| Task | Location | Note |
|------|----------|------|
| Lookup Service Caching Pattern | `active/2026-07-30-MEDIUM-REFACTOR-LOOKUP-SERVICE-CACHING-PATTERN.md` | Stale since 07-30; needs dispatch-or-defer decision. **Untracked in git.** |

### Backlog — ready for dispatch
| Task | Location | Note |
|------|----------|------|
| Epoxy Resin Blueprint | `backlog/phase10-venus/2026-08-20-HIGH-DATA-CREATE-EPOXY-RESIN-BLUEPRINT.md` | READY — next dispatch item |
| Fabrication Plant Blueprint | `backlog/current/2026-08-20-HIGH-DATA-CREATE-FABRICATION-PLANT-BLUEPRINT.md` | DEFERRED (Phase 11+); shell+fit split finding logged |
| Orbital Mechanics Data Layer | `backlog/current/2026-08-19-HIGH-FEATURE-ORBITAL-MECHANICS-DATA-LAYER.md` | Phase 5 pending |
| Launch Window + Transit Timing | `backlog/current/2026-08-18-HIGH-FEATURE-LAUNCH-WINDOW-TRANSIT-TIMING-ENGINE.md` | Architecture feature |

### Completed (verified on disk)
- `completed/2026-08/2026-08-24-MEDIUM-FIX-CAN-HARVEST-LOCALLY.md` ✅
- `completed/2026-08/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md` ✅ (see duplicate #7)
- `completed/2026-08-16-MEDIUM-BUG-FIX-HARVESTER-COMPLETION-JOB-OXYGEN-FIXTURE.md` (flat duplicate)

---

## 📦 UNTRACKED / UNCOMMITTED INVENTORY

### agent-tasks (12 commits ahead of origin)
- `M CLAUDE_SESSION_START.md` (adds "Asset creative direction lives with Tracy + ChatGPT" + "Dispatch Tracking — Qwen planning session's job" standing rules)
- `M projects/galaxy_game/status.md` (STALE — see #6)
- `?? projects/galaxy_game/handoffs/chatgpt/` (the 08-29 asset/frontend handoff)
- `?? projects/galaxy_game/handoffs/claude(free web)/2026-08-27-SESSION-HANDOFF-CLOSING.md`
- `?? projects/galaxy_game/tasks/active/` (the active task file — see #3)

### galaxyGame (0 ahead/behind origin, but dirty working tree)
- `M docker-compose.dev.yml` (forbidden docs mount — see #4)
- `M galaxy_game/app/services/ai_manager/escalation_service.rb` (can_harvest fix — see #1)
- `M galaxy_game/spec/services/ai_manager/escalation_service_spec.rb` (can_harvest specs — see #1)
- `?? docs/reference/asset-generation/` (18 files: audits, specs, prompts, RH-400 run logs)
- `?? galaxy_game/spec/services/asset_generation/` (3 spec files — see #5)
- `?? tools/asset_generation/` (3 service files, correctly outside Rails)

---

## 🎯 RECOMMENDED SEQUENCE FOR CLAUDE

1. **Resolve the can_harvest_locally? commit question** (#1) — commit or investigate. This is the highest-value item and currently at risk of being lost.
2. **Revert the forbidden `docker-compose.dev.yml` docs mount** (#4) — uncommitted, low-risk, directly violates the stated boundary.
3. **Move the 3 asset-generation specs out of Rails** (#5) — completes the "outside Rails" boundary the services already obey.
4. **Fix `status.md`** (#6) — restore the committed 08-28 version; do not commit the stale 08-21 working-tree copy.
5. **De-duplicate the oxygen task file** (#7) — keep one canonical version.
6. **Track + commit the active task file** (#3) — explicit path, per git-discipline rule.
7. **Push agent-tasks to origin** (#2) — 12 commits are local-only.
8. **Then** proceed to normal dispatch work (epoxy_resin is the next ready item).

---

## ⚠️ STANDING REMINDERS (from prior handoffs, still in force)
- **Git discipline:** Always `git add [explicit path]`, never `git add .` in the shared agent-tasks repo.
- **`data/` is gitignored** — `git add -f` under `data/` is a recurring failure mode; flag explicitly in any dispatch touching that path.
- **A "clean" `git status` through a symlinked path** (e.g. `docs/new_agent/` → agent-tasks) is not proof of which repo's tree is being checked — confirm the repo.
- **Asset creative direction** belongs to Tracy + ChatGPT; Claude's role is logging/verification, not steering style.
- **Qwen planning session** must log dispatches (task/host/timestamp) the moment work goes out.
- **Green tests are not sufficient sign-off** for shared/global code — Synthesis Report + approval required.

---

## 📝 VERIFICATION LOG (what I actually checked, 2026-08-29)
- `find` over `tasks/{active,backlog,completed}` in agent-tasks → confirmed active=1, located all key task files.
- `git status --short` + `git log --oneline` + `git rev-list --left-right --count origin/main...HEAD` in **both** repos.
- `git ls-files` on `tasks/active/` (empty → untracked) and on both oxygen file paths (both tracked).
- `diff` between the two oxygen files (different content).
- `git diff` on `escalation_service.rb`, `escalation_service_spec.rb`, `docker-compose.dev.yml`, `status.md`, `CLAUDE_SESSION_START.md`.
- `find` over `tools/asset_generation/` and `galaxy_game/spec/services/asset_generation/` (services moved out, specs still in).
- `grep -n docs docker-compose.dev.yml` (line 21 mount confirmed).
- `grep -nE "tools|data|docs" .gitignore` (confirmed `/data/` excluded, `tools/` not excluded).
