Handoff: Claude (web) reviewer, eve-dashboard, 2026-10-04
Your role
You are the read-only, free-tier reviewer for Tracy. Tracy relays between you and two local Qwen sessions in VS Code (a planner and an implementer). You run nothing: you read pasted output and public repo files, and give the next step. Premium Copilot (Haiku) is a last-resort fallback only.
How to work with Tracy

ONE command or edit per message. Wait for the pasted output before giving the next.
Give exact copy-paste terminal commands. For file edits use a python3 script that backs up first, matches plain-string anchors, and exits without writing if an anchor isn’t found exactly once.
Agents never commit, push, or rebuild. Tracy approves.
Planner edits only files in agent-tasks; implementer edits app code.

Environment facts (unchanged)

Paths: /Users/tracymccormick/Documents/git/agent-tasks and /eve-dashboard (branch local/improvements).
Container: eve-dashboard. Database: /app/data/dashboard.db. No sqlite3 CLI; use python3 -c + sqlite3 module.
Only config/ and data/ are bind-mounted → app code changes need rebuild (docker compose down && docker compose up -d --build; never -v).
Run PRAGMA integrity_check before/after rebuilds when relevant.

Completed this session
Logging regression fixed and verified live:

app/logging_config.py: first-draft print_exc shim replaced with guarded version (re-entrancy flag via getattr(..., "_logs_to_file", False), only logs when sys.exc_info()[0] is not None, uses logging.getLogger("app.unhandled").exception).
app/sync.py:
Market block now captures the count returned by market.sync_character_market_orders, logs "Market sync for character %d: %d orders saved", warns on esi.RateLimited, uses logger.exception on other errors. Dead _get_market_orders line removed.
_one() (inside _sync_all) now logs "Successfully synced character %s" on success and "Failed to sync character %s: %s" on failure.

Both files committed on local/improvements as b61b50a (“fix: restore structured sync logging and guarded print_exc shim”).
Live verification: multiple post-rebuild syncs produced the exact new log lines in data/logs/dashboard.log. Tal Beyond consistently reports 0 orders; Neon Blue Mernher 10, Neon Red 6 (total 16 active sells).

Backup files from the edit scripts were present; Tracy was instructed to rm them.
Current git state (eve-dashboard)
text## local/improvements...origin/local/improvements [ahead 1]
(Working tree expected clean after backup removal. Push of the one commit is Tracy’s decision.)
Still open (from previous handoff + this session)

Market bug root cause still UNCONFIRMED (leading candidate remains the Oct 1 DB corruption that was cleared by restart; no evidence).
CORRECTION STILL PENDING: task file’s Next steps item 2 and status.md still mention the old “sort on None values” hypothesis; replace with the current hypothesis (JSONResponse failing to encode a bad value in one order row). The unexplained 500 on /market/orders on 2026-10-02 is the related symptom.
Tal Beyond: 0 market rows, no verified recent meaningful sync beyond the empty result. Margin always “–” (no buy history). wallet_transactions / wallet_journal tables exist and may be usable.
invalid_scope: esi-corporations.read_structures.v1 is not enabled on the EVE developer app → blocks adding accounts. Either enable it or remove it from SCOPES in app/config.py.
Slow controls still unexplained (Refresh market runs sync.sync_all in-request; the endpoint itself is fast when tokens are valid).
Other backlog items still need task files: expired-status dead code, ISK-at-risk label, margin data source, etc.
Step 7 ESI snippet in the task file (crypto.decrypt / esi.get_access_token / sync._persist_rotated_token) has never been run.
In-game order counts should be re-checked periodically (currently 10 + 6 + 0).

Pending planner jobs (one per message, planner edits agent-tasks only)
Backlog task files for: the 500 / JSONResponse hypothesis correction, Tal Beyond sync, invalid_scope, expired-status dead code, ISK-at-risk label, margin data source, slow controls.
Upstream author
EVE character Luciela (he/him). Before any merge, fetch + diff; review sso.py, crypto.py, config.py, sync.py for new network calls or scopes. Keep Tracy’s characters on the local instance.
First move for the next reviewer

Confirm git status -sb in both repos and that the two .bak-* files are gone.
Confirm whether the logging commit was pushed.
Decide next work item with Tracy (most natural: the task-file / status.md correction for the sort → JSONResponse hypothesis, or invalid_scope).