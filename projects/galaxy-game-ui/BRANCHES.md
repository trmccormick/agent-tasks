# galaxy-game-ui — branch and reference layout

**Last updated**: 2026-10-23

## Git branches (remote)

| Branch | Role | Edit? |
|--------|------|--------|
| `main` | First-attempt salvage (MockDataHelper, Panel H + bay inventory, GCC header). Tip ~`67a1168`. | **No** for T3a–T3c. Preserve. |
| `review/qwen-ui-prototype` | Clean prototype. CSS foundation, shell, JSON fixtures, thin dashboard. Tip includes landing WIP + stub dashboard. | **Yes** — all new implementation. |

Do not force-push review onto main. Optional tag on main: `archive/first-attempt-mockdata-dashboard`.

## Local git-ignored reference

- **Path**: local only (typically `galaxy-game-ui-first-attempt/` or sibling dir listed in `.gitignore`).
- **Use**: Qwen/implementers may **read** for panel markup, MockDataHelper shapes, CSS experiments.
- **Rules**: never stage or commit; never copy whole stacks; re-implement patterns on `review/qwen-ui-prototype` as fixtures + views.

## Agent instruction (paste into tasks)

> Implement only on `review/qwen-ui-prototype`. Read-only reference: local ignored first-attempt tree and/or `main` history. Do not modify `main` for T3a–T3c.