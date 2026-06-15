# Routing Logic — Quick Reference
**Last Updated**: 2026-06-14

> Full routing table and agent details are in `rules/AGENT_ROUTING.md`.
> This file is a quick-reference summary only.

---

## ⚠️ EXPERIMENTAL TOOLS (Not in Active Routing)

- **Continue (VS Code Extension)**: Text-only terminal execution (no `run_terminal`), but CAN read files including git history.
  - DeepSeek-v2: Honest refusal ("unable to execute").
  - Codestral: Can access and read local files (including .git/ structures). Does NOT fabricate — output verified accurate. However, still cannot execute terminal commands.
  - **Limited use case**: Architecture review, file analysis, git history reading. **NOT recommended for active workflow** since Qwen3.5 in Copilot does everything Continue does PLUS terminal execution.

---

## The One Rule

**Local workspace agents handle file reads, active terminal triage, planning, and targeted edits.**
**Cloud agents handle mechanical execution, 0.33x tasks, or deep complex reviews.**
**Cloud agents never receive the full codebase — snippets only.**

---

## Session Roles (Behavior-Based, Not Model-Specific)

Roles are assigned per session. The same model can fill different roles in different sessions.

| Role | Typical Agent | What They Do |
|---|---|---|
| **STRATEGIST** | Qwen3.5-9B (Copilot) | Triage, plan, delegate, update task files — never execute code in this role |
| **EXECUTOR** | GPT-5 mini / Raptor mini / Claude Haiku (0.33x Cloud), Qwen3.5-9B / 27B (Local Live Work) | Implement assigned tasks only — never self-assign |
| **REVIEWER** | Claude (web) | Architecture decisions, complex task creation — spot checks only |
| **DOMAIN EXPERT** | Gemini (web) | Geological/game balance expertise — no local file access |
| **RESEARCHER** | Perplexity | External documentation lookup |
| **DOCUMENTATION AGENT** | Gemini (Web - Context/Intent) / Qwen3.5-27B (Local - Tech Structure) | Parse repositories, maintain markdown guides, and sync code realities with gameplay design rules |

---

## ⚖️ Cross-Auditing & Verification Protocol (Anti-Hallucination Gate)

To prevent structural drift and trope hallucinations, no agent output may be finalized without a cross-audit from a separate model family.

1. **The Code-to-Doc Audit**: When a local agent (e.g., Qwen) generates architectural maps or changes code, a context agent (e.g., Gemini) must audit the output against the master design intent to ensure it has not slipped into generic 4X/turn-based tropes.
2. **The Doc-to-Code Audit**: When a design rule is updated, the local execution agent must immediately run targeted terminal checks (e.g., running specific RSpecs with `unset DATABASE_URL`) to verify that the codebase physically reflects the documentation.
3. **The Contradiction Ban**: Agents are explicitly ordered to cross-reference their active output against `docs/new_agent/agent_guides/galaxy_game.md`. If a generated code pattern or task file assumes mechanics (like turn-clocks) that contradict the guide, the execution must be halted and flagged for the human.

---

## 📋 Task Assignment & Operational Handoff Rules (STRATEGIST Mandate)

When the STRATEGIST (Qwen3.5-9B) is updating task files, allocating work, or closing out a session, it must strictly adhere to these operational steps to prevent state drift:

### 1. The State-Tracking Triad
Every active development cycle requires updating three core files simultaneously to maintain project telemetry:
- **Task Files**: Must be built using `TASK_TEMPLATE.md` exactly, clearing out all old template suffixes. Every assignment must clearly define the scope, target specific RSpec files, and state explicit **Escalation Criteria** (e.g., *"If local tests fail due to unexpected environmental dependency collisions, halt execution and report back to the human—do not rewrite core network infrastructure"*).
- **`STATUS.md` Updates**: The STRATEGIST must update the global tracking log whenever a task shifts from active to completed or when a block is cleared by a verified RSpec pass.
- **`DECISIONS.md` Logging**: Any architectural pivot, database model adjustment, or game balancing deviation from the master `galaxy_game.md` context must be logged as a permanent entry in `DECISIONS.md` before work is handed off.

### 2. Executor Handoff Mechanics
When assigning a blueprint to an EXECUTOR model (whether cloud-based or local live work), the STRATEGIST must append a **Simple Handoff Template** to the active task file. This template must explicitly map:
1. **Priority Stacking**: Tasks must be ordered sequentially (Task 1, Task 2). Executors are strictly banned from working out-of-order.
2. **Context Injection**: Explicit instructions on which domain guides (e.g., `@galaxy_game.md`) the executor must pull into their session before writing code.
3. **The Sandbox Constraint**: A reminder that Git is unavailable in the container and raw JSON changes are prohibited.

### 3. Session End Handoffs
At the conclusion of an exploration or development session, the active strategist agent must synthesize a concise **Session Handoff Document** summarizing what was validated, what paths were checked via terminal, and what state the active task folder is being left in for the next active launch.

---

## Escalation Protocol — Local Failure Handling

**Two distinct failure types require different responses:**

### Type 1 — Tool Execution Failure
Symptoms: "Sorry, no response was returned", JSON leaking raw to output, tool calls not completing, no response after multiple retries.
Cause: Usually session state exhaustion from long-running context, not a capability gap.

**Escalation steps (in order):**
1. **Fresh local session first** — open a new Qwen3.5-27B session with the same task file. Tool failures in long sessions often resolve immediately in clean context. Do NOT retry in the same session.
2. If fresh local session also fails → escalate to 0.33x cloud agent (Haiku, GPT-5 mini, or Raptor mini).

### Type 2 — Task Complexity Failure
Symptoms: Wrong output, logic errors, spec failures, incorrect code, misunderstood requirements.
Cause: Task beyond local model capability or task file under-specified.

**Escalation steps (in order):**
1. **Tighten the task file** — add explicit before/after code examples, narrow scope, add stop conditions. Retry once in a fresh local session.
2. If second attempt fails → escalate to 0.33x cloud agent.

**Rule: Never retry a tool execution failure in the same session. Never escalate a complexity failure without first trying a tighter task file.**

---

## ⚠️ Known Agent Behavior Issues

### Qwen3.5-27B — git mv
Qwen does not reliably use `git mv` for task file moves. It creates a copy in the destination and leaves the original in place, resulting in duplicate task files. 

**Mitigation:**
- Task files must include explicit `git mv` instruction with full source and destination paths
- After every Qwen session that moves task files, human must run `git status` to verify no duplicates
- If duplicate found: `git rm -f` the stale copy, commit manually on host

### Qwen3.5-27B — Long session instability
Sessions running longer than one major implementation task accumulate context and become prone to tool execution failures. 

**Mitigation:**
- One task per Qwen session — do not chain implementation tasks in the same session
- If a session produced a large implementation (100+ lines), start fresh for the next task regardless of apparent stability

### All agents — spec file recreation
Agents under pressure (repeated failures, syntax errors) may attempt to recreate spec files from scratch via Python or heredoc rather than editing in place. This risks losing existing valid specs.

**Mitigation:**
- Task files must include explicit stop condition: "Do not recreate this file from scratch. Edit in place only."
- If agent attempts recreation, halt and escalate to human.

---

## AI Stack at a Glance

**⚠️ TESTED FINDINGS (2026-06-14):**
- **Copilot + Qwen3.5 (both 9b & 27b)**: ✅ Tools work correctly
- **Copilot + Qwen2.5 (14b & 7b)**: ❌ Tool format incompatibility (outputs JSON, Copilot doesn't execute)
- **Continue**: ❌ All tools disabled by design (text-only mode)
- **DeepSeek-v2:16b & Codestral**: Not selectable in Copilot (discovered but hidden from dropdown)
- **Claude Haiku 4.5 (GitHub Copilot)**: ✅ Confirmed working as 0.33x cloud fallback (June 2026)

| Agent | Cost | When to Use | Local File/Terminal Access |
|---|---|---|---|
| **Qwen3.5-9B (Copilot)** | Free/local | **Fast Local Strategist / Planner**, quick triage, terminal context gathering, task detailing | ✅ Yes (Tested & confirmed working) |
| **Qwen3.5-27B (Copilot)** | Free/local | **Heavy Local Executor**, deep logic validation, terminal verification runner, complex multi-file reasoning | ✅ Yes (Tested & confirmed working) |
| ~~Codestral (Continue)~~ | Free/local | ~~Complex offline reasoning~~ **NOT SELECTABLE IN COPILOT** | ❌ Text-only (Continue disabled tools) |
| ~~Qwen2.5 variants (Copilot)~~ | Free/local | ~~Code implementation~~ **DO NOT USE — TOOL FORMAT BROKEN** | ❌ Tool calls not executed (format mismatch) |
| **Claude Haiku 4.5 (Copilot)** | 0.33x Tier | **Cloud fallback executor** for focused fixes, syntax corrections, targeted spec work | ✅ Yes (IDE Workspace Only) |
| **GPT-5-mini (Copilot)** | 0.33x Tier | **Cloud fallback executor** for mechanical code & migrations | ✅ Yes (IDE Workspace Only) |
| **Raptor mini (Copilot)** | 0.33x Tier | **Cloud fallback executor** for mechanical code, quick logic blocks, and specs | ✅ Yes (IDE Workspace Only) |
| Claude (web) | Free tier | High-level conceptual reviews, session strategy, task file drafting | ❌ No local access |
| Gemini (web) | Free | Macro session planning, priority stacking, and deep domain game-balance advice | ❌ No |
| Perplexity (web) | Free | Research, workspace task deployment validation | ❌ No |

---

## Quick Routing Table

| Task | Agent | Role Context | Notes |
|---|---|---|---|
| **Triage overnight failures / build task files** | Qwen3.5-9B (Copilot) | **STRATEGIST** | Fast, proven tool support |
| **Verify workspace paths (Ledger check)** | Qwen3.5-9B (Copilot) | **STRATEGIST** | Terminal-verified only |
| **Execute live specs via terminal to check errors** | Qwen3.5-27B (Copilot) | **EXECUTOR** | ⚠️ ONLY Qwen3.5 works; Qwen2.5 format broken |
| **Syntax fix / single file edit — cause known** | Qwen3.5-9B or fresh 27B session | **EXECUTOR (Local)** | Fresh session if prior session ran long |
| **Tool execution failure in long session** | Fresh Qwen3.5-27B session first, then 0.33x cloud | **EXECUTOR** | Do NOT retry in same session |
| Macro session planning / priority stacking | Gemini (web) or Qwen3.5-9B | **STRATEGIST** | Avoid Qwen2.5 for planning |
| Validate task deployment clarity | Perplexity (web) | **RESEARCHER** | |
| Architecture decision / multi-file design | Qwen3.5-27B (Copilot) | **REVIEWER / EXECUTOR** | Primary local option |
| **Mechanical code implementation (0.33x)** | Claude Haiku / GPT-5-mini / Raptor mini (Cloud) | **EXECUTOR (Cloud)** | Only after two local failures |
| **Database migration scaffolding (0.33x)** | Claude Haiku / GPT-5-mini / Raptor mini (Cloud) | **EXECUTOR (Cloud)** | Only after two local failures |
| Fix a single spec — cause is known | Qwen3.5-9B | **EXECUTOR (Local)** | Verified working |
| Fix a single spec — cause unknown | Qwen3.5-27B (Terminal verification) | **EXECUTOR (Local)** | Deep reasoning + terminal tools |
| Multi-file refactor (Patterns specified) | Claude Haiku / GPT-5-mini / Raptor mini (Cloud) | **EXECUTOR (Cloud)** | Only after two local failures |
| Write docs / update task tracking | Qwen3.5-9B (Copilot) | **EXECUTOR (Local)** | Use Qwen3.5, not Qwen2.5 |
| Session strategy / task file drafting | Claude (web) | **REVIEWER** | Current session role |

---

## Hard Rules

- **⚠️ Qwen2.5 Tool Incompatibility (Copilot)**: Qwen2.5 models (7b, 14b, 32b) output tool-calls in a format that Copilot doesn't recognize/execute. They are NOT suitable for terminal automation in Copilot. Do NOT use in active routing.
- **Qwen3.5 Only for Copilot Terminal Tools**: Both Qwen3.5-9B and Qwen3.5-27B support terminal execution in Copilot. These are the ONLY local Copilot agents confirmed working with `run_in_terminal` tool.
- **Continue is Experimental/Deprecated**: Continue disables terminal execution (`run_terminal` tool) for all models but allows file reading. Cannot execute, so no advantage over Copilot + Qwen3.5 which does both reading AND execution. Keep for architecture analysis only, but do NOT use for active task routing since Qwen3.5 covers all use cases.
- **The Verified Execution Rule**: Local models utilizing the `run_in_terminal` tool (Qwen3.5 series in Copilot) MUST use it to verify files and run live tests instead of fabricating or assuming test results.
- **Role Isolation**: The **STRATEGIST** generates and coordinates the plan. The **EXECUTOR** acts on existing task blueprints and never self-assigns tasks.
- **One Task Per Local Session**: Do not chain implementation tasks in a single Qwen session. Long sessions cause tool execution instability. Start a fresh session for each task.
- **No In-Place File Recreation**: Agents must edit files in place. Recreating spec or service files from scratch (via Python, heredoc, or cat) is prohibited. If an agent attempts this, halt and escalate.
- **git mv Required for Task File Moves**: Agents must use `git mv` to move task files between folders. Creating a copy and leaving the original is a violation. Human must verify with `git status` after every session that moves task files.
- **Scrub Your Templates**: Ensure all generated task files strip out old `_2` template suffixes and use `TASK_TEMPLATE.md` exactly.
- All codebase-wide scans go to local models — never send the full codebase to cloud agents.
- One RSpec runner at a time — never run parallel spec execution inside Docker.
- **Supervised Host-Only Version Control**: Because Git is not installed or accessible inside the Docker container environment, all version control operations must be executed directly on the host node (Intel Mac). Agents may stage and commit application code, specs, and markdown documentation under direct human supervision on the host, but they are strictly prohibited from attempting Git commands inside Docker.
- **Data Commit Restriction**: Staging or committing raw JSON data files is strictly banned at this time; version control tracking is reserved exclusively for source code, tests, and documentation.
