---
title: "Research: Temporary Account Password Reset & Shared MySQL Instance Impact"
status: active
priority: MEDIUM
type: RESEARCH
created: 2026-06-18
updated: 2026-06-18
estimated_effort: "2-3 days"
sequence: "Research (pre-Seq 3)"
blocked_by: []
blocks:
  - "2026-06-18-HIGH-MAINTENANCE-DEPRECATED-MYSQL-FUNCTIONS"
branch: "N/A (Research only, no code changes)"
---

# Task: Research — Temporary Account Password Reset & Shared MySQL Instance Impact

## Problem
During MySQL 8.4 compatibility testing, discovery that:
1. Application may reset passwords in temporary accounts table
2. MySQL instance may be shared with other WVU Libraries applications
3. Planned MySQL functions migration (Seq 3) could affect other apps if schema is shared
4. No clear documentation on:
   - How temp account password resets work
   - Which other applications use this MySQL instance
   - What dependencies exist between apps
   - Whether schema changes are safe across all consumers

**Critical Unknown**: If we refactor mysql_* functions or modify schema, will it break other applications?

## Scope

### Phase 1: Understand Password Reset Mechanism
1. Locate password reset functionality in codebase:
   - Search for `tempAccounts` table updates
   - Find any `UPDATE` queries that modify password fields
   - Identify where passwords are hashed/generated/reset
   - Files to check:
     * `src/public_html/authentication/tempAccounts/` (temp account UI)
     * `src/phpincludes/` (backend logic)
     * `src/public_html/engineIncludes/` (LDAP/auth functions)

2. Document password reset workflow:
   - When do passwords get reset? (on creation? on expiry? on demand?)
   - What triggers password reset?
   - How are passwords stored? (plain text? hashed? bcrypt? md5?)
   - Are there SQL functions involved in reset? (STORED PROCEDURES? USER FUNCTIONS?)

3. Identify affected tables:
   - Which columns are modified: `password`, `passwordHash`, `status`, `updated_date`?
   - Are there indexes on password columns?
   - Are there foreign keys to other tables?

### Phase 2: Identify Shared MySQL Instance & Dependencies
1. Discover other applications using this MySQL instance:
   - Check for other databases in MySQL (besides `authentication`)
   - Search DevOps docs for applications using `database.lib.wvu.edu`
   - Check `/home/systems/` for other PHP applications
   - Ask DevOps: "Does any other application use the `authentication` database?"

2. Identify applications & their dependencies:
   - MFCS (Migrating to Knapsack — check if it still uses authentication DB)
   - Any other services referencing `tempAccounts` table
   - Any external scripts or cron jobs that read/write to authentication DB

3. Check for shared functions/procedures:
   - Are there STORED PROCEDURES that other apps depend on?
   - Are there VIEWS or TRIGGERS that cross-reference multiple apps?
   - Any application-specific SQL functions?

### Phase 3: Assess Refactoring Risk
1. Map mysql_* functions to actual usage:
   - Which deprecated functions are used in password reset?
   - Which are used in other apps (if any)?
   - Which are used in read-only queries vs. write operations?

2. Document breaking change risk:
   - If we convert `mysql_*` → `mysqli`, will it break other apps?
   - If we modify schema, what rollback plan exists?
   - What's the blast radius of changes?

3. Recommend safe refactoring path:
   - Can we refactor in isolation (this app only)?
   - Do we need to coordinate with other app teams?
   - Should schema changes be versioned or backward-compatible?

## Success Criteria
- [ ] Password reset mechanism fully documented (workflow, tables, functions)
- [ ] All tables involved in password reset identified
- [ ] Other applications using MySQL instance identified (or confirmed: "only Authentication uses it")
- [ ] Shared dependencies mapped (or confirmed: "none")
- [ ] Risk assessment completed: "Safe to refactor" OR "Requires coordination with [apps]"
- [ ] Recommendation provided: "Proceed with Seq 3" OR "Wait for [dependency]" OR "Coordinate with [team]"
- [ ] Discovery document created (password_reset_findings.md)

## Output Deliverable
Create a file: `docs/MYSQL_INSTANCE_DEPENDENCIES.md`
- Section 1: Authentication app password reset workflow
- Section 2: Other applications using MySQL instance (if any)
- Section 3: Shared schema/function dependencies (if any)
- Section 4: Refactoring risk assessment
- Section 5: Recommendation for Seq 3 (MySQL functions migration)

## Important Notes
- **No code changes in this task** — research only
- **Application is running**: Can inspect behavior live if needed
- **DevOps coordination needed**: May need to ask DevOps about other app dependencies
- **Blocks Seq 3**: MySQL functions migration depends on understanding these dependencies
- **Context**: If other apps use this instance, we can't just refactor freely
- **Timing**: Should complete before Seq 3 begins to avoid surprises

## Related
- Seq 3: Deprecated MySQL Functions Migration (blocked by this research)
- MFCS migration to Knapsack (may have shared dependencies)
- Production database: database.lib.wvu.edu (multi-app or dedicated?)

## Questions to Answer
1. Does the application reset temporary account passwords? How? When?
2. What password storage mechanism is used? (plain text / hashed / bcrypt)
3. Are any SQL functions involved in password reset? (STORED PROCEDURES / TRIGGERS / UDFs)
4. Do other WVU Libraries applications use the `authentication` database?
5. Are there shared functions/views/procedures across applications?
6. What's the blast radius if we refactor `mysql_*` functions?
7. Can Seq 3 proceed safely, or do we need cross-app coordination?

## Stop Conditions
- If DevOps confirms: "Only Authentication uses this instance" → can proceed with Seq 3 safely
- If DevOps warns: "MFCS also reads from here" → research how MFCS depends on current schema
- If you find STORED PROCEDURES: Document them completely before Seq 3 refactors functions
- If you find TRIGGERS: Ensure they're not dependent on deprecated `mysql_*` syntax
