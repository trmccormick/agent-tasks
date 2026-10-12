# Session Handoff — 2026-10-11 — Workflow review (web session)

Written by the web review agent at the end of a long session, so a clean session can start tomorrow. Evidence labels: **VERIFIED** = read on origin at 626178b or checked with a command; **REPORTED** = stated by Tracy or an agent log, not re-checked. People are named by role only (public repo).

## How to start the next session

1. **Local planning session (Qwen or whichever model is available):** read `PLANNING_AGENT_SESSION_START.md`, then `PLANNING_AGENT_CLOSEOUT.md`, then `notes/AGENT_NOTES.md`. Stay the planning session: spawn research or implementation work as separate sessions (context length).
2. **Web review session (Claude, ChatGPT, Perplexity, etc.):** read `WEB_AGENT_SESSION_START.md`, including the new "Handing over changes" section, before doing anything.
3. Read this handoff, then the first screen of `projects/agent-tasks/status.md`.
4. Pacing: one request at a time, wait for the reply. No scripts. Stage explicit paths only; path-limited commits; Tracy commits and pushes.

## What landed today (VERIFIED on origin, 626178b)

| Commit | What |
|---|---|
| `b44b6c3` | `SESSION_CLOSEOUT.md` trimmed to research/implementation sessions (adds stop-early, section 2a); new `PLANNING_AGENT_CLOSEOUT.md` |
| `282d816` | `summaries/2026-10-11-CLOSEOUT-SPLIT-INSERTS.md` (the follow-up edits list) |
| `6462d0f` | Pointers in `PLANNING_AGENT_SESSION_START.md`; README index lines; `resume` field and Progress / Resume block in `TASK_TEMPLATE.md` |
| `f5260b8` | Session Log entry for the closeout-split edits |
| `2ff669e` | `projects/agent-tasks/status.md` header, In Flight and Recent Closures refreshed; stale claims removed |
| `7419cfd` | README `[cite: 3]` tags removed |
| `626178b` | `WEB_AGENT_SESSION_START.md`: "Handing over changes" section |

## Not done yet (nothing here has been changed)

1. **Four "Pending Items" appends** to project `status.md` files: `wvulibraries_acda_portal`, `samvera_hyku`, `wvulibraries_databases`, `wvulibraries_knapsack`. Content comes from `summaries/2026-10-07-PROJECT-FACTS-FROM-TRACY.md` (REPORTED). One file per round, small append, check `git diff --stat`, then commit. ACDA: production already has the good_job update (REPORTED by Tracy, 2026-10-11), so the Phase 1a/1b text in that file is probably out of date; confirm with Tracy before curating it. Draft wording was in the web session only, so redraft from the facts file.
2. **`SESSION_CLOSEOUT.md` section 2 vs 2a:** section 2 sends held work to `backlog/current/`; 2a (stop-early) sends it to `backlog/`. Decide one and fix. (VERIFIED, both lines on origin.)
3. **Files that mention `SESSION_CLOSEOUT` without a section number** and were not reviewed: `PLANNING_AGENT_WORKFLOW.md`, `REVIEW_AGENT_GUIDE.md`, `REVIEW_AGENT_WORKFLOW.md`, `projects/galaxy_game/SESSION_GUIDANCE.md`. Check they still read correctly after the split.
4. **Open decisions for Tracy:** Rule 21 (stale, or a routing preference) and the Galaxy-specific part of Rule 18.
5. **Not yet exercised:** the stop-early routine, the `resume: true` flag and the Open Sessions table are written but untested. Watch the first real use.
6. **Leave alone:** other `[cite: 3]` tags in eight archived or dated Galaxy files; `workflow-updates/qwen-session-closout-log.md` (untracked on one laptop, not this session's).

## Galaxy items (parked; Tracy said no Galaxy task edits today)

- **C3b, RH-400 catalog image URL.** Owned by ChatGPT on another laptop (REPORTED). Findings from the review of the Qwen/Haiku session log:
  - galaxyGame `main` is still at `b816d8e` on origin; Haiku's fix commit is local only (VERIFIED: origin clone; REPORTED: the local commit).
  - `ASSETS_PATH` is `JSON_DATA/images`, so the existing `/api/assets/*path` URL is relative to `images/` (e.g. `catalog/crafts/ground/rh400_concept.png`). Haiku's new `/admin/catalog/image/*path` route duplicates it and used a different base (VERIFIED in code).
  - Two RH-400 render candidates exist (`rh400_concept.png`, `rh400_regolith_harvesting_rover.png`) (REPORTED in the log). The C3b task says to stop and ask when there is more than one. Tracy has not yet chosen.
  - The visual definition exists at repo-root `docs/reference/asset-generation/visual_definitions/` but compose mounts only `./galaxy_game`, so the registry cannot see it in the container (VERIFIED from compose and code). Haiku's hardcoded RH-400 fallback hides that.
  - Haiku's commit used `git add -A`; check `git show --stat` for swept-in files (`tools/diag_rh400*.rb`, `docs/new_agent/...`). A hidden `app/data/.diag_rh400_v2.rb` was written into the json-data mount.
  - Tracy holds `HAIKU-PROMPT-C3B-PHASE1.md` and `C3B-ADJUSTMENT-PLAN.md` (downloaded from the web session; not in the repo).
- **C3 status entry** in the Galaxy `status.md` is still missing. Write it only after the C3b work lands, labelled VERIFIED/REPORTED.
- **C2 task file has three copies on origin** (`active/`, `backlog/asset-ui/`, `completed/2026-10`) (VERIFIED). Proposal for Tracy's approval, not applied: keep `completed/2026-10`, remove the other two.
- A transit stash and an untracked `SPECS-ORIGINAL-ON-GUARDED.txt` are parked on the work machine (REPORTED). They belong to a paused session. Do not touch.

## Workflow topics Tracy wants to discuss (not decided)

- **Delivery pattern.** Today showed no clear pattern. The "Handing over changes" rules are now written; is that the pattern, or does it need changing?
- **Planning session boundaries.** The planning session stays a planning session and spawns research or implementation sessions. What does a good dispatch look like when Qwen is weak at long tasks?
- **Lost work across many Copilot windows.** The stop-early routine, `resume: true`, and the Open Sessions table are the first answer. Is it enough? What else makes work visible across laptops?
- **Qwen stalls and loops.** Stall ladder exists (`PLANNING_AGENT_SESSION_START.md`); nudges worked partly today. Does Qwen need smaller prompts, or a different role?
- **Who does maintenance** (`status.md` curation, active-task checks) and how often.

## Environment notes (REPORTED unless stated)

- Three laptops (2014 Intel, 2018 Intel, M4). Ollama runs on the Ryzen 7 and the M4 only. Files that exist only in a chat are invisible to other machines: commit anything another session needs.
- Copilot budget about 25% per week; Haiku is the go-to model, which changes often (`notes/AGENT_NOTES.md`).
- Qwen has run risky git commands in past closeouts (`stash`, `pull` on a dirty tree, `reset HEAD`). Those are forbidden (`SESSION_CLOSEOUT.md` section 0).
