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

A Solr username/password was present in the Resources section of:

`projects/wvulibraries_knapsack/tasks/active/2026-11-02-HYKU-7.2.0-UPGRADE-REVERT-CLEAN.md`

The file was redacted in commit `a271045641401356605bfae1c1df4e2b46462d98`, but the original value remains recoverable from earlier public Git history unless that history is separately rewritten. The repository `trmccormick/agent-tasks` is public. The WVU Knapsack source repository was also confirmed public:

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

### Phase 1 — Confirm Scope and Consumers

1. Identify the Solr account associated with the exposed value using the approved secret store or an authorized administrator. Do not print or disclose the secret.
2. Determine whether the account is still active and whether it is shared with other services or people.
3. Inventory every consumer and environment that uses this account, including as applicable:
   - WVU Knapsack application configuration and environment files
   - Docker Compose / container environment configuration
   - hykudev, staging, and production deployments
   - indexing jobs, scheduled jobs, scripts, and administrative tooling
   - local development configurations, if they use the same account
4. Search the public `wvulibraries/wvu_knapsack` repository and relevant configuration history for references to the account or embedded Solr credentials. Report locations and commit references only; never reproduce secret values.
5. Confirm who owns Solr account administration and who can update deployment secrets. Agents must not access or operate VMs; coordinate VM changes with DevOps.

### Phase 2 — Prepare Replacement

1. Use the approved secret store/configuration mechanism. Do not put the replacement value in Git, task files, logs, issue comments, or chat.
2. Prefer creating a separate least-privilege replacement account/credential if supported and approved. Otherwise agree on a coordinated in-place password change.
3. Record the secret-store reference/name and the environments updated, not the secret itself.
4. Prepare a rollback/recovery approach that does not require restoring or reusing the exposed credential.

### Phase 3 — Deploy and Verify

1. Coordinate the Solr-side change with updates to all inventoried consumers.
2. Update the relevant secret-store entries or deployment environment configuration.
3. Restart/redeploy only the services required to load the new configuration, following the established DevOps procedure.
4. Verify:
   - Solr authentication succeeds for the application.
   - Search requests work.
   - Indexing and background jobs can write to Solr.
   - Relevant logs show no authentication failures.
   - Each environment in scope is using the replacement credential.
5. If verification fails, stop and use the approved recovery plan. Do not restore the exposed credential as a permanent workaround.

### Phase 4 — Revoke and Close Out

1. Revoke or disable the exposed credential/account once all consumers have been migrated and verified.
2. Confirm the old credential no longer authenticates.
3. Review available Solr/authentication logs for suspicious use where feasible; record the time range and limitations of the review. Do not claim that absence of log evidence proves the credential was never accessed.
4. Record completion, affected environments, secret-store references, verification results, and any remaining remediation.
5. Track Git-history cleanup separately. Redacting the current file does not remove the old value from prior commits, forks, caches, or clones. Any history rewrite must be separately approved and coordinated with repository collaborators.

## Acceptance Criteria

- [ ] Affected Solr account and credential status identified without exposing the secret.
- [ ] All environments and consumers inventoried.
- [ ] Public WVU Knapsack repository checked for current and historical credential references; findings recorded without secret values.
- [ ] Replacement credential provisioned and stored only in the approved secret store.
- [ ] All in-scope consumers updated.
- [ ] Search, indexing, and relevant background operations verified in each in-scope environment.
- [ ] Exposed credential revoked/disabled and confirmed unusable.
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
- Credential source of truth: approved secret store / environment configuration (identify during Phase 1; do not record secret values here)
