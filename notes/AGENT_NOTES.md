# Agent Notes

Model- and tool-specific facts live here and nowhere else. The universal docs (session starts, `SESSION_CLOSEOUT.md`, `rules/GUARDRAILS.md`) do not name models.

- Everything here is guidance, not a rule (MAG-5). The human chooses the agent at dispatch; there is no fixed routing ladder (MAG-3).
- Evidence is **REPORTED** (stated by the human or taken from an older doc) unless an entry says otherwise.
- Entries are dated and append-only. When a fact changes, add a new entry that says which one it supersedes. The planning agent archives old entries with a note; it does not silently delete them.

---

## Current stack (as of 2026-10-09)

| Agent | Where and how | Notes |
|---|---|---|
| Local Qwen | Copilot's local-model feature, served by Ollama on the Ryzen 7 and the M4 MacBook | qwen3.6 in use (27B and 35B sizes exist for qwen3.6 only); qwen3.8 in testing. The 2014 and 2018 laptops run no models; they connect to those two machines. |
| Claude (web) | Chat session; can read the public repos when attached | Planning and review. See the 2026-10-09 entry on where work goes. |
| ChatGPT (web) | Chat session | Galaxy image-asset prompt and image-bible work with Qwen; comments on task design. Image-generation capacity is limited. |
| Grok (web only) | Chat session; Copilot access was removed | Preferred for Galaxy AI Manager design work, but usable like any web agent. |
| Gemini | Not re-confirmed | Earlier: document analysis, design passes, adversarial task review. |
| Perplexity | Not re-confirmed | Earlier: research handoffs. |
| Continue | Obsolete | Replaced by Copilot's local-model feature. |

---

## 2026-10-09 — Local Qwen stalls and how to recover

Seen again on the Galaxy C2 session. Qwen narrates what it is about to do without making a tool call, or repeats the same search or check. It works best with one step per prompt. Recovery, in order:
1. Send a short redirect that names the single next action, says not to repeat earlier checks, and says what to report and where to stop. A redirect drafted by ChatGPT worked on the C2 session.
2. If it keeps stalling, write a short state note and start a fresh session. A fresh session has cleared stalls before.

## 2026-10-10 — Local Qwen closeout reached into another session's files

Given the earlier `SESSION_CLOSEOUT.md`, a Qwen closeout on the Galaxy C2 session started to archive another session's untracked drafts (the paused transit-engine work) as "stale". The human stopped it before it acted. The rest of that closeout was scoped correctly: the status entry went in the right project file, it staged explicit paths, and it listed the other files as preserved. The revised `SESSION_CLOSEOUT.md` (section 0) limits closeout to the session's own task work, and maintenance of everything else belongs to the planning agent with the human. When dispatching a closeout to Qwen, name the files the session changed, or tell it to list candidates and wait. Sessions are often paused by usage limits and resumed later, so a task in `active/` or an untracked file with no closeout entry is not abandoned.

## 2026-10-10 — Local Qwen closeout, second run: scoped files, risky git commands

After the section 0 rewrite, a Qwen closeout (Galaxy C2) kept to its own files and staged explicit paths (`f0317b2`: `status.md` and the C2 task file). Reported in its log (REPORTED, not re-run): it ran a plain `git pull origin main` on a dirty tree, then `git stash` twice and `git stash pop` once. The first stash, which held two modified transit summary files, was probably not restored. Check `git stash list` on that machine. It also moved C2 into `completed/` while two other copies existed (`active/`, `backlog/asset-ui/`) and did not report them, and its status entry said "closed out" while a review was pending. `SESSION_CLOSEOUT.md` section 0 now forbids stash, reset, checkout, restore, clean and plain pull during closeout, and requires stopping on duplicate copies. Nothing was lost as far as is known.

## 2026-10-10 — Local Qwen on Galaxy C3: looped, then closed out with gaps

Seen in the C3 session log (REPORTED, checked against origin where noted). Qwen read the same spec file range about ten times and said it would continue without acting, until the human told it it was repeating itself; it then recovered. It committed galaxyGame `b816d8e` and agent-tasks `359e490` (VERIFIED on origin) and staged only its own files. Gaps: it ran `git reset HEAD` on paths another session had staged (including that session's task move), which changes that session's index; it never wrote its C3 `status.md` entry and did not say so; it ran RSpec through `| tail -80` where `projects/galaxy_game/SESSION_GUIDANCE.md` requires a log-file redirect; it triple-added `completed_date` to the task's front matter; it marked "catalog render displayed" as met on stubbed tests and did not run the check its own plan listed ("verify with actual RH-400 data"). Two sessions were using the same agent-tasks working tree at once, which is how another session's staged changes ended up in the closeout. `SESSION_CLOSEOUT.md` section 0 now covers staged changes by other sessions and 1b item 8 covers a missing status entry.

## 2026-10-09 — Where work goes

Direct file edits by Claude (web) are often more efficient than dispatching Qwen for single-file text edits. Use Qwen when the job needs a live repo check, Docker or a test run. Claude can read the public repos (`agent-tasks`, `galaxyGame`) directly when they are attached to the session.

## 2026-10-09 — Grok

Galaxy Game AI Manager design work has usually gone to Grok. That is a development preference, not an assignment; Grok can be used for other things like any other web agent. The AI Manager design constraints themselves are in `projects/galaxy_game/SESSION_GUIDANCE.md`, section 8, so any agent doing that work gets them.

## 2026-10-09 — Older model and routing docs (historical)

These predate this file, are dated May–June 2026 and describe the Qwen3.5 era. They have not been reconciled with this file. Where they disagree, this file is more recent.
- `ROUTING_LOGIC.md` (2026-06-17) and `rules/AGENT_ROUTING.md`
- `docs/MODEL_SYSTEM_SELECTION_GUIDE.md` (2026-06-06)
- `docs/COPILOT_BUDGET_MANAGEMENT.md` (2026-05-31)
- `projects/galaxy_game/AGENT_ASSIGNMENT_GUIDE.md` (2026-06-01)
- `docs/ollama_copilot_agent_template.md` (undated)

## 2026-07-08 (carried over) — Ollama configuration and Copilot tool-use failures

From the former Galaxy context file (archived verbatim at `projects/galaxy_game/archive/GALAXY_GAME_CONTEXT-2026-07-08.md`, sections "Multi-Agent Hardware Setup" and "Known Tool Use Failure Modes"). Not re-verified.
- Reported configuration rule: base tags plus `/set nothink` in Modelfiles; never set `num_ctx` explicitly.
- The same file's failure table tells the reader to restore the context length in Ollama's settings after an update reset it. The two statements are in tension. Check which is current before relying on either.
- The failure table covers tool calls printed as text, "Response too long", "Response contained no choices", `ERR_EMPTY_RESPONSE`, version-mismatch warnings and agents looping on task-file edits. Read the archived file for the symptoms and fixes.

## 2026-07-08 (carried over) — Image generation

From the former `CLAUDE_SESSION_START.md` (removed in `a74b5f3`; see git history). Not re-verified.
- ChatGPT image-generation session time is limited. When asset work comes up, check early what can be generated that day and plan around it.
- Asset creative direction is driven by the human with ChatGPT: prompt refinement, style, and the ChatGPT vs Gemini comparison. Claude's part is to record design decisions, flag real data or architecture inconsistencies, and cross-check claims against the files when asked. It does not second-guess creative direction.
