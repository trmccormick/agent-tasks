# Rotate Exposed Solr Credential

**Date Created**: 2026-11-03
**Priority**: HIGH (Security)
**Status**: Ready for coordinated execution
**Project**: WVU Libraries Knapsack

---

## Objective

Treat the Solr credential previously committed in the public `trmccormick/agent-tasks` repository as exposed. Identify every environment and service that uses the affected Solr account, replace the credential through the approved secret-management process, verify service health, and revoke the exposed credential.

Do not copy the exposed credential into this task, logs, chat, tickets, shell history, or any other repository file.

## Background

A Solr username/password was inadvertently committed in plaintext to the public `trmccormick/agent-tasks` repository in the Resources section of the development upgrade task. The current file is redacted and the repository's `main` history has been rewritten to remove the exposed commit from branch ancestry. GitHub Support has created a ticket to clear the still-retrievable cached/dangling commit; that cleanup is not yet confirmed complete. The WVU Knapsack source repository is also public:

https://github.com/wvulibraries/wvu_knapsack

Assume the credential is compromised even if there is no evidence it was used by an unauthorized party.

## Impact / Change Risk

Rotating this credential **can affect the current setup** if any running application, deployment, script, container, or operator workflow still uses it.

- If Solr's password is changed or the account is disabled before all consumers receive the replacement, those consumers may fail Solr authentication. Search, indexing, background jobs, and administrative Solr operations may be affected.
- If a new Solr account/credential can be created alongside the existing one, prefer a staged change: provision replacement access, update and verify consumers, then revoke the exposed account/credential.
- If the existing account must be modified in place, coordinate the Solr-side change and all configuration updates as one maintenance operation. A brief interruption may be unavoidable.
- Do not assume local development, hykudev, staging, and production share the same credential. Inventory each environment separately.

No live configuration or Solr account should be changed until the affected account, consumers, deployment owner, and maintenance/rollback plan are understood.

## Execution Plan

The rotation is deliberately split across the development refresh and the later production 7.2.0 upgrade. Do not perform an uncoordinated standalone production password change.

### Phase 1 — Confirm Scope and Consumers (before development rotation)

1. Identify the Solr account associated with the exposed value through the approved secret store or an authorized administrator. Do not print or disclose the secret.
2. Determine whether the account is active and whether the same account/credential is shared by hykudev, production, staging, local development, scripts, or other services.
3. Inventory consumers and identify who owns Solr account administration and deployment-secret updates.
4. Search the public `wvulibraries/wvu_knapsack` repository and relevant configuration history for credential references. Record locations and commit references only; never reproduce secret values.
5. Agents must not access or operate VMs. Coordinate VM and deployment changes with DevOps.

### Phase 2 — Development rotation during the hykudev refresh (Wednesday Nov 4)

This work is coordinated with `2026-11-02-HYKU-7.2.0-UPGRADE-REVERT-CLEAN.md`.

1. DevOps restores hykudev from the Thursday Oct 29 snapshot.
2. Complete the planned development-only Solr cleanup and fresh data import; verify the clean 7.1.3 baseline.
3. During preparation/testing of the Hyku 7.2.0 upgrade, provision replacement Solr access through the approved secret store/configuration mechanism. Prefer a separate least-privilege replacement account/credential if supported.
4. Update hykudev and its dependent jobs/tools to use the replacement. Restart/redeploy only the services required to load the new configuration, following the established DevOps procedure.
5. Verify Solr authentication, search, indexing, background jobs, and relevant logs on hykudev.
6. Revoke the exposed credential only after confirming no un-migrated consumer depends on it. If production shares the same account/credential, do not disable it during the development change unless production has a separately provisioned working credential. Coordinate the final revocation with the production cutover if necessary.

### Phase 3 — Production rotation during the production Hyku 7.2.0 upgrade (future)

Production rotation is deferred until the 7.2.0 production upgrade is ready and approved.

1. Reconfirm all production consumers and whether the production credential is shared with any other environment.
2. Prepare and stage the replacement credential in the approved secret store. Use the configuration and verification procedure proven on hykudev.
3. Coordinate the Solr-side change, production secret/configuration updates, and service deployment as part of the production upgrade.
4. Preserve all existing production content and Solr data. **Do not clear collections, delete documents, re-import repository data, or use the hykudev cleanup procedure in production.**
5. Verify production Solr authentication, search, indexing, background jobs, and relevant logs after the upgrade.
6. Revoke the exposed credential once all production and other in-scope consumers have migrated and passed verification.

### Phase 4 — Close Out

1. Confirm the exposed credential no longer authenticates after revocation.
2. Review available Solr/authentication logs for suspicious use where feasible; record the time range and limitations. Absence of log evidence does not prove the credential was never accessed.
3. Record completion, affected environments, secret-store references, verification results, and any remaining remediation.
4. Track GitHub cached-history cleanup separately. The history rewrite is complete, but GitHub Support's cache/dangling-object cleanup remains pending until confirmed.

## Acceptance Criteria

- [ ] Affected Solr account and credential status identified without exposing the secret.
- [ ] Development and production consumers inventoried separately; shared-credential dependencies identified.
- [ ] Public WVU Knapsack repository checked for current and historical credential references; findings recorded without secret values.
- [ ] Replacement credential provisioned and stored only in the approved secret store.
- [ ] Development credential rotated and hykudev search, indexing, and background operations verified during the Nov 4 refresh/7.2.0 test.
- [ ] Production credential rotation completed during the approved production 7.2.0 upgrade, with production data and indexes preserved.
- [ ] All consumers migrated before the exposed credential is revoked/disabled and confirmed unusable.
- [ ] Available authentication logs reviewed and limitations documented.
- [ ] Git-history remediation considered as a separate, explicitly approved operation.

## Safety Constraints

- Never print, paste, commit, or otherwise reproduce either the exposed or replacement credential.
- Do not make live Solr, VM, or production changes without the responsible administrator/DevOps coordination.
- Do not run destructive Solr collection operations as part of this task.
- Do not rewrite public Git history as part of credential rotation.
- Do not assume which environments share the credential; verify first.

## Resources

- Redacted task file: `projects/wvulibraries_knapsack/tasks/active/2026-11-02-HYKU-7.2.0-UPGRADE-REVERT-CLEAN.md`
- Public source repository: https://github.com/wvulibraries/wvu_knapsack
- Redaction commit: https://github.com/trmccormick/agent-tasks/commit/a271045641401356605bfae1c1df4e2b46462d98
- Development upgrade coordination: [2026-11-02-HYKU-7.2.0-UPGRADE-REVERT-CLEAN.md](2026-11-02-HYKU-7.2.0-UPGRADE-REVERT-CLEAN.md)
- GitHub Support cache-cleanup ticket: reference held in the GitHub Support confirmation email; add ticket number here when available.
- Credential source of truth: approved secret store / environment configuration (identify during Phase 1; do not record secret values here)
