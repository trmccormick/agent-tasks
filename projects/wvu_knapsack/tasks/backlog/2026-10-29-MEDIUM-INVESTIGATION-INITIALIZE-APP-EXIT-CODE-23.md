# Task: Investigate initialize_app Container Exit Code 23 (Transient Failure)

---
status: backlog
resume: false
priority: MEDIUM
type: investigation
system_domain: DEPLOYMENT
mvp_alignment: OTHER
local_worker_safe: false
project: wvulibraries_knapsack
created: 2026-10-29
last_updated: 2026-10-29
---

## Problem

When running `up.sc.local.sh` (or `up.sh` on hykudev), the `initialize_app` container occasionally fails with exit code 23:

```
✘ Container wvu_knapsack-initialize_app-1               Error service "initialize_app" didn't complete successfully: ex...  166.0s
service "initialize_app" didn't complete successfully: exit 23
```

**Key observation**: The failure is **transient** — retrying the `up.sc.local.sh` script immediately afterward succeeds without any code changes.

## Evidence

- **Observed**: 2026-10-29 during v7.2.1 upgrade testing (locally)
- **Exit code 23**: Typically indicates a Ruby exception or script failure during Rails initialization
- **Reproducibility**: Non-deterministic; doesn't fail consistently on retry
- **Scope**: Affects both local `up.sc.local.sh` and production `up.sh` deployments

## Questions to Answer

1. **What is the root cause of exit code 23?**
   - Is it a race condition (database not ready, migrations incomplete)?
   - Is it a gem/dependency issue that resolves on retry?
   - Is it a transient network/storage issue?

2. **Why does retry succeed?**
   - Does docker compose rebuild something?
   - Does the container retry its entrypoint?
   - Is this a known docker compose behavior?

3. **Is this related to v7.2.1 upgrade?**
   - Did this occur before the facet workaround removal?
   - Is it specific to the hyrax-webapp v7.2.1 submodule?
   - Or is it a pre-existing intermittent issue?

4. **Can we prevent it?**
   - Add retry logic to `up.sc.local.sh` / `up.sh`?
   - Fix the underlying race condition?
   - Add better error logging to `initialize_app` container?

## Acceptance Criteria

1. Root cause identified via full error logs from `docker compose logs initialize_app`
2. Determine if this is new (v7.2.1) or pre-existing
3. Propose a fix (retry logic, sequence fix, or code change)
4. Document any workarounds in SESSION_GUIDANCE.md if not fixable

## Implementation Notes

- Run `docker compose logs initialize_app | tail -100` when failure occurs (do NOT retry immediately)
- Compare success vs failure logs side-by-side
- Check if database migrations are the bottleneck (add timing logs)
- Consider if `db-wait.sh` timeout is too short
- Check hyrax-webapp v7.2.1 changelog for any initialization changes

## Related Tasks

- Facet workaround removal (2026-10-28): Removed decorator YAML logic; may have exposed this issue
- v7.2.1 submodule update: New version may have timing-sensitive initialization

## Owner

Planning agent (research) → Implementation agent (if a code fix is needed)
