# Operational Guardrails
**Last Updated**: 2026-06-14
**Maintained By**: Session Strategist (Claude)

> Read before every task. These rules are non-negotiable.
> If a task file conflicts with these rules, STOP and flag before proceeding.

---

## Core Execution Rules

### Rule 1 — Docker
All Rails and RSpec commands MUST run inside Docker using:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec [command]'
```
Never use `docker compose exec` — use `docker exec -it web`.
Never run Rails or RSpec on the host system directly.

> **Working Directory**: The `web` container working directory is already
> `/home/galaxy_game`. Do NOT prefix commands with `cd /home/galaxy_game &&`.
> Adding it is redundant and clutters commands. Omit it entirely.

**Correct:**
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/shortage_detector_spec.rb 2>&1 | tail -20'
```

**Wrong:**
```bash
docker exec -it web bash -c 'cd /home/galaxy_game && unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/shortage_detector_spec.rb 2>&1 | tail -20'
```

### Rule 2 — Database Migrations
Always generate migration files using the Rails generator inside Docker.
Never create migration files manually.

```bash
docker exec -it web bash -c 'rails generate migration MigrationName column:type column:type'
```

**Why**: Manually created migration files bypass Rails timestamp tracking and cause
`ActiveRecord::Migration.maintain_test_schema!` failures in the test environment.
The generator produces a correctly sequenced timestamp automatically.

After generating, run in both environments:
```bash
docker exec -it web bash -c 'rails db:migrate && RAILS_ENV=test rails db:migrate'
```

If a migration is needed mid-task, this is a **Stop Condition** (see Rule 19) —
stop, generate the migration, confirm it runs cleanly, then continue.

---

### Rule 3 — RSpec Execution: AGENTS MUST NEVER RUN FULL SUITES
**CRITICAL**: Agents execute ONLY targeted specs for the feature/service being implemented.
Never run the full RSpec suite (`bundle exec rspec` with no file argument) during feature work.

**Why**: Full suite runs consume significant buffer, can overflow output, and are the human's responsibility for overnight/batch runs.

**What agents CAN run** (targeted only):
- `rspec spec/services/logistics/shortage_detector_spec.rb` (single service)
- `rspec spec/services/logistics/` (service directory)
- `rspec spec/models/logistics/import_request_spec.rb:42` (specific line/test)

**What agents CANNOT run**:
- `rspec` (full suite — FORBIDDEN)
- `rspec > /tmp/full.log` (full suite to file — FORBIDDEN)
- Background RSpec processes (FORBIDDEN)
- `rspec --exclude-pattern` spanning multiple services (FORBIDDEN — too broad)

**If the human wants a full run**: They will ask explicitly and provide exact instructions.
**Log location**: `/home/galaxy_game/log/rspec_full_*.log` — use for reading latest results, never generate new ones.

If you encounter a need for full suite validation, STOP and escalate to human.

---

### Rule 3a — MANDATORY PRE-EXECUTION CHECK (CRITICAL INCIDENT PREVENTION)
**Before running ANY RSpec command, you MUST explicitly perform this check and state the result:**

```
✅ PRE-RSPEC EXECUTION CHECK:
- Command: [EXACT command you are about to run]
- Scope: [single file | directory | specific line]
- Targeted: YES | NO
- File path: [path to spec file]
- NOT a full suite: YES | NO
- Ready to execute: YES | NO
```

**If ANY answer is NO or unclear, STOP and ask the human before proceeding.**

**Example of REQUIRED output BEFORE executing the command:**
```
✅ PRE-RSPEC EXECUTION CHECK:
- Command: docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec spec/services/logistics/shortage_detector_spec.rb'
- Scope: single file
- Targeted: YES
- File path: spec/services/logistics/shortage_detector_spec.rb
- NOT a full suite: YES
- Ready to execute: YES
```

**Critical Incident Prevention Rule**: If you are about to run a command that looks like a full suite (no file argument, `--exclude-pattern`, background process `&`, output redirect to file), STOP immediately. State what you almost did, why it would violate Rule 3, and ask the human for permission before proceeding. Do not assume you have permission.

---

### Rule 7 — RSpec Output
- All failure messages verbatim
- Full stack traces
- Summary line (X examples, Y failures, Z pending)
Never summarize or truncate RSpec output unless explicitly told to.
For large suites redirect to a log file:
```bash
docker exec -it web bash -c 'unset DATABASE_URL && RAILS_ENV=test bundle exec rspec [spec] 2>&1 | tee /tmp/rspec_output.log && tail -30 /tmp/rspec_output.log'
```

### Rule 10 — Host vs Docker Paths
The host path and Docker path are different:
- **Host**: `~/Documents/git/galaxyGame/galaxy_game/app/models/...`
- **Docker**: `/home/galaxy_game/app/models/...` (working directory root)
Always use the correct path context for commands and file references.
Task files use Docker paths for commands, host paths for git operations.
Do NOT include `/home/galaxy_game` prefix in docker exec commands — it is the
container working directory and is already active.

---

## Documentation Rules

### Rule 11 — Atomic Documentation
`TASK_OVERVIEW.md` must be updated BEFORE any task is marked In Progress or Complete.

### Rule 12 — Task File Lifecycle
Every task follows this exact path:
```
backlog/ → active/ → (work happens) → completed/
```
Use `git mv` to move task files between folders — never copy and delete.
A task in active/ with no completion report is abandoned — flag it.
Never leave a task in active/ after work is done.

> **Known Agent Issue**: Qwen3.5-27B frequently creates a copy in the destination
> folder instead of using `git mv`, leaving a duplicate in the source folder.
> After every session that moves task files, human must run `git status` and
> `git rm -f` any stale duplicate.

### Rule 13 — Handoff Commands
Reserve strategic context for handoff commands.
Keep active implementation discussion focused on the current sub-task only.
When switching tasks, produce a new handoff command — do not carry context forward.

### Rule 14 — Code Payload Protocol
All agents must output a raw code block for any file changes.
Precede every block with `[CODE_PAYLOAD: path/to/file]`:
```
[CODE_PAYLOAD: app/models/units/habitat.rb]
```ruby
module Units
  class Habitat < BaseUnit
    ...
  end
end
```
This ensures work can be recovered if the file-write tool fails.

---

## Economic Rules

### Rule 15 — Financial Constants
All financial calculations must strictly adhere to DECISIONS.md:
- 1:1 USD/GCC peg — no conversion logic
- SCC Surcharge: 0.5%
- Broker Fee: 0.3%
- Sales Tax: 3.37%
- Lunar loss rate: 15% on iron and silicate sourcing

Never hardcode these values in Ruby — read from game constants.

---

## Safety Rules

### Rule 16 — No Parallel RSpec Runners
Only ONE implementation agent runs RSpec at a time.
Task creation, documentation, and research can run in parallel.
Implementation cannot.

### Rule 17 — Synthesis Before Implementation
Any task touching BaseUnit, shared concerns, or services used across
multiple models MUST have a Synthesis Report approved before any code changes.
No exceptions.

### Rule 18 — Integration Specs Are Quarantined
Do not assign work on integration specs until unit/service layer is clean.
Integration specs that fail are noted but not touched.
They will self-resolve as unit specs green up.

### Rule 19 — Stop Conditions
Stop immediately and report to human if:
- A currently passing spec breaks after your change
- Same failure persists after two fix attempts
- Root cause is in a shared concern or base class
- A database migration is needed
- Any architectural decision is required
- Fix requires changing more files than the task specifies

### Rule 20 — Local Model Fabrication Prohibition
**Applies to all local models running via Continue.**

Local models CANNOT execute terminal commands, Docker, RSpec, or git.
Local models CAN read files and create/edit files via Continue.

If a task requires command execution:
- Ask the human to run the command and paste the output
- Never fabricate what the output would look like
- Never mix real file data with invented command results

If asked to analyze test failures:
- Only report failures that were provided as input
- Never invent failure lists from directory listings
- State clearly: "I cannot run tests. Please provide the test output."

Fabricated output that looks real is more dangerous than obvious failure.
A model that says "I can't do this" is always preferable to one that invents results.

### Rule 21 — Qwen3.5 Triage Phase Requirements
**Applies to Continue-based Qwen3.5 models during task triage.**

When Qwen3.5 is triaging task files, it MUST:
1. Add frontmatter fields: `status`, `priority`, `type`, `system_domain`, `mvp_alignment`, `local_worker_safe`
2. Add "Local Worker Triage Report" section with:
   - Template Conformance: PASS/FAIL
   - Docker Wrapper Check: PASS/FAIL/N/A
   - MVP Alignment: VALID/STALE/OBSOLETE
   - MVP Impact Note: one-line connection to goal
   - Action Line: READY FOR CLOUD HANDOFF | NEEDS MANUAL REVIEW | OBSOLETE
3. Verify Agent Assignment is present with "Why This Agent" rationale
4. Add implementation steps if not present or enhance if incomplete
5. Add code examples/PORO patterns where applicable
6. Cross-reference DECISIONS.md and GUARDRAILS.md

**Triage Phase Output Quality**: Task file should be 100% ready for cloud agent handoff with no additional human specification needed.

### Rule 22 — Continue Model Scope Limits
**Applies to all Continue agents (any model/size).**

Continue models have these fixed constraints:
- ✅ CAN read task files, model definitions, service code, specs
- ✅ CAN understand Rails patterns and Ruby syntax
- ✅ CAN generate code examples and PORO patterns
- ✅ CAN verify template conformance structurally
- ❌ CANNOT execute terminal commands or shell scripts
- ❌ CANNOT run Docker, RSpec, or rails commands
- ❌ CANNOT execute git commands
- ❌ CANNOT verify code actually works
- ❌ CANNOT access the live database
- ❌ CANNOT see which tests are currently failing

When a Continue model hits these limits:
- ✅ CORRECT: "I cannot run this test. Please paste the RSpec output and I'll analyze it."
- ✅ CORRECT: "This code requires database verification. Please check the schema."
- ❌ WRONG: Inventing what the test output would be
- ❌ WRONG: Assuming database schema based on Rails conventions

### Rule 23 — Token Conservation (Core Strategy)
**Applies to all agents in the planning/triage/implementation chain.**

Token conservation is THE core constraint. Local agents are always preferred.
Cloud agents are fallback only — never first choice.

**Escalation ladder (always follow in order):**

1. **Local Tier (Always Use First — 0 cost)**
   - Qwen3.5-9B (Copilot) — planning, triage, task file updates, doc edits
   - Qwen3.5-27B (Copilot, fresh session) — implementation, spec fixes, targeted edits
   - If tool execution failure in a running session → open a NEW local session first
     before escalating to cloud. Long sessions cause instability, not capability gaps.

2. **Cloud Fallback Tier (0.33x — only after two local failures)**
   - Claude Haiku 4.5 / GPT-5 mini / Raptor mini (GitHub Copilot)
   - Use for focused, well-specified tasks only
   - Never send full codebase — snippets only
   - Two local failures means two genuine attempts in fresh sessions,
     not two retries in the same session

3. **Premium Tier (Reserve — use sparingly)**
   - Claude Sonnet (this web session) — architecture decisions, complex diagnosis,
     session strategy, task file drafting, handoff document creation
   - Target: end of month premium quota still available
   - If a task can be completed locally, using premium is a workflow bug

**Golden Rule**: Local first. Cloud second. Premium last.
Spare quota at month end can be used for accelerated implementation sprints.

### Rule 24 — Perplexity Workflow Integration
**Applies to task validation phase before cloud agent handoff.**

Perplexity's role in the workflow:

1. **Task Clarity Validation**: "Is this task clear enough for the assigned agent?"
   - Review Qwen3.5 output for ambiguities
   - Flag if acceptance criteria are testable
   - Verify docker commands are correct and follow Rule 1

2. **Routing Verification**: "Is this task routed to the right agent?"
   - Confirm task complexity level matches agent tier
   - Check if parallelization is safe
   - Suggest re-routing if needed

3. **Workflow Management**: "Can we run these tasks in parallel?"
   - Review task dependencies
   - Identify blocking relationships
   - Optimize execution order

**Perplexity does NOT replace task creation** — it validates Qwen3.5 output before handoff.

### Rule 25 — No File Recreation From Scratch
**Applies to all agents.**

Agents must edit files in place. Recreating service files, spec files, or factories
from scratch (via Python heredoc, `cat >`, or any full-file overwrite method) is
prohibited unless explicitly authorized by the human.

**Why**: Recreation discards valid existing content and introduces new bugs.
Incremental in-place edits are always safer and reviewable.

If an agent cannot fix a file incrementally and wants to recreate it:
- STOP
- Report the specific blocker to the human
- Wait for explicit authorization before proceeding

The human may choose to provide a replacement file directly rather than
authorizing the agent to recreate it.
