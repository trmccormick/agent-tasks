# Closeout handoff: workflow decisions, 2026-11-06 evening

**Agent**: Claude (free web), review role  **Project**: agent-tasks  **Type**: REVIEW (workflow design)
**Written**: 2026-11-06 about 20:10, with origin at `a39ab04` (VERIFIED: my read-only clone matched it). Labels: VERIFIED (a command or a read of origin), REPORTED (Tracy said it), INFERRED (my conclusion), DEFAULT (my proposal that Tracy has not confirmed).
This continues `2026-11-06-REVIEW-CLOSEOUT.md` (on origin since `a39ab04`). Origin has since moved to `639eaaf` (a Galaxy Game `NEEDS_REVIEW.md` note, 8 lines; VERIFIED). Tracy uses he/him.

## How to start the next session

1. Read this file, then `2026-11-06-REVIEW-CLOSEOUT.md`.
2. Ask Tracy to run `git pull --ff-only && git status -sb`. Other sessions push often.
3. Read the repo yourself: call `mcp__claude-code-remote__add_repo` for `trmccormick/agent-tasks` with read access; it is a public repo and the session proxy serves it, then follow the clone instructions in the result. Do not tell Tracy you cannot read the repo before trying that. (The environment note in `2026-11-06-REVIEW-CLOSEOUT.md` saying this session could not read the repo was wrong: I had not tried the tool.)
4. Draft exact text; Tracy applies, commits and pushes (or directs an agent to); read it back from origin.

## Decisions from Tracy (all REPORTED unless marked)

**Lanes and scope**
- Task files exist to carry details and work we cannot do now. They are not a gate on every change.
- Small work we can do now goes straight to Qwen: he reads GUARDRAILS, the project README and the project `SESSION_GUIDANCE.md` at session open, then we work with him directly.
- A direct session does one piece of work. If other work is noticed, a planning session later turns it into a task file. (Where the "noticed work" note lives is not decided; DEFAULT: an entry in the project's `NEEDS_REVIEW.md`.)

**Review flow**
- The drafter never reviews its own draft.
- Stage 1 (format only): an agent with no git access (Office 365 Copilot, Perplexity, Gemini) gets the task and `TASK_TEMPLATE.md`. It checks that the sections are present. It cannot verify the codebase and does not try.
- The reviewer fixes the draft and returns the file instead of bouncing the issues back to Qwen, who is slower. Tracy diffs and commits it.
- Stage 2 (content): an agent with git access (Grok in the example) verifies the task against the codebase and, for Galaxy Game, against the wiki. After the first pass on the information, the task goes to a third, final reviewer, then to Tracy.
- If a reviewer is unsure or something is off, it may stop and ask. Tracy would rather explain the intent than have the agent guess.
- Office 365 Copilot is for Samvera and WVU work only (`samvera_hyku`, `wvulibraries_knapsack`, `wvulibraries_databases`). He can review if given the package (task plus template).
- A test on 2026-11-05 or 11-06 where Claude and Grok corrected the file themselves, instead of reporting findings, went much more smoothly (REPORTED; no written record seen).

**Qwen and pairing**
- Pairing Qwen with a no-git web model (Perplexity or similar) works well and sessions last longer. Any agent can pair with Qwen.
- Edit-and-return is faster than asking Qwen to edit; Qwen is a slower local model and does not always save time in the loop.
- DEFAULT (Tracy: "flexible, still deciding"): Qwen is the eyes and hands on the repo and project machine for agents without git access and reports raw output; the web agent edits documents. Qwen also keeps implementation and the direct lane.

**Intent and the wiki**
- Galaxy Game: `docs/wiki` in the GalaxyGame repo (https://github.com/trmccormick/galaxyGame) is the one home for design intent. All other docs there are frozen and being consolidated into it. `wiki_reorganization` (repo root) is an earlier attempt; ignore it.
- Tasks never point at the old docs. The wiki is updated with the needed data while the task is being built, and the task points to the wiki.
- When an agent is unsure of intent: it first pulls up what exists and quotes it with file and commit; if something is off, Tracy and the agent discuss and update the wiki (or other doc) in place. Reason: Tracy may not remember his earlier phrasing, and a second statement can conflict in a detail. A new statement is drafted only when nothing exists. A conflict is shown to Tracy, not resolved by the agent (DEFAULT wording).
- Wikis: wanted for repos Tracy controls (WVU Knapsack and other WVU projects, personal projects). Samvera: find their documentation and suggest edits by PR; their workflow differs and a documentation working group handles it (REPORTED, not checked). Where intent lives for Samvera work until a PR lands is not decided.

**Commits**
- Tracy decides; an agent commits, pushes or moves files when Tracy tells it to and names the files (ChatGPT with GitHub integration editing `NEEDS_REVIEW.md` on request is the example). Proposed wording: nothing is committed unless Tracy directs it; the result is read back from origin.
- Not answered: does this cover moving a task from `drafts/` to `backlog/`? `PLANNING_AGENT_SESSION_START.md` Rule 7 says planners may not, calling it human-only.

**Fixed core vs flexible (my proposal; Tracy agreed the workflow should stay flexible and has not confirmed this split)**
- Fixed: Tracy approves; one objective per session; the same three startup files in every prompt; a reviewer works on an exact commit and names it; design intent has one home; a round cap (DEFAULT: three, then it comes to Tracy).
- Flexible: who drafts and reviews; whether Qwen is paired; one review stage or two; who edits a file.
- DEFAULT: optional improvements a reviewer notices are listed, not applied.

**Pronouns**
- Tracy uses he/him. Older handoffs say "she" (see pending edits).

## What I read this evening (VERIFIED, my clone at `a39ab04`)

- `DIRECT_SESSION_TEMPLATE.md` (72 lines): a good lean base. Missing: where noticed work is recorded, the intent source, and its table row points to `SIMPLE_HANDOFF_TEMPLATE.md` (an earlier entry says that file is likely retired).
- `PLANNING_AGENT_SESSION_START.md` L1 to L112 and L146 to L247: one review stage; REVISE loops back to the planner (L94); the planner self-checks the template (L60); it calls itself a "local planning or review agent". The "Preferred Pairing Patterns" (L215 to L241) sit under the "DEPRECATED: Detailed Agent Table" heading (L194 to L245). The live quick guidance recommends Office 365 Copilot for quick review with no project restriction (L186). It sends capability questions to `MODEL_SELECTION_GUIDE.md`, which I have not read.
- The three-file startup (GUARDRAILS, project README, `SESSION_GUIDANCE.md`) is in `DIRECT_SESSION_TEMPLATE.md` and the `TASK_TEMPLATE.md` dispatch block. The "What to read at session start" list in `WEB_AGENT_SESSION_START.md` (L39 to L45) has neither GUARDRAILS nor the README. The review dispatch fragment in `REVIEW_AGENT_GUIDE.md` names no startup files.
- The review-package rule (`ddadc92`) is in `REVIEW_AGENT_GUIDE.md` only; no startup or template file carries it.
- `REVIEW_AGENT_GUIDE.md` line 7 says *GitHub* Copilot is not used for review; line 101 and the planning file list *Office 365* Copilot. Different products, so probably not a conflict (INFERRED; confirm which Copilot reviewed on 2026-11-05).
- Inventory: 40 markdown files, 9,373 lines (`ls -1 *.md rules/*.md projects/*/SESSION_GUIDANCE.md | xargs wc -l`). Groups: startup and role guides, three closeout docs, four handoff templates, six rules and routing files, and about 3,500 lines of governance history and proposals.
- Earlier records: the 2026-10-27 closeout (L31) records the pairing flow with Perplexity as mid-level drafter; the 2026-10-20 closeout (L28) records that agents may commit and push when Tracy tells them to.
- GitHub folder pages (`.../tree/main/docs/wiki`) refused automated fetch (robots); single file pages worked. I did not try `add_repo` for the GalaxyGame repo, so whether I can read `docs/wiki` is open.

## Pending edits (item 1 is applied; items 2 and 3 are not, as far as I know)

1. Pronoun fix in `projects/agent-tasks/handoffs/claude-free-web/2026-11-06-REVIEW-CLOSEOUT.md` lines 27, 28 and 62 ("Basis she gave", "She applied", "she works"): applied, and committed together with this file. Still pending: the same fix in `2026-11-05-REVIEW-CLOSEOUT.md` line 65.
2. One line under the opening note of `rules/GUARDRAILS.md` (after line 6), "Tracy uses he/him", as its own commit.
3. At least 24 lines in 12 files under `projects/agent-tasks` say "she" or "her" (the search was capped at 40 results; nothing outside that folder was searched). My recommendation: leave the older history alone and fix only the last two days' files.

## Open items, in order

1. **Pilot before writing rules.** Run one of the three Galaxy Game drafts (`projects/galaxy_game/tasks/drafts/2026-11-05-MEDIUM-ARCHITECTURE-*`) through the flow: stage 1 Perplexity or Gemini paired with Qwen; stage 2 Grok against `docs/wiki`; final reviewer; Tracy. Record every handoff and stall, then write rules from that. Not answered; Tracy to say yes and pick a draft.
2. **One file of role prompts** (drafter, format reviewer, content reviewer, direct session), each carrying the three-file startup and the package list, so handoffs are copied and not invented. Proposed; not answered.
3. **Replace, not add**: `ddadc92` merged template and content into one pass and treats REVISE as a return to the drafter. A two-stage, reviewer-fixes section would replace that text. Not drafted. Also `PLANNING_AGENT_SESSION_START.md` L54 to L95 and L215 to L241 (pairing patterns under the deprecated heading), `WEB_AGENT_SESSION_START.md` (reading list; "only the human commits and pushes" at L25 and L120), planning Rule 7 (drafts to backlog), `DIRECT_SESSION_TEMPLATE.md` additions, guide line 7 wording, and `MODEL_SELECTION_GUIDE.md` (read first).
4. Carry forward items 3 to 8 and the open decisions from `2026-11-06-REVIEW-CLOSEOUT.md`: slim tasks, consolidating the 11-05 planning entries, Rule 26 and 13b, entry statuses, Galaxy Game UI guidance, and the unread commits and entries.

## What to verify, and why (process notes)

- Pasted terminal output drops or adds spaces at wrapped lines; grep the file for the spaced form before calling it a defect.
- Stage only after a check that must succeed (strings found, file unmoved on origin, expected counts); read each push back from origin (`git ls-remote origin main` plus a grep count).
- Edits: one file at a time, as an asserting script or exact text, with Tracy pasting `git diff` before staging. Path-limited commits; commit and push as separate steps.
- Addenda go directly after their own entry.

## Environment notes

- This session's clone: `/home/claude/trmccormick/agent-tasks` (shallow, read-only, session-local). Tracy's clone is `/Users/tracymccormick/Documents/git/agent-tasks`; he works from more than one machine, so pull before any local check.
- This file belongs at `projects/agent-tasks/handoffs/claude-free-web/2026-11-06-WORKFLOW-DECISIONS-CLOSEOUT.md`; Tracy commits it.
