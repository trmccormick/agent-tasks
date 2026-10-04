# Handoff: Claude (web) reviewer, eve-dashboard, 2026-10-03

## Your role
You are the read-only, free-tier reviewer for Tracy. Tracy relays between you and two local Qwen sessions in VS Code (a planner and an implementer). You run nothing: you read pasted output and public repo files, and give the next step. Premium Copilot (Haiku) is a last-resort fallback only.

## How to work with Tracy (learned the hard way)
- ONE command or edit per message. Wait for the pasted output before giving the next. Don't revise earlier instructions afterward unless the output shows an error.
- Give exact copy-paste terminal commands. For file edits use a python3 script that backs up first, matches plain-string anchors, and exits without writing if an anchor isn't found exactly once.
- Terminal pastes wrap long lines, so words like "noevidence" are paste artifacts, not file defects.
- Qwen stalls after announcing a command or says "I already have this from earlier reads". Fix: one job per message, raw output only, fresh session after a repeat. Label anything meant for Qwen "file content: save it, do not execute". The planner edits only files in agent-tasks, never app code.
- Agents never commit, push, or rebuild. Tracy approves. Commit named files only, then run git pull --rebase origin main and git push (other laptops push to agent-tasks).
- Reading the repos: github.com/trmccormick/agent-tasks is public. Folder (/tree/) pages are blocked, file pages open if the link appeared on a fetched page, and raw.githubusercontent.com URLs work when Tracy pastes them. The app repo eve-dashboard is private, so ask for pasted output.

## Environment facts
- Paths: /Users/tracymccormick/Documents/git/agent-tasks and /eve-dashboard (branch local/improvements).
- Container: eve-dashboard. Database: /app/data/dashboard.db. There is no sqlite3 CLI; use a python3 -c one-liner with the sqlite3 module.
- Only config/ and data/ are bind-mounted, so app/ code changes need a rebuild: VS Code right-click Compose Up, or docker compose down then up -d --build. Never use down -v. Run PRAGMA integrity_check before and after a rebuild.
- Sync logging regressed after the upstream merge: sync.py has 3 logger calls versus 17 traceback.print_exc, so failures go to docker logs (reset on rebuild) and dashboard.log has no sync lines since 2026-10-01 18:57.

## Market bug: state (task file PARTIAL, report PARTIAL, all pushed)
- /market loads live data: 17 sell orders (Neon Blue Mernher 11, Neon Red 6), 0 buys. Totals history 24 -> 18 -> 17 -> 17. Integrity ok, container healthy, /market/stats matches the DB.
- Root cause UNCONFIRMED. Database corruption cleared by the Oct 1 restart is the leading candidate, with no evidence.
- Ruled out by code review: the wipe in save_market_orders (esi._get raises on HTTP errors, sync.py catches them, so DELETE only runs after a successful ESI response); a slow server or client render (API 7 ms, 24-row render under 1 ms); the sort in _market_orders_view (null-safe).
- Unexplained 500 on /market/orders on 2026-10-02 (stats still worked; traceback lost). Current hypothesis: JSONResponse failing to encode a bad value in one order row. CORRECTION PENDING: the task file's Next steps item 2 and status.md still say the sort on None values; replace that with the hypothesis above.
- Slow controls are unexplained. Note that the Refresh market button runs sync.sync_all in-request.
- Tal Beyond has no market rows and no verified recent sync. The margin always shows "-" because there is no buy history; wallet_transactions and wallet_journal tables exist and may supply it.
- Step 7 ESI snippet in the task file (uses crypto.decrypt, esi.get_access_token, and sync._persist_rotated_token) has never been run.

## Uncommitted work in eve-dashboard (needs a fresh implementer, one step per message)
- app/logging_config.py: the first-draft print_exc shim is deployed in the container. Replace it with the fixed version (guard with getattr(traceback.print_exc, "_logs_to_file", False), call logging.getLogger("app.unhandled").exception only when sys.exc_info()[0] is not None, and confirm that logger reaches the file handler).
- app/sync.py: a partial edit is deployed, with logger.exception in the market block plus a dead `fetched = ... market._get_market_orders ...` line that must be removed (that function doesn't exist). The intended market block logs "Market sync for character %s: %d orders saved", warns on esi.RateLimited, and uses logger.exception on other errors. First confirm that save_market_orders returns a count on the non-empty path. Per-character "Successfully synced" and "Failed to sync" lines at the _one entry point are still to be added (that region was never printed).
- print_exc also appears in alerts.py, agenda.py, chain/kills.py, chain/api.py, chain/tracker.py, wallet_archive.py, and main.py; the shim covers them.
- Review each diff before Tracy rebuilds.

## Tracy's own checks (open)
- In-game order counts: Neon Blue Mernher 11, Neon Red 6, Tal Beyond.
- Buy/Sell tab test: with 0 buys, Buy should show an empty table and Sell all 17 rows.
- Tal Beyond's dashboard card (fresh data?).
- A tripwire loop is meant to capture the traceback if the 500 returns.
- invalid_scope: esi-corporations.read_structures.v1 is not enabled on the EVE developer app. Enable it, or remove it from SCOPES in app/config.py. It blocks adding accounts.

## Pending planner jobs (one per message, planner edits agent-tasks only)
Backlog task files for: logging fix, the 500, Tal Beyond sync, invalid_scope, expired-status dead code (status always "active"), ISK-at-risk label, margin data source, slow controls. Also the sort-hypothesis correction above.

## Upstream author
EVE character name Luciela (he/him), moves fast, runs a fly.io deployment. Before merging, fetch and diff. Review sso.py, crypto.py, config.py, and sync.py diffs for new network calls or scopes. Keep Tracy's characters on the local instance.

## Workflow review (waiting on one answer)
I read README.md, TASK_TEMPLATE.md, and SYSTEM_INTENT.md. Findings: the root grew from 6 intended files to about 22; handoffs/ was abandoned, so status.md absorbed history and reached 460 lines (now purged to 67, history in status-archive.md); contradictions remain (synthesis in chat versus file, who commits, status.md "historical log" versus Tracy's intent of recent and pending work only); hardcoded /Users/tam0013 paths and Galaxy-specific content in the "generic" files. Draft intent: short entry files (about 100 lines), task files hold detail, handoffs hold history, evidence over assertion (criteria unchecked until evidenced, PARTIAL allowed), humans own commits and rebuilds, one job per message for local models, verified commands per project. Open question for Tracy: apply this to eve_dashboard only, or to the shared root for all projects? Another laptop reworked the template recently (see the 2026-10-02 compliance review under wvulibraries_databases/summaries), so re-read TASK_TEMPLATE.md first. Still unread: PLANNING_AGENT_SESSION_START.md, TEMPLATE-SESSION-HANDOFF.md, REVIEW_AGENT_WORKFLOW.md, ROUTING_LOGIC.md, rules/GUARDRAILS.md (ask Tracy for the URL).

## First move
Ask Tracy for: git status -sb in both repos, the in-game counts, and the tripwire result. Then continue with the logging-fix implementer prompt.