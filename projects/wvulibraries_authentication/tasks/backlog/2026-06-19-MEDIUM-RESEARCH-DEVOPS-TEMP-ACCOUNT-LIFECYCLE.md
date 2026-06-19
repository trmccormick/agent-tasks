---
title: "DevOps Research - Temp Account Lifecycle & Reset Scripts"
status: backlog
priority: MEDIUM
type: RESEARCH
created: 2026-06-19
updated: 2026-06-19
estimated_effort: "1-2 hours"
sequence: "R3 (supplemental to Seq 4)"
blocked_by:
  - "Seq 4 security audit discovery"
depends_on: []
---

## Objective

Document the external scripts and infrastructure processes that manage temporary account lifecycle, including:
- Daily password reset mechanism
- Account cleanup and removal
- How these processes integrate with the Authentication application
- Schedule and triggering mechanism (cron jobs, systemd timers, etc.)

## Context

Seq 4 security audit discovered that:
1. Plain text passwords stored in `tempAccounts` table (by design — needed for LDAP bind)
2. No password reset mechanism exists in the PHP application
3. Application documentation noted password reset as "not implemented"
4. **Discovery**: External scripts/processes must handle daily temp account reset and cleanup

This gap in documentation is blocking understanding of the true account lifecycle and security posture.

## Success Criteria

✅ Document all scripts involved in temp account management
✅ Identify where scripts are located (cron, systemd, scripts/, etc.)
✅ Document execution schedule (timing, frequency, triggering)
✅ Explain what each script does (reset, cleanup, validation)
✅ Clarify which operations are database-level vs. application-level
✅ Document any dependencies (services, permissions, etc.)
✅ Create TEMP_ACCOUNT_LIFECYCLE.md with complete flow diagram
✅ Confirm: are scripts documented anywhere, or is this institutional knowledge only?

## Investigation Scope

### Files to Review
- `/scripts/` directory (setup.sh, other maintenance scripts)
- System cron jobs on production/dev servers
- systemd timer configurations (if applicable)
- Documentation or comments in existing scripts
- DevOps runbooks or maintenance procedures

### Questions for DevOps

1. **Password Reset Process**:
   - How/when are temp account passwords reset?
   - Is it automated daily, or manually triggered?
   - What command/script executes the reset?
   - Are passwords regenerated, cleared, or set to a default?

2. **Account Cleanup**:
   - How/when are expired temp accounts removed from the database?
   - Is cleanup automated or manual?
   - How long are accounts kept before removal (24 hours, 7 days, etc.)?
   - Are there audit logs of removed accounts?

3. **Infrastructure Integration**:
   - Are scripts in this repository or managed elsewhere?
   - Who maintains/owns these scripts (DevOps team, application team)?
   - Are there monitoring/alerting for reset/cleanup failures?
   - How would we know if this process breaks?

4. **Documentation**:
   - Are these scripts documented in runbooks or wikis?
   - Is there a service/support ticket for questions?
   - Are there SLAs or guarantees for temp account availability?

## Deliverables

**Investigation Report**: `doc/TEMP_ACCOUNT_LIFECYCLE.md`

Include:
1. **Process Flow Diagram** — ASCII art showing when/how temp accounts are created, reset, cleaned up
2. **Script Inventory** — location, purpose, schedule, owner of each script
3. **Execution Timeline** — daily schedule of reset/cleanup operations
4. **Dependencies** — what services/permissions are required
5. **Gaps & Risks** — any undocumented processes or potential single-points-of-failure
6. **Contact Info** — who to reach for questions/support

## Implementation Notes

- **Action**: Reach out to DevOps team directly (Slack, email, or ticket)
- **Scope**: This is research/documentation, not code changes
- **Constraint**: Only document what exists — do not design new processes
- **Output**: One markdown file explaining the actual temp account lifecycle

## Related Tasks

- Seq 4 (Security Audit): Uses this documentation to complete threat model
- Seq 3 Phase 2: May be blocked on understanding password storage context

---

## Task Status Log

- **2026-06-19**: Created after agent discovered external reset mechanism during Seq 4 investigation
