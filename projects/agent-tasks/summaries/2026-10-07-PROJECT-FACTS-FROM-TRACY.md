# Project facts stated by Tracy, 2026-10-07
Source: Tracy, in chat. Label all of this REPORTED (by Tracy), not verified against the repos.
Revised 2026-10-09 with corrections and additions from Tracy in chat. Colleagues are named by role or team only, because this repo is public.

## Folders
- wvu-moonshot: PAUSED. Event canceled by the university. Incomplete prototype for a one-off iPad event check-in, tested by a few people. Never built: is_alumni flag, education-history display, manual-entry form. Useful starting point for future event apps. Keep in place; do not archive or delete. Event-specific parts: the October 2026 date and the iPad check-in.
- wvulibraries_authentication: ACTIVE (live, stable). Internal app for library staff to issue temporary patron computer accounts. Quiet tracking since June (last milestone June 19, Seq 7 Phase 1) is not project inactivity.
- wvulibraries_acda_portal: ACTIVE (live) at congressarchives.org and congressarchivesdev.lib.wvu.edu. Educational site. Harvests from various partner sites using Bulkrax. WVU's data was first served by a Hydra-head site, mcppc.lib.wvu.edu, which existed specifically to serve WVU's data to congressarchives.org; the same data is now served by the Hyku instance digitalhistory.lib.wvu.edu, and the old URL redirects. Samvera stack, closer to a Hydra head. ActiveFedora to Fedora; no Valkyrie, no Wings. Its Bulkrax version differs from samvera_hyku's. Overengineered for its need. Modernization: MODERNIZATION.md in the repo (hydra_acda_portal_public) is a proposal, in flux, with no real target yet. The only work done so far is replacing Sidekiq with good_job, by a junior developer, as the starting point; whether it is deployed is not recorded. Phase 1a/1b in agent-tasks status are blocked on DevOps approval for a dev VM window; agent-tasks phase names differ from the plan's. Link to the plan; do not copy it. Watch item: Cloudflare was reported earlier as blocking thumbnails; that appears resolved and is a concern to monitor. WVU records were cleared from ACDA and reimported after the WVU harvest source changed; the overall work is still in progress.
- hyku (folder): the samvera fork. Tracy believes it is not needed; samvera_hyku is the active one. Its one task (2026-09-17-MEDIUM-BACKPORT-FACET-LIMITING-CONFIGURATION-TO-HYKU.md) was recorded as waiting on Tracy's WVU production validation; it is now on hold, not dropped: a colleague on the Notch8 team, who works with Samvera, may have a solution that works upstream, and the two approaches will be compared (discussed last week). Fold the task into samvera_hyku/tasks/backlog/ first, then move the folder to an archive location. Do not delete.
- samvera_hyku: ACTIVE. The active Samvera Hyku folder. The Phase 1 GA fix (branch fix/ga-tenant-property-scoping) is Tracy's work for Samvera. Testing it for cross-tenant pollution needs a VM with Google Analytics on both tenants, which the current Hyku product owner is to set up; Phase 2 waits on that. Tracy plans to follow up with him directly, likely next week.
- wvulibraries_knapsack: ACTIVE. This repo is the digitalhistory.lib.wvu.edu application. Its aim is to retire multiple individual Hydra-head sites (too many to list) to make management easier and to be more in line with the Samvera community. The main VM, hyku.lib.wvu.edu, serves two tenants, demo and digitalhistory. The same git repo also runs on hykudev.lib.wvu.edu with a demo tenant only. digitalhistory runs Hyku 7.1.3 / Hyrax 5.2.0. Pending: DevOps has a new puma.rb that needs to be applied as a proper override in the knapsack. It currently sits in the hyrax-webapp folder, which is the wrong location. It works on the VM. Probably not a task yet.
- wvulibraries_databases: ACTIVE. The Rails 7 update is paused on CSS issues in the main navigation menu on the admin side (not the public side). Most were resolved this week, and the rest were passed to the lead frontend developer at WVU Libraries for final polish.
- Other folders as in 2026-10-07-PLAN-SAMVERA-REORG.md sections 1 and 5, except as corrected above.

## Stated by Tracy
- Tracking is created on demand: Tracy prompts an agent to create it after she pulls a repo. Not every repo needs a folder.

## Conventions (proposed; confirm before applying)
- First lines of every project status.md: Project state: active | paused | archived, plus a one-sentence reason.
- Every project README gets a stack-versions line (persistence layer, Rails, Hyku/Hyrax/Bulkrax versions) read from Gemfile.lock.
- Do not rename existing folders; apply naming only to new ones.
- agent-tasks links to plans that live in a repo; it does not copy them.
- Cross-repo features (Hyrax, Hyku, Knapsack): add one line "does this affect ACDA's harvest of digitalhistory?". Tracking pattern (single feature file vs linked task files) is still Tracy's decision.

## Not applied yet
No project status.md or README has been changed for any of the above.
