Task: 2026-08-06-HIGH-BUGFIX-MARKET-STABILIZATION-SERVICE-MISSING-THRESHOLD-METHOD.md
(backlog/current/, confirmed to exist earlier this session)

This is small, isolated, and does NOT touch anything currently in use by
another agent — safe to run in parallel.

STEP 0 — move task file backlog→active (git mv), update status, verify
with find (single result), paste both before proceeding.

Root cause (already confirmed): calculate_minimum_threshold(settlement, item)
is called by handle_production_shortages but never defined — NoMethodError
risk if that path is ever invoked. Note identify_import_candidates DOES
exist (already confirmed, line 251) — don't rebuild that.

Fix: implement calculate_minimum_threshold following whatever pattern
similar threshold/minimum-level methods in the same service use. Run
relevant specs, confirm 0 new failures. Commit, move to completed/2026-08/,
verify with find (single result).