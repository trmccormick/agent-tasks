# Galaxy Game UI — NEEDS_REVIEW

Short, active list of items that need a second opinion or a human decision. Primary interface for review and planning agents: read this first, then `status.md`.

**Last updated:** 2026-11-03 (entry 9 added by Claude web session; drafted as text, saved by Tracy)
**Evidence labels:** VERIFIED = a command or a read of GitHub `main` shows it. REPORTED = stated by a person or agent, not re-checked.

## How this file works
- One entry per item. State: OPEN (with a specific next action and owner) or RESOLVED (with reasoning). Nothing is silently dropped.
- Move RESOLVED entries to the bottom section; prune them once they are in `status.md`.
- Implementation agents add an entry instead of resolving alone when they:
  - touch gitignored or data paths,
  - mark something complete without same-session re-verification,
  - depend on or conflict with a completed task's architecture,
  - find two docs that disagree about the same system, or
  - catch themselves repeating an identical action three or more times.
- Agents paste raw command output in entries, not summaries of it.

---

## OPEN

### 1. T3a bookkeeping not done
- **State:** OPEN — owner: Qwen (one prompt), Tracy approves
- **Evidence:** T3a is still in `tasks/drafts/` and `tasks/completed/` has no copy (VERIFIED, GitHub `main`, 2026-10-29). Code commit `b1eb0d3` on `review/qwen-ui-prototype` is REPORTED in the 2026-10-26 handoff; not re-checked (app repo not readable from the web session).
- **Next action:** move T3a to `completed/` with a short completion note; set `status: drafts` in `2026-10-23-HIGH-DASHBOARD-OVERVIEW-FROM-FIXTURES.md` (VERIFIED `status: active` while in `drafts/`); refresh `status.md` (header date still 2026-10-23, Open Sessions rows stale, 2026-10-26 closeout entry not appended).

### 2. Two lessons must go into T3a-2 before dispatch
- **State:** OPEN — owner: Claude web (draft text), Tracy applies
- **Detail:** (a) body background/text color is set in the layout in T3a-2, not in CSS; (b) new tokens live in `@theme static`.
- **Next action:** edit the T3a-2 task, then dispatch T3a-2. Do not dispatch T3a-2 on top of unreviewed CSS.

### 3. Seeds draft contradicts a decision
- **State:** OPEN — owner: Tracy
- **Detail:** the seeds draft's criterion 2 deletes `UserCsvLoader`. Tracy decided to keep it and says login/seed work (REPORTED).
- **Next action:** retire the seeds task, or reduce it to "run `db:seed`, confirm login" with the `UserCsvLoader` deletion removed.

### 4. Task lifecycle docs disagree
- **State:** OPEN — owner: planning agent, human decides
- **Detail:** the dispatch block's Step 0 moves `drafts → active`; Rule 12 as summarized in a Haiku log (REPORTED) says `drafts → backlog (human) → active`.
- **Next action:** reconcile in the 2026-10-26 template-wording draft in the agent-tasks project; confirm against the actual GUARDRAILS text first.

### 5. Possible uncommitted `status.md` edits
- **State:** OPEN — owner: Tracy
- **Detail:** a stale Haiku planning session reported uncommitted edits to `projects/galaxy-game-ui/status.md`; the routing table is already on `main`.
- **Next action:** `git status --short` in both repos and `git diff` on `status.md` before staging anything there. Also check whether `~/scratch/qwen-reports/` still exists (cleanup: `rm -r ~/scratch/qwen-reports`).

### 6. Decisions waiting on Tracy
- **State:** OPEN — owner: Tracy
- T3c routing: Claude Sonnet (premium) vs Qwen first.
- Galaxy Game accent color (`--accent` is `#4aa8ff` pending a human choice).
- Eden identifier `AOL-732356` and wormhole link types (fixtures use `unknown` until confirmed).

### 7. Later tasks to draft (not yet started)
- **State:** OPEN — owner: Claude web drafts when asked; do not dispatch
- Port base components from the first attempt (commit `8ce1250`: `eve_dashboard.scss` buttons/forms/bars/badges/utilities; `dashboard.scss` topbar/panel base) into the Tailwind entry file as plain CSS using tokens only. Comes after T3a-2.
- `landing.scss` does not match the current landing view's classes (`hero-section`, `status-grid`, `stat-large`, ...); needs a small new stylesheet. Landing comes after the dashboard.

### 8. Commit authorship is invisible
- **State:** OPEN — owner: Tracy
- **Detail:** all four task commits carry Tracy's git identity, so history does not show which agent acted.
- **Next action:** decide whether to add an `Agent: <name>` line to commit messages.

### 9. Backend work paused; wiki intent that screens may depend on
- **State:** OPEN — owner: Tracy and ChatGPT (UI and gameplay intent); Claude web keeps the wiki wording
- **Decision (REPORTED, Tracy, 2026-11-03):** no new backend code until the screen structure and gameplay intent settle. UI and wiki come first. The stale task files for the main game (`galaxy_game`: 189 in `tasks/backlog`, 328 in `tasks/review`, 224 in `tasks/completed`, counted 2026-11-03) are reconciled with the wiki before any new backend task is created. The wiki holds the design intent; tasks stay thin and link to it.
- **Wiki intent that screens may depend on** (galaxyGame `main`, commit `09bf11f`; the settlement page notes below are in an updated copy awaiting commit as of this entry):
  - `engineering/interstellar_communications.md` — ESTABLISHED INTENT: no FTL. Inside a system, communication is light-speed and delayed by distance. Between systems it is store-and-forward through craft and WS/AWS stations. Urgent messages go by a craft through the wormhole, or by a courier shuttle if no craft is due soon. DIRECTION: reach depends on power. Screens should not assume instant messages.
  - `infrastructure/settlement_infrastructure.md` — the base / outpost / settlement / city tiers are DIRECTION and not implemented, so screens should not hard-code names or population thresholds. ESTABLISHED INTENT: a space station and an orbital depot are structures, not settlement types, and an orbital structure can exist without a settlement (the Earth LEO depot is the example). DIRECTION: the market may need to work per structure for structures that are not physically connected.
  - Cycler (REPORTED, Tracy, 2026-11-03; not yet in the wiki): a cycler is a craft type, not a settlement type. It flies a long route that other craft dock with, and can serve as a temporary station during builds.
- **Code facts that affect screens** (VERIFIED, read of `main` at `09bf11f`): a market (`Market::Marketplace`) belongs to a `Settlement::BaseSettlement`; I found no market association for orbital settlements or orbital structures. `Settlement::LifeSupport#life_support_status` always returns `'operational'` (a stub). The population rule that sets a settlement's type never assigns `station`.
- **Next action:** ChatGPT: when you document a screen, note which of the items above it depends on. Anything that depends on a DIRECTION or OPEN item is marked as such in the screen notes and is not built as settled fact. Tracy decides.

### 10. Wiki spot-check feedback: unsourced numbers, authority wording, status labels
- **State:** OPEN — owner: Tracy decides; the wiki session (Qwen/Gemini) applies
- **Evidence:** web-agent spot check of galaxyGame `main` on 2026-11-03: `docs/wiki/technical/isru_fabrication_chain.md` read in full (`26a4c88`), plus status headers of four other pages (VERIFIED). Small sample: patterns to check, not a verdict on the wiki.
- **Finding 1 (sources):** the per-tier kW and thermal tables in the ISRU page are arithmetically consistent, but the three docs it cites as authority (`docs/architecture/isru/README.md`, `cnt_production.md`, `logistics/SYSTEM_INDUSTRIAL_CHAINS.md`) contain no matching figures, and the banner says the profiles were added in this expansion. Not proven invented (blueprint data was not searched properly). Label them PROPOSED/placeholder or cite a source; UI fixtures and power panels may borrow them.
- **Finding 2 (authority wording):** "If code contradicts these specs, the code is wrong" is on the ISRU page (VERIFIED today) and, per an earlier snapshot to re-check, on `eap_and_ai_manager_escalation.md` and `precursor_mission_deployment.md`. It conflicts with the wiki's own "verify against code" stance and with Tracy's rule that doc/code conflicts are discussed and he decides. Suggested wording: "a mismatch is logged as a gap for the owner to decide".
- **Finding 3 (status labels):** four status formats seen on four pages (frontmatter `status: DESIGN INTENT — owner-confirmed`, `LIVE (design intent + code-verified note…)`, a "short intent page" line, a "Code Alignment Status" banner). UI work needs one vocabulary; entry 9 already uses ESTABLISHED INTENT / DIRECTION.
- **Next action:** Tracy decides whether to pass this to the wiki session; if yes, apply findings 1-3 as small edits, one page at a time.

---

## RESOLVED

(none yet)
