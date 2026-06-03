# Agent Routing
**Last Updated**: 2026-05-31
**Maintained By**: Session Strategist (Haiku)
**Token Strategy**: Maximize free tier usage. Premium reserved for complex work only. **Pivot to Continue local agents** for mechanical work.

✅ **CONFIRMED FREE TIER**: GPT-5 mini and Raptor mini (preview) are confirmed 0x. **PRIMARY STRATEGY: Use Continue local agents** (Qwen, Codestral, DeepSeek) for RSpec failures, mechanical fixes, and implementation work.

> Read DECISIONS.md before this file.
> Routing decisions here are based on actual model capabilities AND token cost.

---

## The Cluster

### Planning Gate — 0 Token Cost
| Agent | Cost | Role | When to Use |
|---|---|---|---|
| **Gemini** | 0 tokens | PRIMARY planner | All session starts, priority stacking |
| Claude (free web) | ~0 tokens | Overview/alignment checks | Secondary if Gemini unavailable |

---

### Triage & Detailing Phase — 0 Token Cost
| Model | Node | Provider | Cost | Role |
|---|---|---|---|---|
| **Qwen3.5 27B** | M4 | Continue | 0 tokens | Heavy auditing, complex multi-file reasoning |
| **Qwen3.5 9B** | M4 / Windows | Continue | 0 tokens | Template conformance, implementation detail |

---

### RSpec Failure Debugging (Flexible Maintenance Routing)
**Principle**: RSpec failures are ad-hoc maintenance, NOT formal tasks. Route based on agent availability.

| Failure Type | Route To | Notes |
|---|---|---|
| Single service failures (3-5 failures) | Whoever is available first | Start immediately; don't wait for specific agent |
| Factory/setup issues (nil associations) | Local Qwen3.5 (if available) else Claude | Quick file inspection + fix |
| Complex cross-service issues (5+ related) | Codestral synthesis (try first) | Escalate to Claude Sonnet only if pattern unclear |
| Fixture/data load failures | GPT-5 mini or available local | Mechanical fix; assign based on availability |

**Example (2026-05-30)**: 19 failures after overnight run → Claude (free) available at 1:40pm → routed immediately (no task file needed)

**Flexibility rule**: If another agent becomes available while one is working, evaluate if work can be parallelized. RSpec failures often have independent root causes.

---

### Task Management & Validation — 0 Token Cost
| Agent | Cost | Role | When to Use |
|---|---|---|---|
| **Perplexity** | 0 tokens | Review task clarity, validate deployment, manage workflow | After Qwen3.5 triage, before cloud handoff |

**Perplexity role**: Can validate that Qwen3.5 output is clear and deployable. Can manage task queues and deployment order. Good for: "Is this task clear enough for GPT-5 mini?" or "Can this be parallelized safely?"

---

### Cloud Implementation Agents — Potential Premium Usage (MONITOR)
| Agent | Current Cost | Capability | Status | When to Use |
|---|---|---|---|---|
| **GPT-5 mini** | 0x | Mechanical implementation | STABLE — confirmed free tier | Primary cloud agent |
| **Raptor mini (preview)** | 0x | Mechanical implementation, parallel | STABLE — confirmed free tier | Parallel execution fallback |
| **Haiku 4.5** | 0.33x | Fast fixes, spec corrections | STABLE | Reserve for quick decisions, not routine work |
| **Claude Sonnet** | 1x PREMIUM | Complex reasoning, architecture | STABLE | **RESERVE** for truly complex multi-file work |

**WARNING**: Do not route mechanical RSpec fixes to Copilot agents until billing structure is confirmed.

---

## Strategy Shift: Prioritize Continue Local Agents

**Effective immediately** (June 2, 2026):
- ✅ **Primary**: Route all mechanical RSpec fixes to Continue agents (Qwen3.5-9B, Codestral, Qwen2.5-14B)
- ✅ **Primary**: Multi-file refactors to Codestral (local synthesis)
- ⚠️ **Fallback**: Copilot agents only if local agents unavailable
- ✅ **Complex reasoning**: Continue to use Claude Sonnet (stable 1x cost)

**Rationale**: 
- GPT-5 mini and Raptor mini confirmed as free tier (0x)
- Continue local agents are guaranteed free and always available
- Mechanical work (RSpec fixes, edits) routes naturally to local agents
- Reduces financial risk during pricing transition

---

### Local Execution Agents — 0 Token Cost (PRIMARY FOR MECHANICAL WORK)
| Model | Node | IP | Best For |
|---|---|---|---|
| Codestral | M4 | 10.6.186.161 | Architecture reasoning, synthesis (⚠️ filesystem issues in galaxyGame setup) |
| Qwen3.5-9B | M4 | 10.6.186.161 | **Primary for local files in galaxyGame** (more reliable filesystem access) |
| Qwen2.5-Coder 14B | M4 | 10.6.186.161 | Multi-file implementation with context |
| DeepSeek-Coder 16B | M4 | 10.6.186.161 | Logic verification, second opinion |
| Qwen3-Coder 30B | Windows | 10.6.186.50 | Heavy implementation, primary local worker |
| Qwen2.5-Coder 3B | Windows | 10.6.186.50 | Fast single-file edits |
| Llama 3.1 8B | Windows | 10.6.186.50 | Fallback chat only — not for implementation |
| Nomic Embed | Windows | 10.6.186.50 | RAG/codebase indexing — always on, never reassign |
| Qwen2.5-Coder 1.5B | Windows | 10.6.186.50 | Tab autocomplete — always on, never reassign |

---

## Critical Local Model Limitations

> These limitations apply to ALL local models via Continue.
> Violating these rules produces fabricated output that looks real.

### What local models CAN do
- Create and edit files when exact content is provided
- Read files that Continue can access on the filesystem
- Apply specific code changes with clear before/after
- List directory contents via Continue file tools

### What local models CANNOT do
- Execute terminal commands (no shell access)
- Run Docker commands or RSpec
- Run git commands
- Access the internet or external APIs
- Know which tests are currently failing without being told
- Analyze real runtime behavior without actual output provided

### The Fabrication Rule — CRITICAL
**Local models MUST NOT report output from commands they did not actually run.**

If asked to analyze test failures without actual test output provided:
- ✅ CORRECT: "I can see these spec files exist but I cannot report which are failing. Please run the tests and paste the output."
- ❌ WRONG: Inventing a list of failing tests based on file names seen in the directory

If asked to run a command the model cannot execute:
- ✅ CORRECT: "I cannot execute this command. Please run it on the host and paste the output."
- ❌ WRONG: Fabricating what the output would look like

**Mixing real file data with fabricated command output is the most dangerous failure mode.**
A document that is 30% real makes the 70% fabrication look credible.

### RAG Status Warning
Local model codebase search is only reliable when Nomic Embed has actively indexed
the codebase. If RAG status is unknown, use a cloud agent for any codebase search.

---

## Routing Table

### Planning Gate (Session Start) — 0 Token Cost
| Task | Agent | Cost | Reason |
|---|---|---|---|
| Session triage and priority stack | Gemini | 0 | PRIMARY gatekeeper |
| Produce initial task recommendations | Gemini | 0 | Understand MVP alignment |
| Route tasks for Qwen3.5 triage vs direct handoff | Gemini | 0 | Quality assessment |
| High-level alignment check (if needed) | Claude free web | ~0 | Fallback to Gemini |

### Qwen3.5 Triage Phase (All Tasks) — 0 Token Cost
| Task | Agent | Cost | Reason |
|---|---|---|---|
| Read backlog task files and assess | Qwen3.5 27B (M4) | 0 | Heavy reasoning, multi-file |
| Verify template conformance | Qwen3.5 9B (M4 or Windows) | 0 | Structural analysis |
| Add implementation detail, code examples | Qwen3.5 (either size) | 0 | Code pattern understanding |
| Identify MVP alignment | Qwen3.5 27B (M4) | 0 | Requires reasoning |
| Produce triage report | Qwen3.5 (either size) | 0 | Structured output |
| Route to cloud agent | Qwen3.5 27B (M4) | 0 | Complex decision-making |

### Task Management & Validation — 0 Token Cost (NEW)
| Task | Agent | Cost | Reason |
|---|---|---|---|
| Validate task clarity after Qwen3.5 triage | Perplexity | 0 | "Is this deployable?" check |
| Review task queue for parallelization | Perplexity | 0 | Workflow management |
| Check if task routing is correct | Perplexity | 0 | Deployment optimization |
| Ensure acceptance criteria are testable | Perplexity | 0 | QA before cloud agent |

### Investigation & Synthesis (After Validation) — Minimal Premium
| Task | Agent | Cost | Reason |
|---|---|---|---|
| Cross-file reasoning (8+ files) | Claude Sonnet | 1x | **RESERVE** — only if truly needed |
| Logic audit before implementation | Codestral (try first) | 0 | If fails, escalate to Claude 1x |
| Codebase search | Qwen3-Coder 30B + Nomic Embed | 0 | RAG-assisted local search |
| Identify root cause from error | Codestral (try first) | 0 | If fails, escalate to Claude 1x |

### Implementation (Minimal Cost)
| Task | Agent | Cost | Reason |
|---|---|---|---|
| Single file edit — exact specs from Qwen3.5 | GPT-5 mini 0x | 0 | Mechanical, well-prepared task |
| Single file edit — needs some inference | Haiku 0.33x | 0.33x | Slightly more complex |
| Multi-file refactor — patterns specified | GPT-5 mini 0x | 0 | Mechanical with guidance |
| Multi-file refactor — needs reasoning | Codestral synthesis + GPT-5 mini impl | 0 | Never skip local synthesis |
| Create missing fixture or config | GPT-5 mini 0x | 0 | Mechanical |
| Factory trait fix | GPT-5 mini 0x | 0 | Mechanical |
| Add logger call to rescue block | GPT-5 mini 0x | 0 | Mechanical |
| Architecture refactor (PREMIUM ONLY) | Codestral synthesis + Claude 1x impl | 1x | Use sparingly — almost never needed |

### Data & JSON (0 Token Cost)
| Task | Agent | Cost | Reason |
|---|---|---|---|
| JSON file edits — small targeted | Qwen2.5-Coder 3B (Windows) | 0 | Fast, low risk |
| JSON file audits — validation | Qwen3.5 27B (M4) | 0 | Needs judgment |
| Large JSON generation | GPT-5 mini 0x | 0 | Free, handles volume |

### Documentation (0 or Free Token Cost)
| Task | Agent | Cost | Reason |
|---|---|---|---|
| Update .md files after code change | Qwen2.5-Coder 3B or GPT-5 mini | 0 | Mechanical |
| Write new architecture docs | Gemini or free Claude | 0 | Prefer Gemini (planning gate) |
| Session handoff document | Haiku 0.33x or GPT-5 mini 0x | 0-0.33x | Prefer GPT-5 mini (free) |
| Update task files (post-implementation) | GPT-5 mini 0x | 0 | Mechanical update |

### Repository Operations (No Agent Cost)
| Task | Agent | Cost | Reason |
|---|---|---|---|
| Codebase-wide grep/search | Local M4 or Windows via Continue | 0 | Never send full codebase to cloud |
| git add / commit / push | Human on host only | 0 | No agent commits without human review |
| File moves and renames | Human on host | 0 | Simple terminal commands |

---

## Decision Rules — Token Conservation Strategy

### The 0 Token Tier (Default)
- Gemini for all planning/triage
- Qwen3.5 (Continue) for all task detailing
- Perplexity for task validation and management
- Local models for implementation synthesis
- GPT-5 mini 0x for mechanical implementation

**Rule**: Do NOT use premium tokens unless work has exhausted all 0-token options.

### When to Use Perplexity (0 Tokens)
- After Qwen3.5 triage: "Is this task clear and deployable?"
- Before cloud handoff: validate task routing correctness
- Queue management: which tasks can run in parallel safely?
- QA check: are acceptance criteria testable?

**Perplexity strength**: Excellent at pattern-matching task clarity without premium cost

### When to Use Claude Free Web (~0 Tokens)
- Fallback to Gemini if unavailable
- High-level alignment checks
- Non-critical architecture review
- Do NOT use for task creation or implementation

### When NOT to Use Premium Tokens
- Single file edits with exact specs → GPT-5 mini 0x
- Template enforcement → Qwen3.5 (Continue)
- Task queue management → Perplexity
- Codebase search → Local models + RAG
- Mechanical implementations → GPT-5 mini 0x

### When to Use Premium Tokens (1x Claude / 0.33x Haiku)
- Multi-file refactor requiring judgment across shared services
- New architectural pattern requiring design review
- Complex BaseUnit or concern changes
- **Almost never** for mechanical work

**Golden rule**: If Codestral (local) can do a synthesis, never use Claude 1x for that task.

### When NOT to Use Local Models (Route to Cloud)
- Tasks requiring knowledge of project history (use Perplexity or Claude)
- Anything requiring actual command execution
- Database schema verification needed
- Cross-session memory required

---

## GitHub Copilot & Perplexity Integration

### GitHub Copilot (June 1, 2026)
- **Policy changes incoming** — read carefully when released
- Current: Treat as research/non-critical only to conserve tokens
- After June 1: Update this section with new routing rules
- Do NOT use for implementation until policy clarified

### Perplexity (NEW)
- Free tier, 0 tokens per query
- Excellent for: "Is this task clear?" and workflow validation
- Deployed after Qwen3.5 triage, before cloud agent handoff
- Strong at pattern-matching, routing decisions, QA

**Example workflow**:
```
Gemini: Plan session → 0 tokens
Qwen3.5: Detail task files → 0 tokens
Perplexity: Validate clarity + routing → 0 tokens
GPT-4.1: Implement → 0 tokens
Result: Full task cycle with 0 premium tokens spent
```

---

## June 2026 Changes
Update this file when GitHub Copilot policy changes take effect on June 1, 2026.
Current plan: clarify Copilot token allocation and update routing table accordingly.

---

## Communication Protocol
All agents must output a raw code block for any file changes.
Precede every block with `[CODE_PAYLOAD: path/to/file]` to ensure
work can be recovered if the file-write tool fails.
