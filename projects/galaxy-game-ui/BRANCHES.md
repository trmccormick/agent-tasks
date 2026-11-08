# galaxy-game-ui — branch and reference layout

**Last updated**: 2026-11-08

## Git branches (verified)

| Branch | Role | Edit? |
|--------|------|--------|
| `main` | Clean prototype and active implementation line. Renamed from `review/qwen-ui-prototype`; this is the repository default branch. | **Yes** — all new implementation work belongs here. |
| `archive/first-attempt-mockdata-dashboard` | Preserved first-attempt implementation (MockDataHelper, Panel H + bay inventory, GCC header). Created from former `main` tip `67a1168`. | **No** — read-only historical reference. |
| `docs/initial-ui-wiki` | Initial UI project wiki and repository README draft, based on the clean prototype line. | Documentation review only; not the application development branch. |

The GitHub default-branch change and branch rename are complete and verified. The clean prototype is now `main`; the first attempt remains preserved in the archive branch. The two histories were not merged.

## Local git-ignored reference

- **Path**: local only (typically `galaxy-game-ui-first-attempt/` or sibling dir listed in `.gitignore`).
- **Use**: Qwen/implementers may **read** for panel markup, MockDataHelper shapes, and CSS experiments.
- **Rules**: never stage or commit; never copy whole stacks; re-implement useful patterns on `main`.

## Agent instruction (paste into tasks)

> Implement only on `main`. Treat `archive/first-attempt-mockdata-dashboard` and the local ignored first-attempt tree as read-only references. The `docs/initial-ui-wiki` branch is for documentation review, not application implementation.
