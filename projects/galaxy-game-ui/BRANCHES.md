# galaxy-game-ui — branch and reference layout

**Last updated**: 2026-11-08

## Intended Git branch layout

| Branch | Role | Edit? |
|--------|------|--------|
| `main` | Clean prototype and active implementation line. This is the former `review/qwen-ui-prototype` branch after the default-branch transition and rename. | **Yes** — all new implementation work belongs here. |
| `archive/first-attempt-mockdata-dashboard` | Preserved first-attempt implementation (MockDataHelper, Panel H + bay inventory, GCC header). Created from former `main` tip `67a1168`. | **No** — read-only historical reference. |
| `docs/initial-ui-wiki` | Initial UI project wiki and repository README draft, based on the clean prototype line. | Documentation review only; not the application development branch. |

## GitHub transition — remaining manual steps

The original first-attempt history is safely preserved in `archive/first-attempt-mockdata-dashboard`.

Complete the transition in this order:

1. In GitHub repository Settings → Branches, change the default branch from the current `main` (first attempt) to `review/qwen-ui-prototype`.
2. After GitHub confirms the new default, rename `review/qwen-ui-prototype` to `main`.
3. Verify that `main` now points to the clean prototype history and that `archive/first-attempt-mockdata-dashboard` still preserves the first attempt.

Do not merge the two histories. The new `main` should be the renamed clean-prototype branch, not a merge into the old first-attempt branch.

## Local git-ignored reference

- **Path**: local only (typically `galaxy-game-ui-first-attempt/` or sibling dir listed in `.gitignore`).
- **Use**: Qwen/implementers may **read** for panel markup, MockDataHelper shapes, and CSS experiments.
- **Rules**: never stage or commit; never copy whole stacks; re-implement useful patterns on `main`.

## Agent instruction (paste into tasks)

> Implement only on `main` (the clean prototype line, formerly `review/qwen-ui-prototype`). Treat `archive/first-attempt-mockdata-dashboard` and the local ignored first-attempt tree as read-only references. Do not commit new implementation work to the archive branch or the old first-attempt history.
