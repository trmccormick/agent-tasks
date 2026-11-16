---
status: backlog
priority: LOW
type: refactor
system_domain: MATERIALS | INFRASTRUCTURE
created: 2026-07-30
---

# Decide: should material cache refresh restart the server daily?

## Context
`config/schedule.rb` was updated 2026-07-30 to run
`maintenance:refresh_material_cache` as part of the existing daily job
that also restarts the server (log/temp cleanup → cache refresh →
restart). The cache refresh itself doesn't require a restart —
`reset_cache!` plus a reload happens in-process. Worth deciding whether
to leave it bundled with the existing restart, or split it onto a
lighter standalone cron entry with no restart.

## Decision needed
Is the daily restart already happening for reasons independent of
material freshness (log/temp cleanup) such that bundling the cache
refresh in is harmless — or does "refresh happens right before restart"
make the restart cadence load-bearing for material freshness in a way
that should be decoupled, so a future change to the restart schedule
doesn't silently also change how often materials refresh?

## Note
Not urgent. Explicitly fine to leave until there's time to look at
`config/schedule.rb` directly.
