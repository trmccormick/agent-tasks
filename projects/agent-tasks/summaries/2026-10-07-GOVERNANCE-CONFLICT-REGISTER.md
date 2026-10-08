# Governance conflict register (read-only review)
Reviewed by Claude, 2026-10-07. Sources: rules/GUARDRAILS.md (Last Updated 2026-09-06 header, MAG-1..6 present), ROUTING_LOGIC.md (2026-06-17), rules/AGENT_ROUTING.md (2026-06-19), TASK_TEMPLATE.md, README.md (2026-06-23), as uploaded by Tracy.
Evidence: all rows are REPORTED from the five uploaded files. Nothing here was verified against the live repo. Classification and "suggested resolution" are Claude's proposals; Tracy decides.

## A. Instructions that conflict with controlling rules and can cause harm
| # | Where | Says | Conflicts with | Suggested resolution |
|---|---|---|---|---|
| A1 | README "Task Completion Workflow" Step 2 | If duplicates exist in active/ or backlog/, `rm -f` them | MAG-1 (two canonical copies: stop and escalate, do not delete); Rule 29 (never delete task files without verification and human approval) | Replace with: report duplicates, do not delete |
| A2 | GUARDRAILS Rule 12 | Two results from `find`: "remove it before committing" | MAG-1 (same) | Same: report and escalate |
| A3 | ROUTING_LOGIC "Known Agent Behavior Issues" | Duplicate found: `git rm -f` the stale copy | MAG-1, Rule 29 | Same. Note this file also documents the real failure mode: qwen copies instead of moving |
| A4 | README "Task Completion Workflow" Steps 5-6 | Agent runs `git commit`, then waits for approval before push | Rule 26 (no commit without explicit approval in the current session) | Agent stages and presents; Tracy commits |
| A5 | README "Hard Rules" | Agents "may prepare and execute staging and commits under direct human supervision" | Rule 26 | Align wording to Rule 26 |
| A6 | README "No Multi-Project Cross-Pollination" | Do not reference other repos/directories unless a task blueprint says so | The Hyrax -> Hyku -> Knapsack pattern (features that must change three repos) | Add an exception for linked cross-repo features authorized by Tracy |

## B. Routing and model policy stated in several places, disagreeing
| # | Where | Says | Conflict | Suggested resolution |
|---|---|---|---|---|
| B1 | GUARDRAILS Rule 23 ("Golden Rule: local first, cloud second, premium last") and Rule 24 (Perplexity validation role) | Fixed escalation ladder and per-agent role | MAG-3 in the same file: "Fixed routing ladders are prohibited ... local-first ... rejected" | Reclassify Rules 23/24 as routing preferences (move to agent notes / routing doc) or retire |
| B2 | AGENT_ROUTING Hard Rules "Local First: Always attempt with local models" and the "all three conditions" cloud escalation | Fixed ladder | MAG-3; and AGENT_ROUTING's own banner and "Default: Eligibility Review First" | Remove the ladder rules; keep as dated notes |
| B3 | TASK_TEMPLATE "Always assign to local Qwen (Copilot) first" | Fixed ladder | MAG-3 | Remove; replace with "see routing recommendation" |
| B4 | README model table (Qwen3.5-27B primary, 9B, Ryzen) vs ROUTING_LOGIC (qwen3.6 27b/35b primary, validated 2026-06-23) vs AGENT_ROUTING (qwen3.5, 3.6 "under review") | Three different model baselines | Stale and inconsistent | One dated agent notes file; delete model tables from the other docs |
| B5 | README, ROUTING_LOGIC, AGENT_ROUTING, GUARDRAILS | Routing is described in four places; ROUTING_LOGIC says "full table is in AGENT_ROUTING" | Overlap | One routing doc (the planning agent's decision aid) plus the agent notes file |
| B6 | GUARDRAILS Rules 0, 20, 21, 22 | Tool lists and limits written for Continue and Qwen3.5 | TASK_TEMPLATE: "Continue is installed but is not part of the active workflow"; Copilot-based Qwen has terminal access | Classify as ROUTING PREFERENCE / tool-surface notes; Rule 20's fabrication ban stays universal (it overlaps Rule 20a) |

## C. Synthesis gate
| # | Where | Says |
|---|---|---|
| C1 | README (EXECUTOR "mandatory"), ROUTING_LOGIC ("all executable tasks"), AGENT_ROUTING Hard Rules, TASK_TEMPLATE ("REQUIRED") | Synthesis report before any work, for every task |
| C2 | GUARDRAILS MAG-2 and MAG-4 | Synthesis is optional for bounded, low-risk work unless a rule, task file, project guidance, gate or risk profile requires it; Rule 17 stays in force |
Resolution: GUARDRAILS already encodes the optional-for-low-risk position, and it is the controlling source. The open work is aligning four docs to MAG-2/MAG-4, plus Tracy confirming that is what he wants.

## D. Conflicts with the new planning workflow and the light default
| # | Where | Says | Conflict |
|---|---|---|---|
| D1 | README "Planning-Agent-Only Workflow" and PLANNING Agent Role | Planning agent is a premium agent; does not read target files, run commands, or analyze code | New design: local Qwen planning agent reads actual files and runs git checks first |
| D2 | GUARDRAILS MAG-1 | Implementation agent reads the selected task file before any work | Light default allows direct work with Tracy, no task file. Needs one line: work Tracy directs in-session is authorized under MAG-2; no task file required |
| D3 | TASK_TEMPLATE | Dispatch Interface is "NOT optional scaffolding" for every task file | Same light default; also completed tasks keep a live Step 0 block that tells an agent to move the file into active/ |
| D4 | GUARDRAILS Rule 12 vs TASK_TEMPLATE | Rule 12: backlog does NOT use date folders; TASK_TEMPLATE: place in backlog/{YYYY-MM}/ | Internal conflict |
| D5 | Rule 13a (9 lines), README (2-4 lines), README handoff section (~10 lines) | Three different handoff limits | Pick one |

## E. Galaxy-specific or machine-specific content in generic files
- Hard-coded /Users/tam0013/Documents/git/agent-tasks paths: README (throughout), TASK_TEMPLATE, GUARDRAILS Rules 12 and 13a. Break on the tracymccormick profile.
- Rule 1 working directory /home/galaxy_game, container name `web`; Rule 12 backlog subfolder list; Rule 15 financial constants; Rule 17 (BaseUnit); Rule 18 (integration specs quarantine) look Galaxy-specific.
- README "Symlinked Task Repo" section describes the docs/new_agent symlink, which Tracy says exists only in the Galaxy repo.
- README "Documentation Updates Needed (June 2026)" lists documents to create (NEW_PLANNING_AGENT_GUIDE.md, WORKFLOW_TRANSITION_PROTOCOLS.md) that look abandoned.

## Missing from the plan's section 6 summary (per Qwen's chat report)
Qwen said the synthesis gate, Agent Dispatch Interface, 27B/35B hierarchy and Copilot budget are "not in GUARDRAILS.md" and dropped them. They are in the files above and need classifying.
