# galaxy-game-ui — branch and reference layout

**Last updated**: 2026-11-08

## Git branches (remote)

| Branch | Role | Edit? |
|--------|------|--------|
| `archive/first-attempt-mockdata-dashboard` | Preserved first-attempt implementation (MockDataHelper, Panel H + bay inventory, GCC header). Created from former `main` tip `67a1168`. | **No** — read-only historical reference. |
| `review/qwen-ui-prototype` | Current clean prototype and active implementation line. | **Yes** — new implementation work belongs here. |
| `docs/initial-ui-wiki` | Initial UI project wiki and repository README draft, based on `review/qwen-ui-prototype`. | Documentation review only; not the application development branch. |

### Default branch transition

The first-attempt branch has been preserved as `archive/first-attempt-mockdata-dashboard`. The repository's GitHub default branch still needs to be changed from `main` to `review/qwen-ui-prototype` in repository Settings → Branches. Only after that default-branch change should the old `main` branch name be retired (rename it to the archive name if still present, or delete it once the archive branch is verified). Do not delete the first-attempt history.

## Local git-ignored reference

- **Path**: local only (typically `galaxy-game-ui-first-attempt/` or sibling dir listed in `.gitignore`).
- **Use**: Qwen/implementers may **read** for panel markup, MockDataHelper shapes, and CSS experiments.
- **Rules**: never stage or commit; never copy whole stacks; re-implement useful patterns on `review/qwen-ui-prototype`.

## Agent instruction (paste into tasks)

> Implement only on `review/qwen-ui-prototype`. Treat `archive/first-attempt-mockdata-dashboard` and the local ignored first-attempt tree as read-only references. Do not commit new implementation work to the archive branch or the former `main` line.
