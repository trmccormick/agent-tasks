# Planning session — Galaxy Game UI (2026-10-23)

**Participants**: Tracy, Grok (review), Haiku Copilot (planning), Claude (task draft/population)  
**App branches**: see `BRANCHES.md`  
**Status**: T3a–T3c reviewed GO; GameState path STOP; first-attempt preserved separately

---

## Locked decisions

1. **World data**: static JSON under `db/mock_data/` + `GameDataJsonLoader` (UserCsvLoader-style). **No** GameState model, **no** `users.metadata` for world data, **no** MockDataHelper for new work.
2. **First dashboard (T3c)**: wayfinding only (systems + links). Foothold panels (bay, orders, GCC) are **later** tasks; design source may be first-attempt, not live merge.
3. **Auth**: Rails 8 built-in session; `Current.user`, `new_session_path`, `role_admin?` / `role_player?` (prefix). Preserve login; do not strip all `wvu-*` until a dedicated task after layout is Galaxy-branded.
4. **Container**: UI app = `galaxy_game`. Main game = `web` (GUARDRAILS). Never run UI tasks in `web`.
5. **Branches stay separate**: `main` = first-attempt salvage; `review/qwen-ui-prototype` = clean prototype. No force-merge over `main`.
6. **Local ignored first-attempt tree**: read-only reference for Qwen; never `git add`. Path is local/gitignore (e.g. `galaxy-game-ui-first-attempt/`).

---

## Deployment order (implementation)

| Step | Task file | Branch | Notes |
|------|-----------|--------|-------|
| 1 | `2026-10-23-HIGH-UI-CSS-FOUNDATION.md` (T3a) | `review/qwen-ui-prototype` | Tokens in Tailwind entry; keep WVU tokens |
| 2 | `2026-10-23-HIGH-UI-LAYOUT-SHELL.md` (T3a-2) | same | After T3a preferred for screenshots |
| 3 | `2026-10-23-HIGH-JSON-FIXTURE-LOADER-SYSTEMS.md` (T3b) | same | Parallel with 1–2 OK |
| 4 | `2026-10-23-HIGH-DASHBOARD-OVERVIEW-FROM-FIXTURES.md` (T3c) | same | After T3a + T3b |
| 5 | `2026-10-23-CRITICAL-SEEDS-CLEANUP-DEFAULT-USERS.md` | same | If login/seed still broken; before signed-in screenshots |
| 6 | Landing residual | same | Hero partly done on review tip; about + AC cleanup |

**Do not dispatch**: `2026-10-23-HIGH-DASHBOARD-EVE-CSS-FOUNDATION.md` (GameState), `TASK-3-REVISION-NOTES.md` (user.metadata).

---

## Review dispositions (Grok 2026-10-23)

| Item | Disposition |
|------|-------------|
| T3a, T3a-2, T3b, T3c | **GO** |
| Seeds cleanup | **GO** (finish steps 3–6) |
| Landing | **GO with nits** — drop AC requiring EVE CSS variables in structural-only task; partial work already on review branch |
| GameState dashboard task | **STOP / archive** |
| TASK-3-REVISION-NOTES | **STOP / archive** |
| status.md / INCREMENTAL-TASK-PLAN | **Stale** — rewrite after this session |

Haiku planning review (`PLANNING-AGENT-REVIEW-4-NEW-TASKS.md`): process notes on `[FILL IN]`; population commit resolved most markers. Eden id may stay `placeholder`.

---

## Human confirmations (non-blocking)

- Eden identifier `AOL-732356`: REPORTED → use `source: "placeholder"` until confirmed from main game JSON.
- Accent color: keep `#4aa8ff` until Tracy picks Galaxy accent.

---

## Next after T3c

- Small tasks: foothold fixtures + panels (bay, units, buy orders, GCC), using first-attempt / ignored tree as **IA reference only**.
- Optional: merge review → main only after salvage is catalogued and Tracy approves.