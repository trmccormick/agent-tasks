# DRAFT TASK REVIEW

## Task Identification

- Task files: `investigate_gemfile_lock_hykudev_reboots.md`, `test_7.2.0_upgrade_locally_with_data_persistence.md`
- Project repository: wvulibraries/wvu_knapsack
- Package commit: `ca16a3c` (not independently checked)
- Review date: 2026-11-17
- Reviewer: Claude (web session), saved by Haiku

## 1. Disposition

**REVISE** (both tasks).

Task 2 contains a command that contradicts the "Postgres volume persists" decision (`down --volumes`). It must be fixed before anything is dispatched. Neither task has the mandatory template structure. The investigation's methodology and the testing task's criteria also need tightening.

## 2. Evidence Boundary

- **Read directly (VERIFIED):** both draft task files, `REVIEW_AGENT_WORKFLOW.md`, `REVIEW_REPORT_TEMPLATE.md`, and the review prompt.
- **Not inspected:** `TASK_TEMPLATE.md`, `REVIEW_AGENT_GUIDE.md`, the wiki entries, commit `ca16a3c`, the knapsack repo, and the local Gemfile.lock backup (checksum is REPORTED only).
- Phase A is provisional until re-checked against TASK_TEMPLATE.md; the missing Agent Dispatch Interface block was confirmed by grep (0 matches in both drafts).
- **Consequence:** Phase A is judged against the mandatory-section list in the prompt, not the template itself.
- **Hyku behavior:** statements about Hyku/Rails behavior below come from general knowledge and should be checked against the repo.

## 3. Phase A — Template Conformance

**Result: FAIL (both tasks).** Compared against the prompt's mandatory list:

| Required item | Task 1 | Task 2 |
|---|---|---|
| YAML fields (resume, system_domain, mvp_alignment, local_worker_safe, status: draft) | Missing; uses a bold header block instead | Missing; same |
| `status: draft` | Says "Ready for Execution" | Says "Ready for Execution" |
| DRAFT STATUS NOTE | Missing | Missing |
| DRAFT REVIEW CHECKLIST | Missing | Missing |
| Full Agent Dispatch Interface | Missing | Missing |
| Task Readiness Checklist | Missing | Missing |

Both files use a different structure entirely and assign themselves to Qwen. If the template has further required sections, they are likely missing too.

## 4. Phase B — Content Assessment

- **Acceptance criteria:** Task 1's "can explain…" is not measurable. Task 2's "SAME or close" for Solr counts is undefined.
- **Scope:** Task 2 drifts into hykudev replication, "plan prod timeline," and re-investigating gem conflicts.
- **Dependencies:** "Depends On" is prose only. It doesn't say which investigation output Task 2 consumes.
- **Architecture and paths:** every path is a `/Users/tam0013/...` or `.copilot/session-state/...` local path that Qwen or the repo may not have.
- **Methodology:** Task 1 conflates resolution errors with runtime boot failures.
- **Verification:** Task 2 separates build, Postgres, Solr and UI well, but the commands are shaky (see findings).
- **Review gates:** "Ready for Execution" and a pre-assigned worker bypass the drafts-to-active human gate.

## 5. Findings

### F1 — CRITICAL — Task 2, Phase 2 step 9

- **Evidence (VERIFIED):** `docker compose down --volumes --remove-orphans`.
- **Why it matters:** `--volumes` removes named volumes, which normally includes the Postgres volume and likely the Solr volume. That destroys the data being tested and contradicts the Rollback Plan's own `docker compose down`. It also makes the "does Solr persist?" check meaningless.
- **Correction:** use `docker compose down --remove-orphans` with no `--volumes`. Add an explicit "never use `-v`/`--volumes`" warning.

### F2 — HIGH — Task 2, no pre-switch backup

- **Evidence (VERIFIED):** the only backup mention is "database snapshots may be available from earlier sessions."
- **Why it matters:** the established decision says rollback via `git checkout main` always works. Once 7.2 migrations run against the persisted DB, 7.1.3 code may not run against the migrated schema. This is a caution on that decision, not a reopening of it.
- **Correction:** add a mandatory `pg_dump` (and a Solr note) before Phase 2, plus a restore step in the Rollback Plan. State the schema-migration caveat.

### F3 — HIGH — Both tasks and the submodule rule

- **Evidence (VERIFIED):** Task 1 deletes `hyrax-webapp/Gemfile.lock`. Task 2 step 8 copies a lockfile into `hyrax-webapp/`.
- **Why it matters:** both modify the working tree of the read-only submodule.
- **Correction:** state whether Gemfile.lock is untracked or ignored in the submodule. Require that these changes never be committed and are reverted at the end (`git -C hyrax-webapp status` clean). Otherwise use a build-context override.

### F4 — HIGH — Task 1 methodology

- **Evidence (VERIFIED):** Q1 and step 4 focus on "resolution errors" and `docker compose build` output. The symptom is runtime reboots.
- **Why it matters:** a fresh resolve usually succeeds with newer, breaking gem versions rather than failing. Build logs won't show the crash.
- **Correction:** require:
  1. A lockfile diff of the fresh versus the backup lockfile.
  2. Boot logs from `docker compose up` for the crash.
  3. A bounded bisect: restore the backup lockfile, then change one suspect gem at a time to show causation.
  4. Mapping each crash to a gem.

### F5 — HIGH — Task 2 console commands are unverified

- **Evidence (VERIFIED):** the tasks use `Item.count` and `Hyrax::Indexer.index_remote_objects`.
- **Why it matters:**
  - I can't confirm either exists in Hyku or the knapsack.
  - Hyku is multi-tenant, so console queries without switching to the tenant can return 0 and falsely signal data loss.
  - Reindexing in Hyku normally depends on the adapter (ActiveFedora versus Valkyrie), via a rake task or `reindex_everything`.
- **Correction:** a person who knows the stack should confirm the model names, tenant switching and the real reindex command. Prefer `rails runner` one-liners that write to a file over interactive `>` prompts.

### F6 — MEDIUM — Both tasks, branch and version ambiguity

- **Evidence (VERIFIED):** Task 1's Objective and Q1 say 7.2.0, but its procedure uses 7.2.1. Task 2's title says 7.2.0, but its primary branch is 7.2.1.
- **Correction:** pick one branch per task and rename the files and titles to match.

### F7 — MEDIUM — Task 2, scope creep and overlap

- **Evidence (VERIFIED):** "replicate on hykudev," "plan prod update timeline," and scenario 4 (gem conflicts during build).
- **Why it matters:** scenario 4 duplicates Task 1. The hykudev and prod items go beyond the "data safety only" decision.
- **Correction:** remove them and add "Do not access hykudev or prod; report and stop." Have Task 2 start from the lockfile approach Task 1 recommends. Facet and search checks should be minimal smoke tests.

### F8 — MEDIUM — Shared local stack

- **Why it matters:** both tasks use the local Docker stack and could clobber each other's containers or volumes.
- **Correction:** sequence them explicitly, or require Task 1 to use a separate clone and compose project name so it never touches the data volumes.

### F9 — MEDIUM — Non-portable resources

- **Evidence (VERIFIED):** absolute `/Users/tam0013/...` paths, a "testing best practices doc" with no path, and "upload logs to session."
- **Correction:** use repo-relative paths, point to a repo location for the backup and test plan, and write logs to one scratch directory with a cleanup step. A human uploads them.

### F10 — LOW — Task 1 speculation and undefined outputs

- **Evidence (VERIFIED):** Q3 asks to explain how prod's rollout "avoided" the problem. The analysis file's save location is undefined, and a wiki investigation page already exists.
- **Correction:** limit Q3 to evidence-backed statements. Specify where the file is saved and whether it supersedes the wiki page. Add a time-box and stop conditions.

## 6. Strengths

- Clear problem statements and good background context.
- The comparison-table deliverable in Task 1.
- Task 2 separates build, boot, Postgres, Solr and UI verification.
- Pass/fail lists, baseline-then-compare structure, and a rollback section exist.
- The Task 1 wiki reference gives it a useful starting point.
- Both tasks acknowledge that partial findings are valuable.

## 7. Acceptance Criteria Assessment

| Criterion | Measurable? | Verifiable? |
|---|---|---|
| T1: "can explain" gem(s), errors, prod difference | No | Partly |
| T1: Hyku vs. Knapsack issue | No | Needs the bisect evidence |
| T2: Build succeeds | Yes | Yes |
| T2: Boot without crash | Yes | Define "Listening on" plus no errors in N minutes |
| T2: Item.count > 0 | Weak | Should equal the baseline count (see F5) |
| T2: Solr "same or close" | No | Define a tolerance or require exact |
| T2: Search and facets work | Vague | Name specific queries and facets |
| T2: No MissingTemplate | Yes | Yes, via the log grep |

Add exact pass/fail criteria for partial outcomes, since the document defines them only in prose.

## 8. Scope and Workflow Assessment

- **Bounded?** Task 1 is mostly bounded but needs a time-box and stop conditions. Task 2 is over-scoped (F7).
- **Dependencies:** the investigation-first order is right but needs an explicit gate (F7, F8).
- **Review gates:** not preserved. Both say "Ready for Execution" with a pre-assigned worker. There is no statement that hykudev, prod or any push requires Tracy's explicit authorization.

## 9. Recommended Changes

### Mandatory before approval

1. Convert both to the template: YAML, status: draft, draft note and checklist, full Dispatch Interface, readiness checklist (F-Phase A).
2. Remove `--volumes` and add a pg_dump backup and restore (F1, F2).
3. Resolve the submodule-modification conflict (F3).
4. Redo the Task 1 methodology (F4).
5. Verify the Task 2 console and reindex commands, including tenant switching (F5).
6. Fix the branch and version naming (F6) and remove the Task 2 scope creep (F7).
7. Add the human-authorization gate and the no-hykudev/prod statement.

### Optional, can be deferred

- Sequence or isolate the local stack (F8).
- Fix the non-portable paths and log locations (F9).
- Task 1 time-box and file location (F10).
- Consider splitting Task 2 into smaller tasks: (a) baseline and backup, (b) branch switch and boot, (c) persistence and UI check. This would suit Qwen better than one seven-phase task.
- The workflow doc uses GO/REVISE/STOP in its table but PROCEED/CAUTION/STOP in its artifact format. Reconcile these.

## 10. Final Recommendation

Do not move either task to `backlog/`; revise and re-review. F1 is a data-destroying command in a task whose purpose is data safety, so it is the first fix. Human authorization is still required before any dispatch.
