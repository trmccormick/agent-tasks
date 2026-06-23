# Agent Routing
**Last Updated**: 2026-06-19
**Maintained By**: Claude (web) + human
**Token Strategy**: Local-first execution. Cloud agents are 0.33x — reserve for tasks local models genuinely cannot complete after two attempts.

> Read DECISIONS.md before this file.
> Routing decisions here are based on actual model capabilities AND token cost.

---

## 🔴 NEW: Cross-Project GitHub Copilot Budget Strategy (June 2026)

**Plan**: GitHub Copilot $10/month (Claude Haiku 4.5)

**Budget Constraint**: Keep premium usage ≤15%/week across ALL projects (target 60%/month max)

**Why**: Leaves 25-40% buffer end-of-month for concentrated heavy agent tasks. Prevents premium burn in first 2 weeks.

**Routing Pattern** (applies to all projects):
- **Strategist** (premium): Task planning, context synthesis, risk assessment only
- **Implementation** (local agents): All code changes, testing, debugging, git operations
- **Result**: Premium stays lean for steady-state work, reserves capacity for heavy tasks

**How This Works**:
- Task files are detailed → agents work independently without back-and-forth
- Handoffs are 2-4 lines max → no expansion into long exploratory sessions
- Implementation agents (OllamaExecutor, Explore, qwen models) run locally → zero cloud cost
- Heavy tasks batched for end-of-month → absorb remaining budget in concentrated sprints

**Validation** (as of June 19):
- wvulibraries_authentication: 28% used through day 19 (~10%/week actual burn) ✅
- Pattern holds: 72% remaining runway = enough for Seq 4-8 + month-end heavy task

---

## ⚠️ NEW: qwen 3.6 Models on Ryzen 7 (As of June 18, 2026)

**Hardware Updated**: Ryzen 7 upgraded earlier this week with qwen 3.6 models installed:
- **qwen 3.6:25b** — Now available for testing
- **qwen 3.6:35b** — Now available for testing
- Previous: qwen3.5:35b-a3b (validated working, performance baseline)

**Status**: ⚠️ **ROUTING UNDER REVIEW** — Models installed but not yet validated in live sessions

**Next Steps**:
1. Run Seq 4-5 tasks to evaluate qwen 3.6 performance vs 3.5:35b
2. Document if 3.6:25b is faster/better than 3.5:27b for routine tasks
3. Track if 3.6:35b improves on previous 3.5:35b baseline (complexity, speed, accuracy)
4. Update routing table once validation complete

**Current Assumption**: qwen3.5:35b-a3b remains primary heavy executor until 3.6 validated.

---

## ⚠️ Tier Change — Effective 2026-06-03

**GPT-5 mini and Raptor mini are no longer 0x. Both are now 0.33x tier.**

- All routing tables in this file have been updated to reflect this
- Do not route routine implementation to cloud agents
- Qwen3.5-27B is now the primary implementation worker
- Cloud agents require two local failure attempts before escalation
- Track all cloud usage in `COPILOT_USAGE_LOG.md`

---

## The Cluster

### Strategic Dispatch — Free
| Agent | Cost | Role | When to Use |
|---|---|---|---|
| **Claude (web)** | Free | **PRIMARY STRATEGIST** — review, task file drafting, handoff prompts, synthesis audit | Session starts, task file creation, architecture review |
| **Gemini (web)** | Free | Domain expertise, game balance, macro planning | Geological/game balance questions, design intent checks |
| Perplexity | Free | External research, task clarity validation | After task file is drafted — "is this deployable?" |

---

### Local Execution Agents — Free (PRIMARY IMPLEMENTATION PATH)
| Model | Node | Best For | Terminal Access |
|---|---|---|---|
| **Qwen3.5-27B** | M4 | **PRIMARY EXECUTOR** — multi-file fixes, schema verification, complex logic | ✅ `run_in_terminal` |
| **Qwen3.5-9B** | M4 / Windows | Targeted file reads, single spec runs, simple known fixes | ✅ `run_in_terminal` |
| Codestral | M4 | Complex offline reasoning, multi-file structural planning | ❌ Read only |
| Qwen2.5-3B | Windows | Small text edits, JSON configs, quick doc updates | ❌ Read only |
| Qwen3-Coder 30B | Windows | Heavy implementation, primary Windows worker | ✅ `run_in_terminal` |
| Nomic Embed | Windows | RAG/codebase indexing — always on, never reassign | — |
| Qwen2.5-Coder 1.5B | Windows | Tab autocomplete — always on, never reassign | — |

---

### Cloud Agents — 0.33x Tier (RESERVED)
| Agent | Cost | When to Use |
|---|---|---|
| **GPT-5 mini** | 0.33x | Local models failed twice AND task is urgent AND budget available |
| **Raptor mini (Preview)** | 0.33x | Alternative when GPT-5 mini unavailable — same criteria |

**Before escalating to cloud**: document which local model was tried, what it produced, and why it failed. Log in `COPILOT_USAGE_LOG.md`.

---

## Critical Local Model Limitations

> These limitations apply to ALL local models via Continue.
> Violating these rules produces fabricated output that looks real.

### What local models CAN do
- Create and edit files when exact content is provided
- Read files that Continue can access on the filesystem
- Apply specific code changes with clear before/after
- Run terminal commands via `run_in_terminal` (Qwen3.5 series only)
- Run targeted RSpec via `docker exec web bundle exec rspec spec/...`

### What local models CANNOT do
- Run git commands inside Docker (git runs on host only)
- Access the internet or external APIs
- Know which tests are currently failing without being told
- Analyze real runtime behavior without actual output provided
- Reliably summarize file contents for Claude review — drop files directly instead

### The Fabrication Rule — CRITICAL
**Local models MUST NOT report output from commands they did not actually run.**

If asked to analyze test failures without actual test output provided:
- ✅ CORRECT: "I can see these spec files exist but I cannot report which are failing. Please run the tests and paste the output."
- ❌ WRONG: Inventing a list of failing tests based on file names seen in the directory

If asked to run a command the model cannot execute:
- ✅ CORRECT: "I cannot execute this command. Please run it on the host and paste the output."
- ❌ WRONG: Fabricating what the output would look like

**The Hallucination Gate**: Qwen3.5-9B in particular will hallucinate root causes when lacking file context. Never approve a synthesis report based on Qwen's description of a file without reading it yourself. Drop files directly to Claude (web) for review.

---

## ⚖️ Cross-Auditing & Verification Protocol

1. **The Code-to-Doc Audit**: When a local agent changes code, Claude (web) must audit the output against master design intent — ensure no drift into generic 4X/turn-based tropes.
2. **The Doc-to-Code Audit**: When a design rule is updated, the local execution agent must run targeted terminal checks to verify the codebase physically reflects the documentation.
3. **The Contradiction Ban**: Agents must cross-reference active output against `projects/galaxy_game/README.md`. If a generated pattern contradicts the guide, halt and flag for the human.
4. **Synthesis Report Gate**: Executor produces synthesis report and stops. No changes applied until human explicitly approves.

---

## Routing Table

### Session Start
| Task | Agent | Cost |
|---|---|---|
| Review files, audit architecture | Claude (web) | Free |
| Draft task files from findings | Claude (web) | Free |
| Write handoff prompts | Claude (web) | Free |
| Game balance / domain question | Gemini (web) | Free |
| Validate task clarity before handoff | Perplexity | Free |

### Triage & Investigation
| Task | Agent | Cost |
|---|---|---|
| Targeted file read — confirm contents | Qwen3.5-9B or drop to Claude | Free |
| Triage RSpec failures — cause unknown | Qwen3.5-27B (terminal) | Free |
| Schema verification before fix | Qwen3.5-27B | Free |
| Codebase-wide grep/search | Qwen3.5-9B (local) | Free |
| External docs / API research | Perplexity | Free |

### Implementation
| Task | Agent | Cost |
|---|---|---|
| Single file fix — cause known | Qwen3.5-9B | Free |
| Single file fix — cause unknown | Qwen3.5-27B | Free |
| Multi-file fix with schema verification | Qwen3.5-27B | Free |
| Complex multi-file refactor | Qwen3.5-27B (try first) | Free |
| Heavy implementation — 27B failed twice | GPT-5 mini or Raptor mini | **0.33x** |
| Database migration scaffolding — local failed | GPT-5 mini or Raptor mini | **0.33x** |

### Data & JSON
| Task | Agent | Cost |
|---|---|---|
| JSON file edits — small targeted | Qwen2.5-3B | Free |
| JSON file audits — validation | Qwen3.5-27B | Free |

### Documentation & Task Files
| Task | Agent | Cost |
|---|---|---|
| Draft new task files | Claude (web) | Free |
| Update task files post-implementation | Qwen3.5-9B or Qwen2.5-3B | Free |
| Update status.md | Qwen3.5-9B | Free |
| Write architecture docs | Claude (web) or Gemini | Free |
| Session handoff document | Claude (web) | Free |

### Repository Operations
| Task | Agent | Cost |
|---|---|---|
| git add / commit / push | Human on host only | Free |
| File moves between task folders | Human on host (or supervised agent) | Free |
| docker exec rspec runs | Qwen3.5-9B or 27B via terminal | Free |

---

## Decision Rules

### Default: Always Try Local First
1. Qwen3.5-27B for anything requiring reasoning or multi-file work
2. Qwen3.5-9B for single file reads and targeted spec runs
3. Only escalate to cloud after two genuine local failures

### When to Escalate to Cloud (0.33x)
All three conditions must be true:
- Local model failed or produced clearly wrong output twice
- Task is blocking progress and cannot wait
- Copilot budget is not in red alert (check `COPILOT_BUDGET_MANAGEMENT.md`)

Document the escalation in `COPILOT_USAGE_LOG.md` before proceeding.

### When NOT to Use Cloud Agents
- Routine RSpec fixes → Qwen3.5-27B
- Single file edits with exact specs → Qwen3.5-9B
- Task file moves and status updates → Qwen3.5-9B
- JSON config edits → Qwen2.5-3B
- Any task where local has not been tried first

### When NOT to Use Local Models
- Tasks requiring reading files not available on host filesystem
- Cross-session memory required — use Claude (web) with files dropped in
- Full architecture audit across many files — drop files to Claude (web)

---

## Hard Rules

- **Local First**: Always attempt with local models. Cloud is reserved, not default.
- **No Continue Sidebar for Automation**: Codestral lacks terminal tools. All terminal automation via GitHub Copilot custom agent commands.
- **The Verified Execution Rule**: Qwen3.5 series MUST use `run_in_terminal` to verify files and run Docker specs — never fabricate or assume test results.
- **Role Isolation**: Claude (web) plans and drafts. Executor acts on task blueprints only — never self-assigns.
- **Synthesis Report Gate**: Executor stops after synthesis report. No changes until human approves.
- **No full suite runs**: Targeted specs only — never run the full RSpec suite from an agent.
- **One RSpec runner at a time**: Never run parallel spec execution inside Docker.
- **Supervised Host-Only Git**: All version control on the host Mac only. Never attempt Git inside Docker.
- **No JSON Commits**: Raw JSON data files never staged or committed. Code, specs, and markdown only.
- **No Cross-Project Contamination**: Keep context isolated to the active project repo.
- **Codebase scans go local**: Never send the full codebase to cloud agents — snippets only.

---

## Communication Protocol
All agents must output a raw code block for any file changes.
Precede every block with `[CODE_PAYLOAD: path/to/file]` to ensure
work can be recovered if the file-write tool fails.
