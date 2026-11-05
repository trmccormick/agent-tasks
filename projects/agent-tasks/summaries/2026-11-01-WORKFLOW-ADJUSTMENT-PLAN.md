# Workflow adjustment plan, 2026-11-01

**Author**: Claude (free web), review role, with Tracy. **Written**: 2026-11-01 about 10:00, with origin at `2bf315e` (VERIFIED, fetched). Labels: VERIFIED (a read of origin or a command), REPORTED (Tracy or an agent said it), INFERRED (my conclusion).
**Purpose**: a durable copy of the agreed direction and the exact text of each edit, so any session can continue if this one ends. Nothing below was applied when this was written. To tell whether an edit is applied, search origin for its marker phrase (given per edit); do not trust this file's status.

## How to apply edits safely (other sessions are running)

1. `git pull --ff-only && git status -sb` first. Leave other sessions' staged or untracked files alone.
2. Each edit names the file's blob on origin at `2bf315e`. If the blob has changed, re-read the file before using the text.
3. One file per commit, path-limited (`git commit -m "..." -- <path>`); push right after. `NEEDS_REVIEW.md` is edited by several sessions, so do not leave edits to it unpushed.
4. Each edit stands alone; none refers to text another edit adds, so stopping after any one leaves the docs consistent.
5. After each push, read the text back from origin, and run `git --no-pager diff --stat` before each commit.

## Direction agreed (REPORTED, Tracy, 2026-11-01)

- Task files exist to save effort. If a task takes longer to build than the change takes to do, do the change directly (the direct-edit rule is on origin, `fefc980`).
- A task carries only what differs from standing guidance and points to the rest. For Galaxy Game, intent lives in the wiki; project rules live in `SESSION_GUIDANCE.md`.
- Task approval: a web agent drafts and Qwen verifies (one pair); a reviewer who took no part reviews once and returns one complete list; one round of edits; Tracy reviews; then `backlog/`; then dispatch. No repeated review rounds.
- The workflow adjusts as we see what happens; record what we change and why.

## Evidence behind it (VERIFIED unless marked)

- `REVIEW_AGENT_GUIDE.md` L71 says a failed template check means "do not proceed to content assessment", and L79 says REVISE is "before re-review". Findings therefore arrive in waves.
- The sign-in task passed ChatGPT, Grok and Qwen reviews and Tracy's approval with unfilled placeholders in its dispatch block and a missing synthesis template (`NEEDS_REVIEW.md` L451-478). A grep for the placeholder names matches 9 lines at the approval commit `f7ea1b5` and 1 line (the "One allowed substitution" sentence) at origin head.
- Today's host-command incident: the continuation prompt pointed Qwen at `GUARDRAILS.md` and `projects/galaxy_game/README.md`, not `projects/galaxy-game-ui/SESSION_GUIDANCE.md`, which names the container `galaxy_game` (REPORTED, session history from Tracy).

## Edits, in order. Each is independent.

### E1b. Fix a misplaced addendum (do first, one line)
File: `projects/agent-tasks/NEEDS_REVIEW.md` (blob `08fbde8e`). My 2026-10-31 addendum (L534) now sits below the 2026-11-01 entry, so it reads as an update to that entry. It belongs to the 2026-10-31 "Task-drafting workflow overhead" entry (L479).
Change the start of L534 from
`**Update 2026-10-31 (evening, Claude web review)**: Outcome of the sign-in change.`
to
`**Update 2026-10-31 (evening, Claude web review), to the "Task-drafting workflow overhead" entry above**: Outcome of the sign-in change.`
Expected diff: 1 file, 1 insertion, 1 deletion. Marker: `to the "Task-drafting workflow overhead" entry above`.

### E1. Addendum to the 2026-11-01 host-command entry
File: `projects/agent-tasks/NEEDS_REVIEW.md`. Insert after the entry's `**Status**: OPEN. For Claude's independent review...` line (L530) and before the `---` that follows it, with a blank line before and after. Marker: `Evidence from the session history Tracy supplied`.
```
**Update 2026-11-01 (Claude web review)**: Evidence from the session history Tracy supplied (VERIFIED: pasted history, not seen live). The continuation prompt told Qwen to read `GUARDRAILS.md` and `projects/galaxy_game/README.md`; it did not point to `projects/galaxy-game-ui/SESSION_GUIDANCE.md`, which says the UI container is `galaxy_game` and that `web` is the main game server. Qwen then ran on the host: `cd <repo>/galaxy-game-ui && bundle exec tailwindcss -i app/assets/stylesheets/application.css -o public/assets/application-tailwind-compiled.css`. Tracy reports it failed with missing-gem errors (REPORTED), so nothing on the host was changed (INFERRED; it was a build, not an install). Tracy stopped it with a message saying the project runs in Docker, the repo is mounted at `/home/galaxy_game`, and to find the container and build command from the compose configuration. Qwen then ran `docker-compose ps` and used `docker exec -it galaxy_game ...` correctly for the rest of the session. The correction worked once the container was discoverable; the gap is that nothing at the start pointed Qwen to it. Finding: Rule 1 names Rails and RSpec only, and its template uses `docker exec -it web`, the main game's container; the UI project's container is `galaxy_game` (VERIFIED: `GUARDRAILS.md` L29-37, UI compose files, UI `SESSION_GUIDANCE.md` L56). Also noted: the session ran the standalone Tailwind CLI against `app/assets/stylesheets/application.css` and added `@import "tailwindcss"` and `@source` there, while at `f07953b` the Tailwind entry file is `app/assets/tailwind/application.css` and the build writes `app/assets/builds/tailwind.css` (VERIFIED: origin); whether the session's edits are in the right file is not established. Recommended: one line in Rule 1 for projects whose container is not `web`; the UI session guidance names `galaxy_game`, and prompts for UI work point to that file. Priority LOW to MEDIUM. Status: OPEN until applied.
```
Expected diff: 1 file, 3 insertions (the paragraph and two blank lines).

### E2. UI session guidance: run commands in the container
File: `projects/galaxy-game-ui/SESSION_GUIDANCE.md` (blob `a94095a4`). Insert directly under `## 5. Critical rules` (after its blank line). Marker: `Run commands in the container, never on the host`.
```
- **Run commands in the container, never on the host.** `bundle`, `rails`, `rake`, `rspec`, `tailwindcss`, `node` and `yarn` all run with `docker exec -it galaxy_game bash -c '...'`, for example the CSS build: `docker exec -it galaxy_game bash -c 'rails tailwindcss:build'`. GUARDRAILS Rule 1 shows `web`; that is the main game's container, not this app's.
- **`projects/galaxy_game/` is the main game's folder, and the container `galaxy_game` is this app's.** The names collide. Commands and containers in the main game's docs do not apply here; this file wins for running this app.
```
Expected diff: 1 file, 2 insertions.

### E3. GUARDRAILS Rule 1 (needs Tracy's approval; shared file)
File: `rules/GUARDRAILS.md` (blob `88299afc`). Insert after the line `Never run Rails or RSpec on the host system directly.` (L37). Marker: `In a project whose container is not named`.
```
In a project whose container is not named `web`, use the container named in that project's `SESSION_GUIDANCE.md` (Galaxy Game UI prototype: `galaxy_game`). `bundle`, `rails`, `rake`, `rspec`, `tailwindcss`, `node` and `yarn` all count; never run them on the host.
```
Expected diff: 1 file, 1 insertion.

### E4. Prompt header for implementation agents (blocked on one question)
File: `WEB_AGENT_SESSION_START.md` (blob `a297427d`). Insert directly after the delivery table, before `### When a task file is not needed`, with a blank line on each side. Marker: `A prompt for an implementation agent** opens by naming`. Blocked: whether ChatGPT is given this file (unanswered).
```
**A prompt for an implementation agent** opens by naming the files to read: `rules/GUARDRAILS.md` and `projects/<project>/SESSION_GUIDANCE.md` for the project being worked on, and says which container runs the commands. Where those files disagree about a container name or command, the project file wins.
```
Expected diff: 1 file, 3 insertions. This also answers the open "basic prompt header" question from the 2026-10-24 incident review.

## Process edits, to draft (not started; exact text to be written and approved first)

- **P1. `REVIEW_AGENT_GUIDE.md`** (blob `f1efb2a7`): run the template and content checks in one pass and return one complete list (change L23 and L71); each PASS quotes its evidence (the line read, or command output); after REVISE the reviewer re-checks only the listed items (L79); a second failed re-check stops the task and Tracy decides whether to use the direct lane. Placeholder check as a command: `grep -nE '\[(project_name|project|SUBFOLDER|FILENAME|FILL IN)\]' <task file>`; expected result: one line, the "One allowed substitution" sentence.
- **P2. `PLANNING_AGENT_SESSION_START.md`** (blob `5c08b2fb`), Task Creation Workflow (L35; not read this session): the pairing step. A web agent drafts with `[FILL IN]`; Qwen fills the facts and runs the mechanical checks, pasting raw output; then the reviewer.
- **P3. Slim tasks.** Repeat-once rule: a fact an agent is told twice goes into the project's `SESSION_GUIDANCE.md`, and tasks cite it. A task states its intent source (a wiki page, or "no page exists", which becomes wiki work, not task back-and-forth). Where to write this is undecided (candidates: `PLANNING_AGENT_SESSION_START.md`, `TASK_TEMPLATE.md` intro).
- **P4. `TASK_TEMPLATE.md`** (blob `948c27c7`): L130 omits `[project_name]` from the fill list and does not say the placeholders are filled at drafting. Fold into the queued fix for the 2026-10-18 HIGH entry (four contradictions). Open decision: keep the dispatch block in every task, or move it to one canonical file filled in at dispatch (tasks would then carry no placeholders). Try a slim task first.

## Open decisions for Tracy

1. Approve E3 (the `GUARDRAILS.md` Rule 1 line)?
2. Is ChatGPT given `WEB_AGENT_SESSION_START.md`? (E4 depends on it.)
3. Sign-in task in `projects/galaxy-game-ui/tasks/backlog/`: rewritten again in `64bc6e1` (08:04) after GO; move to `completed/` as superseded, or keep?
4. `64bc6e1` also removed 59 lines from `projects/galaxy_game/status.md` (the 2026-10-26 and 10-25 session logs) and set "Last Updated" back to 2026-10-26. Intended? (Git history has the text.)
5. `64bc6e1` adds a "dispatch sequencing conflict" warning to the sign-in task (the dispatch block forbids reading the task before moving it; the readiness checklist needs the task read first). Not judged yet; if right, it is a template contradiction for P4.
6. Is Qwen applying a single bounded edit with no saved task an approved path? (Open since 2026-10-24.)

## Not touched

The dashboard files (uncommitted, not on origin), `galaxy-game-ui` code, and the 10-31 entries other than E1b (assessment given in chat: resolve the template-completion and dispatch-path entries after P1 and P4; keep the overhead and Qwen-persistence entries open).
