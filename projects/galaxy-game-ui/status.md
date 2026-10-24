# Galaxy Game UI — Status

**Last updated**: 2026-10-23  
**Canonical plan**: `PLANNING-SESSION-2026-10-23.md` · **Branches**: `BRANCHES.md`

## Active track

- **Branch**: `review/qwen-ui-prototype`
- **Next dispatch order**: T3a → T3a-2 → T3b (one at a time) → T3c → seeds if needed → landing residual
- **Architecture**: JSON fixtures + GameDataJsonLoader; no GameState; no world data in user metadata
- **Approval**: T3a, T3a-2, T3b, T3c — reviewed GO by Grok (2026-10-23); approved by Tracy (2026-10-24) with edits applied (commit 9161b65).

## Routing Recommendation (Planning Agent Assessment — Tracy to approve)

| Task | File | Complexity | Write Targets | Recommended Agent | Rationale |
|------|------|-----------|---------------|-------------------|-----------|
| **T3a** | `2026-10-23-HIGH-UI-CSS-FOUNDATION.md` | Low | 1 file (CSS) | Qwen (local) | Single-file CSS, no functional risk |
| **T3b** | `2026-10-23-HIGH-JSON-FIXTURE-LOADER-SYSTEMS.md` | Medium | 3 files (service + data) | Qwen (local) | Ruby service pattern is straightforward; no UI impact |
| **T3a-2** | `2026-10-23-HIGH-UI-LAYOUT-SHELL.md` | Med-High | 1 file (layout, 26 wvu refs) | Qwen (local) | Single file but high visual impact — local first per Rule 23 |
| **T3c** | `2026-10-23-HIGH-DASHBOARD-OVERVIEW-FROM-FIXTURES.md` | High | 2 files + potential tests | Claude Sonnet (premium) | Multi-file coordination; higher risk of subtle bugs |

**Parallel execution groups**:
1. **Group A**: T3a + T3b (no file conflicts, but run one at a time: T3a first, then T3b)
2. **Group B**: T3a-2 (depends on T3a only — run after Group A passes)
3. **Group C**: T3c (depends on T3a + T3a-2 + T3b — run after all three are approved)

### Dependency Graph
```
T3a ──→ T3a-2 ──→ T3c
  │                           ↑
  └─────── T3b ──────────────┘
```

### Key Constraints
- **Rule 26**: No autonomous git commit or push — all commits require explicit human approval
- **Rule 28**: `data/` is gitignored — fixtures go under `db/mock_data/` (tracked)
- **Rule 17**: Synthesis reports required before implementation for shared concerns
- **Container name**: UI app = `galaxy_game` (NOT `web`, which is main game server)

## Open Sessions

| Session Label | Agent | Task | State | Last Touched | Local Commits | Notes |
|---|---|---|---|---|---|---|
| T3a-implementation | **(Tracy to assign)** | 2026-10-23-HIGH-UI-CSS-FOUNDATION | approved, pending dispatch | 2026-10-23 | No | Group A — low risk |
| T3b-implementation | **(Tracy to assign)** | 2026-10-23-HIGH-JSON-FIXTURE-LOADER | approved, pending dispatch | 2026-10-23 | No | Group A — independent of T3a |
| T3a-2-implementation | **(Tracy to assign)** | 2026-10-23-HIGH-UI-LAYOUT-SHELL | depends on T3a | 2026-10-23 | No | Group B — after T3a approved |
| T3c-implementation | **(Tracy to assign)** | 2026-10-23-HIGH-DASHBOARD-OVERVIEW | depends on T3a + T3a-2 + T3b | 2026-10-23 | No | Group C — after T3a/T3a-2/T3b approved |

## Do not implement

- `tasks/drafts/2026-10-23-HIGH-DASHBOARD-EVE-CSS-FOUNDATION.md` (GameState)
- `tasks/drafts/TASK-3-REVISION-NOTES.md`

## Preserved work

- Remote `main`: first-attempt dashboard panels
- Local git-ignored first-attempt tree: Qwen reference only

## Stale docs (ignore until rewritten)

- Older Phase 1 CSS blocker narrative in previous status
- `INCREMENTAL-TASK-PLAN.md` Tasks 4–8 (rewrite after T3c)
