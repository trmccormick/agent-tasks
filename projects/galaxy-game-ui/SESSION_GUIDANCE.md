# Galaxy Game UI — Session Guidance

Project-specific guidance for any agent working on the Galaxy Game UI prototype, read at startup once the project is named. Universal rules live in `rules/GUARDRAILS.md` and `SESSION_CLOSEOUT.md`; this file does not repeat them.

Created 2026-10-29 by a Claude web session, drafted as text and saved by Tracy. Updated 2026-10-30: section 8 now allows harvesting first-attempt work directly where it is usable. **Evidence label:** items marked VERIFIED were read from GitHub `main` on that date. Everything else is REPORTED (stated by Tracy or carried from `status.md` and the 2026-10-26 handoff) unless an agent re-checks it and says so. Where this file and the live repo disagree, the live repo wins; report the difference.

---

## 1. What this project is

- A **prototype UI** for Galaxy Game, built to develop and test UI choices outside the main game repo (the main repo's docs and code are mid-cleanup).
- Based on `wvu_moonshot` (a Rails 8 prototype that read CSV files); WVU-specific parts are being removed and replaced with Galaxy Game styling and views.
- It is tied closely to Galaxy Game main: what it shows and how it is shaped must follow the main game's models and data, not invent its own.
- Repo: `https://github.com/trmccormick/galaxy-game-ui`. Working branch: `review/qwen-ui-prototype` (VERIFIED in `status.md`). Remote `main` holds the first-attempt dashboard panels, preserved for reference and reuse (see section 8).

---

## 2. Startup

1. Do the universal startup in your session-start doc.
2. Read `projects/galaxy-game-ui/NEEDS_REVIEW.md` first, then this file, then the first screen of `status.md`, then the latest handoff in `projects/galaxy-game-ui/handoffs/`.
3. Check `status.md`'s "Do not implement" and "Stale docs" lists before relying on any older task or plan file.

---

## 3. UI choices come from the wiki (project rule)

Galaxy Game main is consolidating its documentation into the live wiki at `docs/wiki/` in the `galaxyGame` repo. That wiki is where design intent lives. Any UI choice made in this prototype must trace to it.

For every UI choice (layout, panel contents, names, colors, fixture shapes):

1. **Look for the intent in the wiki first** (`docs/wiki/README.md` is the front door; `status-and-gaps.md` says how far each page can be trusted).
2. **If the wiki does not cover it, research the deeper docs** (`docs/design/`, `docs/architecture/`, `docs/gameplay/`, `docs/developer/`) to find the intent. Those trees are uneven in quality; prefer the wiki when they disagree.
3. **If no intent exists anywhere, do not decide it in the prototype.** Use a clearly marked placeholder value, add an OPEN entry to `NEEDS_REVIEW.md` ("proposed wiki update: <choice>"), and let Tracy decide where it gets documented.
4. **Conflicts go to Tracy; he has the final say.** Where the wiki, the deeper docs and the code disagree, do not pick a winner silently. Add an OPEN entry to `NEEDS_REVIEW.md` stating what each source says, and discuss it to get at the core design intent. Until Tracy decides, use the main game's actual models and JSON for data shapes (the wiki itself says to verify against code), and mark the choice as provisional.
5. **Task files link to the wiki.** A UI task cites the wiki pages its choices come from. Work orders reference the wiki; they are not design truth.

Current placeholders awaiting intent (see `NEEDS_REVIEW.md` entry 6): accent color (`--accent` is `#4aa8ff`), Eden identifier `AOL-732356`, wormhole link types (fixtures use `unknown`).

---

## 4. Paths and names

`<galaxy-game-ui>` is the root of the UI app repo and `<agent-tasks>` is the root of this repo, wherever each is checked out. Never use another machine's absolute path.

| Purpose | Path |
|---|---|
| Task files | `<agent-tasks>/projects/galaxy-game-ui/tasks/` (`drafts/`, `completed/`; `active/` only while dispatched) |
| Status | `<agent-tasks>/projects/galaxy-game-ui/status.md` |
| Review queue | `<agent-tasks>/projects/galaxy-game-ui/NEEDS_REVIEW.md` |
| Handoffs | `<agent-tasks>/projects/galaxy-game-ui/handoffs/<agent>/` |
| Tailwind entry (tokens) | `<galaxy-game-ui>/app/assets/tailwind/application.css` |
| Built CSS | `<galaxy-game-ui>/app/assets/builds/tailwind.css` (rebuilt with `rails tailwindcss:build`) |
| Fixtures | `<galaxy-game-ui>/db/mock_data/` (tracked). Not `data/`. |
| Logos | `<galaxy-game-ui>/app/assets/images/logos/` (resized copies; originals live in the main repo under `data/images/logos`) |
| Container | The UI app runs in `galaxy_game`. `web` is the main game server, not this app. |

---

## 5. Critical rules

- **Architecture (approved 2026-10-24, REPORTED):** JSON fixtures read by `GameDataJsonLoader`; no `GameState`; no world data in user metadata. `UserCsvLoader` is kept (login works).
- **Fixtures go under `db/mock_data/`.** The root `.gitignore` has `/data/` anchored to the repo root, so `data/` is not tracked (GUARDRAILS Rule 28). Nothing is routed around a gitignore boundary.
- **Tailwind v4 drops unused `@theme` variables.** New design tokens go in an `@theme static { ... }` block. The WVU `@theme` block is left alone until the layout task replaces it.
- **Body background and text color are set in the layout view, not in CSS.** A `body` color rule in CSS made all default text light gray on the still-light WVU layout.
- **Check each step by running it.** After each task: rebuild, load the live page, and review a screenshot or pasted output. The first attempt failed because nothing was checked between tasks, CSS never loaded, and view/route helpers did not match.
- **No commit without explicit human approval** (GUARDRAILS Rule 26). Stage explicit paths only, never `git add -A` or `git add .`.
- **`git mv` only for task files** (GUARDRAILS Rule 12). Agents do not move tasks between `drafts/` and `backlog/`; only the human does.
- **Synthesis report before implementation for shared concerns** (GUARDRAILS Rule 17).
- **Chemical formulas and display names (REPORTED from galaxy_game guidance, confirm before relying on it):** backend data uses formulas (O2, H2O, CO2); full English names are display-layer only. Fixtures mirror backend data, and the UI turns formulas into names.

---

## 6. Working order

- Dependency order: **T3a (CSS tokens) → T3a-2 (layout shell) → T3b (JSON fixture loader) → T3c (dashboard overview)**, then the seeds task if still needed, then the landing page. T3b can run any time after T3a; T3c needs all three.
- **One task at a time.** Dispatch the next only after the previous one's output or screenshot is approved. Do not dispatch on top of unreviewed work.
- Tracy assigns the agent for each task. Planning and review agents may recommend; they do not dispatch.
- Commit messages should carry an `Agent: <name>` line so history shows which agent acted (proposed, not yet adopted; see `NEEDS_REVIEW.md` entry 8).
- Agent reports paste raw command output, not summaries of it.

---

## 7. Known stale or held items

- `tasks/drafts/2026-10-23-HIGH-DASHBOARD-EVE-CSS-FOUNDATION.md` and `TASK-3-REVISION-NOTES.md`: do not implement (GameState design).
- `INCREMENTAL-TASK-PLAN.md` Tasks 4-8: stale until rewritten after T3c.
- Seeds task: held. Its criterion 2 deletes `UserCsvLoader`, which contradicts the decision to keep it.
- Local first-attempt tree (git-ignored): reference and reuse source, see section 8.

---

## 8. First attempt: examples and reusable work

The first attempt at the dashboard was broken by running all tasks too fast, then backed up and replaced by a clean `wvu_moonshot` base. Its work is still useful: read it before writing something new, and **harvest it directly into the new version wherever it can be used**, rather than rewriting it from scratch.

Where it lives:

- **App repo, remote `main` of `galaxy-game-ui`:** first-attempt dashboard panels, preserved. Commit `8ce1250` holds `eve_dashboard.scss` (buttons, forms, bars, badges, utilities) and `dashboard.scss` (topbar, panel base). A small task after T3a-2 will port those into the Tailwind entry file as plain CSS using tokens only.
- **agent-tasks side, `projects/galaxy-game-ui-first-attempt/`:** its task files and notes, kept for reference. For example `tasks/backlog/2026-10-22-HIGH-FIX-SESSION-LOGIN-AND-DASHBOARD-STYLING.md` (VERIFIED on `main`, 245 lines) covers the WVU branding in the session view and describes that attempt's intended look ("glassmorphic cards, cyan accents"). That look is the first attempt's direction, not a decision for the rebuild.
- **Local first-attempt tree (git-ignored):** `<first-attempt path> [FILL IN: Tracy to confirm where the backup lives]`. Check it for examples (panel markup, fixtures, helpers) before inventing new ones.
- **Existing early Galaxy Game UI in the main game repo (REPORTED, not yet inventoried):** another possible source of domain-specific layouts and information organization.
- `landing.scss` from the first attempt does not match the current landing view's classes (`hero-section`, `status-grid`, `stat-large`, ...). Treat it as a style reference, not a drop-in.

How to harvest:

1. **Copy out, never write in.** Copy the file or piece you want into the new version; never edit, stage or commit the first-attempt tree.
2. **Record the source.** The task (and the commit message) names where it came from: path and commit.
3. **Adapt only what is needed.** If it works as it is, use it as it is. Replace hard-coded colors and sizes with the Galaxy Game tokens from T3a.
4. **It still passes the checks.** The choice traces to the wiki or becomes a placeholder entry (section 3), and the section 5 checks apply: rebuild, load the live page, screenshot. The first attempt's failures were unchecked steps, CSS that never loaded, and view/route helpers that did not match the app, so harvested pieces are verified like new work.
5. **One piece at a time.** Harvest one component group per task, not a whole directory.
