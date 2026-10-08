# Project facts stated by Tracy, 2026-10-07
Source: Tracy, in chat. Label all of this REPORTED (by Tracy), not verified against the repos.

## Folders
- wvu-moonshot: PAUSED. Event canceled by the university. Incomplete prototype for a one-off iPad event check-in, tested by a few people. Never built: is_alumni flag, education-history display, manual-entry form. Useful starting point for future event apps. Keep in place; do not archive or delete. Event-specific parts: the October 2026 date and the iPad check-in.
- wvulibraries_authentication: ACTIVE (live, stable). Internal app for library staff to issue temporary patron computer accounts. Quiet tracking since June (last milestone June 19, Seq 7 Phase 1) is not project inactivity.
- wvulibraries_acda_portal: ACTIVE (live) at congressarchives.org and congressarchivesdev.lib.wvu.edu. Educational site. Harvests from various partner sites using Bulkrax; WVU digitalhistory Hyku is one source (earlier a Hydra-head collection). Samvera stack, closer to a Hydra head. ActiveFedora to Fedora; no Valkyrie, no Wings. Its Bulkrax version differs from samvera_hyku's. Overengineered for its need. Modernization plan lives in the repo (hydra_acda_portal_public, MODERNIZATION.md): in flux, may change; target PostgreSQL/ActiveRecord, ActiveStorage, good_job, Fedora removed. Some of that work is already done (which parts: determine from the repo, label VERIFIED/REPORTED). Phase 1a/1b in agent-tasks status are blocked on DevOps approval for a dev VM window; agent-tasks phase names differ from the plan's, so use the plan's names. Link to the plan; do not copy it.
- hyku (folder): the samvera fork. Tracy believes it is not needed; samvera_hyku is the active one. Fold its one task (2026-09-17-MEDIUM-BACKPORT-FACET-LIMITING-CONFIGURATION-TO-HYKU.md, waiting on Tracy's WVU production validation) into samvera_hyku/tasks/backlog/ first, then move the folder to an archive location. Do not delete.
- samvera_hyku: ACTIVE. digitalhistory runs Hyku 7.1.3 / Hyrax 5.2.0.
- Other folders as in 2026-10-07-PLAN-SAMVERA-REORG.md sections 1 and 5, except as corrected above.

## Conventions decided
- First lines of every project status.md: Project state: active | paused | archived, plus a one-sentence reason.
- Every project README gets a stack-versions line (persistence layer, Rails, Hyku/Hyrax/Bulkrax versions) read from Gemfile.lock.
- Do not rename existing folders; apply naming only to new ones.
- agent-tasks links to plans that live in a repo; it does not copy them.
- Cross-repo features (Hyrax, Hyku, Knapsack): add one line "does this affect ACDA's harvest of digitalhistory?". Tracking pattern (single feature file vs linked task files) is still Tracy's decision.

## Not applied yet
Nothing in the repo has been changed for any of the above.
