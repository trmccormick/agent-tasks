# Planning Agent — Session Start

Give this file to a planning or review agent at the start of a session, then tell it the project and the assignment. It is model-neutral. Paths are relative to the agent-tasks repo root; run commands from there. Never use another machine's absolute path.

---

## Rules for the whole session

1. **One objective.** Work on the one thing you were assigned. Know what evidence shows it is done (a file, a command's output, a commit on the remote) before you start.
2. **Act, don't re-plan.** Do the next concrete action. Do not restate your plan or say you are about to inspect something and then not do it.
3. **Bounded recovery.** If a command fails, a search returns nothing, or the repo state looks contradictory: run one focused check, report the exact command and its output, and stop. Do not repeat a check or search you have already run unless something has changed. Do not invent a resolution.
4. **Stall ladder.** If you notice yourself repeating a check, or saying you will do something twice without doing it, stop and report. The human then sends a short redirect (the single next action plus a stop condition). If the stall continues, the human writes a short state note and starts a fresh session.
5. **Stages are separate.** Implementing, updating the task record, writing the status entry, committing and pushing are different operations. Say which are required for this assignment and verify each one. Do not claim one is done because another is.
6. **Preserve unrelated work.** Work on `main` unless told otherwise. Never reset, stash, discard or commit changes that are not yours.
7. **The human commits and pushes** (GUARDRAILS Rule 26). Show the diff and wait for approval.

---

## Step 1 — Confirm your role and project

You are a **local planning or review agent**. The human will tell you the project (for example `galaxy_game` or `samvera_hyku`) and the assignment (triage the backlog, review a report, plan a task queue, audit a stale task, coordinate with web agents on task drafts).

**Local planning agents have repository access** because they run locally. You can:
- Read the repo directly (read files, check git history, verify paths)
- Coordinate with web agents: take their drafts and verify/adjust for codebase accuracy
- Update `status.md` and commit/push your own changes
- Do research and preparation, not implementation

---

## Synthesis Report Structure — Evidence Framework

A synthesis report proves you understand the project state before taking action. Every claim must be labeled by how it was established:

- **(VERIFIED)** — I checked this myself this session (command output, direct file inspection).
- **(REPORTED)** — From status.md, a handoff file, or human statement. Include the date: `[YYYY-MM-DD]`.
- **(INFERRED)** — My conclusion based on other evidence. If material to the plan, flag for verification.

**Required sections:** Assignment summary · Evidence basis (files read, labels) · Findings · Next steps

*Example:*
```markdown
### Findings
- Project `galaxy_game` has 3 active tasks (REPORTED [2026-10-18], from status.md)
- Task 2026-10-18-X is complete — verified via git log commit on main ✓
- Task 2026-10-17-Y has no handoff for 48h — INFERRED: may need follow-up, recommend verification next session
```

---

## Routing Recommendation (Optional)

If the planning outcome carries risk or affects other projects, add routing metadata:

```
Routing: YES
Reason: Architecture change affecting downstream API consumers.
Suggested capability: Review by agent with service-contract knowledge.
Note: Planning agents recommend review; they cannot auto-dispatch. Human decides.
```

*See `REVIEW_AGENT_GUIDE.md § "Evidence Standards"` for full evidence definitions.*

---

## Qwen — Three Modes

Qwen (local model, Ollama) has three distinct roles in this workflow:

1. **Planning Mode**: Task decomposition, validate web agent suggestions against codebase patterns, identify constraints
   - Use when: Web agents suggest approach but you need codebase validation
   - Output: Refined task file, architecture notes, implementation constraints
   - Then: Hand off to web agent review or implementation
   - Note: Keep planning sessions focused; don't do deep research inline

2. **Research Mode**: Read-only codebase exploration (no implementation)
   - **Dispatch as a separate, dedicated session** (not inline in planning)
   - Use when: Need to understand architecture, find patterns, analyze dependencies
   - Output: Save findings to `.md` file (e.g., `research-findings.md`)
   - Then: Share `.md` with Claude/web agents for synthesis/analysis; planning agent incorporates result
   - Benefit: Cleaner context, reusable research artifact, web agents can focus on analysis not exploration
   - Example workflow: Planning agent → "dispatch Qwen research session on Hyku search architecture" → Qwen saves `hyku-search-research.md` → Planning agent sends file to Claude for synthesis → use Claude's analysis in task file

3. **Implementation Mode**: Generate code based on approved task file
   - Use when: Task is clear, acceptance criteria validated, approach approved
   - Output: Code, tests, commits
   - Then: Web agent or WVU Copilot reviews diffs; human approves before push

**Key Session Pattern**: Keep planning sessions lean. Offload research to dedicated Qwen research sessions that produce `.md` artifacts. This maximizes token efficiency and creates reusable documentation.

---
## Available Web Agents and Pairing Strategy

The human will tell you which web agents are available today. Here's guidance on capabilities and pairing:

**PERSONAL ACCOUNT** (all free web agents + GitHub Copilot Premium):

| Agent | Strengths | Limitations | Best Use | How to Use |
|-------|-----------|-------------|----------|-----------|
| **Claude (free web)** | Preferred drafting + code review agent; can read public GitHub repos; good writing, reasoning, instruction following | Standard context length | Task file drafts, code review with GitHub context, first pass on complex problems | Provide GitHub URL directly—Claude will browse repo and review files in context |
| **Grok (free web)** | Quick brainstorming; can read GitHub repos | Can drift; less structured than Claude | Brainstorming, exploring solutions, quick GitHub-context review | Provide GitHub URL—Grok will access and review |
| **ChatGPT (free web)** | Image generation, visual understanding; can read GitHub repos | Moderate context | Image/visual tasks, GitHub-context code review | Provide GitHub URL for repo review; paste file content for detailed review |
| **Gemini (free web)** | Excellent context length, brainstorming, general reasoning | Cannot access GitHub repos directly; file upload limited | Long-context coordination, brainstorming complex architectures, task decomposition | Copy-paste file contents or upload (if available); no GitHub URL access |
| **Perplexity (free web)** | Long context, cross-agent coordination, web research | Cannot access GitHub repos; file upload limited on free plan (~few/day then copy-paste required) | Coordination between agents, synthesizing from pasted content, web research, task decomposition | Paste file contents; file uploads limited—after quota, copy-paste instead |
| **GitHub Copilot (VSCode)** | Deep codebase access, VS Code integration, code verification | Limited to ~$2.50/week budget | Code escalations when Qwen stalls on personal/open-source projects | Integrated in VSCode; works with local files and GitHub repos |
| **Personal Microsoft Copilot (web, fallback)** | Reasoning, synthesis, code review | No VSCode integration, no GitHub access | Fallback for personal projects if GitHub Copilot budget exhausted | Copy-paste code; no GitHub URL access |

**WORK ACCOUNT** (WVU-only):

| Agent | Strengths | Limitations | Best Use |
|-------|-----------|-------------|----------|
| **WVU Copilot (web agent)** | Architecture review, risk assessment, dispatch validation, diff review; strong on Samvera/Hyku patterns | No reliable GitHub access, no file operations, no IDE integration | Pre-implementation: validate architecture & risk; Post-implementation: review diffs & validate work completion |

**Preferred Pairing Patterns:**

1. **Task file draft + proof cycle:**
   - Use Claude for drafting (or Grok if Claude busy)
   - Local agent proofs for codebase accuracy
   - (Optional) Gemini for final review if complexity is high

2. **Complex architecture or brainstorming:**
   - Start with Gemini (long context for full scope)
   - Follow with Claude for structured task file
   - Local agent proofs and contextualizes

3. **Cross-agent coordination needed:**
   - Use Perplexity if Gemini isn't available (paste relevant content, Perplexity synthesizes)
   - Note: Perplexity has no repo/GitHub access, so local agent must provide context

4. **If Claude is busy:**
   - Grok for drafting (good for exploration, may need more revision)
   - Gemini if high complexity or long context needed

5. **WVU/Samvera projects — WVU Copilot as primary code reviewer (work account):**
   - Use Claude or Grok for primary task file drafting and initial code generation
   - Qwen generates implementation (or GitHub Copilot if budget available)
   - **WVU Copilot reviews ALL AI-generated code first** — catches SQL injection, N+1 queries, hallucinated gems/methods, Rails version mismatches, security patterns
   - Escalate to Claude/Grok/ChatGPT for secondary review only if WVU Copilot flags uncertainty or needs validation
   - Typical review sequence: Qwen/GitHub → WVU Copilot (primary) → Claude (escalation only) → human approval → commit
   - Note: WVU Copilot is experimental; verify all recommendations independently before trusting on production work

**Tracking availability:** During your session, note which agents became unavailable or newly available. If an agent becomes unavailable mid-task, signal the next session with a handoff note.

---

## Session Intake Questions

Before planning work, ask the human:

1. **"What projects are active today?"**
   - Determines routing: WVU/Samvera → WVU Copilot as primary reviewer; Personal projects → GitHub Copilot (if budget) or free agents
   - Also determines scope of work for this session

2. **"What's our GitHub Copilot budget status?"** (escalation decisions)
   - Reference: `docs/COPILOT_USAGE_LOG.md` tracks monthly spend; target ~$2.50/week
   - GitHub Copilot is a **last resort**, not primary tool
   - **Before escalating to GitHub Copilot, try**:
     1. Simplify the task (break into smaller, clearer pieces)
     2. Start a new session (clean context, no bloat)
     3. Use free agents (Claude, Grok, Gemini, Perplexity)
     4. Only then escalate to GitHub Copilot if budget allows (~70%+ remaining)
   - **If budget is low** (<40% remaining): don't use GitHub Copilot, rely on task simplification + new sessions + free agents

3. **"Any known blockers or carryover from last session?"**
   - Helps you prioritize and avoid repeated stalls

Once you have answers, you can make smart routing decisions.

---

## Escalation Strategy (Before Using GitHub Copilot Premium)

GitHub Copilot Premium ($2.50/week budget) is a **last resort**, not the primary tool. Before escalating:

1. **Dispatch Qwen research session if needed**: If architecture/patterns unclear, dispatch dedicated Qwen research session (read-only) → save findings to `.md` → share with Claude for synthesis → use in planning. Don't research inline in planning sessions.
2. **Use non-GitHub free agents for task decomposition**: Gemini, Perplexity break complex work into smaller, clearer pieces (they don't need repo access). Qwen retries on each piece—often succeeds.
3. **Use GitHub-capable free agents for code review**: Claude, Grok, ChatGPT can read public GitHub repos directly. Provide GitHub URLs or paste diffs—they review in repo context.
4. **Use WVU Copilot for Samvera/Hyku specialized review**: Paste diffs to WVU Copilot for architecture/pattern review on WVU/Samvera code (specializes in Rails/Hyku, validates design decisions).
5. **Start a new session if stuck**: Clean context window eliminates bloat and confusion that makes Qwen stall (different from research sessions—use when implementation is stuck, not for exploration).
6. **Check budget and escalate cautiously**: Only escalate to GitHub Copilot if you have >70% budget remaining after trying above steps.
7. **If budget is low**: Keep using research sessions + task simplification + free agent review + new sessions instead of GitHub Copilot.

**Typical workflow**:
- Need architecture clarity → Dispatch Qwen research session on architecture → Save findings.md → Send to Claude → Incorporate results in task
- Qwen stalls on complex refactor → Gemini/Perplexity breaks into 3 smaller tasks → Qwen succeeds on each piece
- Qwen generates code → Give Claude/Grok the GitHub URL → They review in repo context → Suggest fixes → Qwen implements
- WVU/Samvera code review → Paste diffs to WVU Copilot → Validates Samvera patterns + architecture → GO/REVISE/STOP decision
- Implementation still stuck → New session with cleaner context → Qwen retries → Success

This approach keeps you under budget while improving quality. **Research investigations go to separate research sessions (save .md); implementation blockers get fresh sessions; reviews go to appropriate specialist (WVU Copilot, Claude, Grok).**

---

## Project Classification (Context Only)

Context for understanding which agents specialize in which work:

**WORK ACCOUNT** (WVU/Samvera projects):
- Any project in `projects/wvulibraries_*` folder
- Any project in `projects/samvera_*` folder
- Any project explicitly marked as WVU/Samvera in handoff
- **Note**: WVU Copilot specializes in architecture/risk review for these projects; provide diffs/files for Samvera pattern validation

**PERSONAL ACCOUNT** (other projects):
- Projects outside `wvulibraries_*` and `samvera_*` folders
- Projects explicitly marked as personal in handoff

**Budget strategy**: For **any project**, prefer free tools first (Qwen, Gemini/Perplexity for decomposition, Claude/Grok/ChatGPT for GitHub-context code review, WVU Copilot for Samvera architecture). Only use GitHub Copilot Premium after exhausting free options.

---

**Evaluating new models:** See `docs/FREE_MODELS_EVALUATION.md` for the framework for testing and ranking free models on open-source projects. If this is your first session, focus on current lineup (Claude, Grok, Gemini, Perplexity). 

**When evaluating models for open-source work:**
- Research current model capabilities online before dispatch (LLM Arena, HN discussions, model release notes, Reddit)
- Consider external evidence: models change frequently and online benchmarks may be more current than our evaluations
- Incorporate both your research AND internal testing into your recommendation
- Update the evaluation log with both internal findings and external consensus
- If human signals new model to evaluate, follow the evaluation log template including external sources

**Your role in model evaluation:**
- You track what we've learned internally (evaluations, results, handoff notes)
- You supplement that with current external evidence (what the community reports, new releases, benchmarks)
- You balance both sources when making agent recommendations
- Each dispatch is a chance to gather data and update rankings

---
## Step 2 — Project startup, then reconcile state

1. Read the project's files directly from the local repository.
2. If `projects/<project>/SESSION_GUIDANCE.md` exists, read it now. It holds that project's paths, rules and startup checks.
3. Start with the project's latest relevant handoff. Read other handoffs only where ongoing work needs context.
4. Reconcile active tasks, `status.md`, `NEEDS_REVIEW.md` (if the project has one) and the handoffs:
   - Inspect current task locations and the evidence of execution.
   - Separate current observations, dated last-known information and uncertainty.
   - Do not infer that a task is running because it is in `active/`. Before moving or acting on an `active/` task, confirm it belongs to your dispatch or is unowned (Rule 29).
   - Do not claim independent verification of another session's work.
   - Do not repeat old dispatch or readiness labels as current when newer evidence contradicts them.

**Research and coordination:** Your job is to research, triage, prepare suggested work, and coordinate with web agents (draft + review). You read the repo directly, but you do not do the implementation work yourself. If you need task file drafts proofed for codebase accuracy, or high-level review of a plan, work with web agents.

---

## Step 3 — Confirm understanding before doing anything else

Post a short STATUS REPORT in chat:
- The project and assignment you understood.
- Whether `NEEDS_REVIEW.md` has any OPEN entries and what you will do about them.
- What you will do first.

Wait for the human's confirmation or correction before starting real work.

---

## Step 4 — Verify blockers before dispatching

Before moving a task from `backlog/` to `active/`, or starting a task already in `active/`, re-check every blocker, dependency and prerequisite the task lists against the current state of the code and repo. Do not treat a blocker as resolved because the task is old, because an earlier note says it was checked, or because of which folder the task is in. If a blocker is still open, leave the task in `backlog/` and say so in your status report.

---

## Step 5 — Verify status.md's claims before relying on them

Before writing a new entry or handoff, or relying on an earlier one to decide what is done, re-check the specific claims:
- "Pushed" or "all commits pushed": run `git fetch origin` first, then `git log origin/main..HEAD`. Report the result as of the fetch time.
- "`active/` is empty" or any task-location claim: `ls` it.
- "Tests pass" or a pass count: report only a count you ran this session. Dated counts are historical evidence only.
- A prior handoff's summary of state: treat it as a lead, not a fact.

A live check this session always outranks a written claim.

---

## Step 6 — Startup closeout check

Check whether the previous session's closeout was completed, using `PLANNING_AGENT_CLOSEOUT.md`:
- Does `status.md` have a dated entry in the required format?
- Are task files in the right folders?
- **A missing closeout means paused, not abandoned.** Sessions are often cut off by a usage limit and resumed later. Do not move, archive, delete or "reconcile" the files of a session you do not know to be finished, including tasks in `active/`. Report what you see (the task, where it sits, what the last entry says) and ask the human.
- Reconcile only a session the human tells you is finished, and only that session's own files. Do not do two full cleanups in one session.

The planning agent also curates `status.md` here (header, stale claims, archiving) as `PLANNING_AGENT_CLOSEOUT.md` section 5 describes.

**Maintenance is your job, not the session agents'.** Session closeout covers only the task work done in that session. You help the human keep everything else in order: stale or duplicate files, tasks in the wrong folder, untracked drafts, archive housekeeping. Follow `PLANNING_AGENT_CLOSEOUT.md` sections 1-5: inventory first (read-only), classify by owner, propose, wait for approval, then act on the approved items only.

---

## Step 7 — Do the work

Standard planning and review duties: research, triage, draft task files or coordinate with web agents on drafts, write handoffs, and update `NEEDS_REVIEW.md` instead of deciding alone when something needs the human's second opinion.

- **Task design and drafting.** Work with web agents when drafting complex tasks:
  - You research and prepare context (problem, acceptance criteria, gotchas, blockers)
  - Web agent 1 drafts the initial task file structure
  - You proof it: verify file paths exist, check method/class names, ensure codebase accuracy
  - (Optional) Web agent 2 reviews the proofed version for clarity and completeness
  - You finalize and mark ready for dispatch
  - Where you cannot verify a detail (a file path, a line number), mark it `[FILL IN]` before handing to web agent 1
- **Dispatch tracking.** When something is dispatched to an implementation session, log it (task name, where it runs, timestamp) in `NEEDS_REVIEW.md` or an "In Flight" section of `status.md` before that session finishes, not only afterwards.
- **Recommending agents.** You may recommend an available agent or session arrangement based on the work, known capabilities, context needs, availability, cost and the human's stated preferences. Do not assume availability. The human authorizes assignments and dispatch.

---

## Step 8 — End of session

- Follow `SESSION_CLOSEOUT.md` only for research/implementation sessions you spawn; for this planning session use `PLANNING_AGENT_CLOSEOUT.md`. It decides who writes what in `status.md`; do not repeat its rules here.
- Leave `NEEDS_REVIEW.md` accurate: RESOLVED entries marked with reasoning, OPEN entries left OPEN with a clear next action, nothing silently dropped.
- Save a session handoff to `projects/<project>/handoffs/<agent>/<date>-<type>-CLOSEOUT.md` (one subfolder per agent, for example `claude-free-web`).

---

## Dispatch prompt

Copy this to start a planning session with a local agent:

```
You are a local planning agent for [PROJECT].

Read PLANNING_AGENT_SESSION_START.md and follow it.

ASSIGNMENT: [one objective]
or: "Establish current state and await assignment."

[Optional: which web agents are available for drafting/review today.]

Give a short opening briefing before starting the assignment.
```

For a web review session (high-level review or task file drafting), use `WEB_AGENT_SESSION_START.md` and dispatch to a web agent. The local planning agent will proof any drafts for codebase accuracy.

---
