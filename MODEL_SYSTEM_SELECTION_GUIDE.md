# Model & System Selection Guide — Galaxy Game Workflow
**Last Updated**: 2026-06-06  
**Purpose**: Decision framework for choosing between M4 Mac (Qwen3.5-27B) and Ryzen 7 system based on task complexity, token requirements, and error patterns encountered during testing

---

## Quick Reference: When to Use Which System

| Task Type | Recommended System/Model | Why |
|---|---|---|
| **Standard implementation tasks** (Luna Phase work, single service integration) | M4 Mac + Qwen3.5-27B | Fast inference (~100ms/token), sufficient context window for 2-5 file edits with targeted specs |
| **Planning Agent duties at session start** (review status.md, triage backlog, draft handoffs) | M4 Mac + Qwen3.5-27B | Quick iteration needed; task files are ~100-300 lines each — well within token limits |
| **Session Strategist mode during active implementation** (monitor Executor progress via terminal logs) | M4 Mac + Qwen3.5-27B | Real-time feedback loop requires fast responses; targeted spec output is small (<8K tokens typically) |
| **Complex multi-file synthesis reports before editing 10+ files** | Ryzen 7 System + Llama 3.1 / Mixtral with extended context window | Can read entire service layer (5-10 files, ~2K lines each) without truncation; better pattern recognition across codebase sections |
| **Full RSpec suite output triage after overnight run** (~4K examples, full log file >8K tokens) | Ryzen 7 System + larger model with extended context window | M4 hits "Response too long" errors when ingesting entire failure logs; Ryzen can analyze all failures in single pass without chunking |
| **Research-heavy tasks requiring external docs (GitHub issues, architectural RFCs) + codebase** | Ryzen 7 System + Llama 3.1 / Mixtral | Larger context window handles reference material + target files simultaneously; less need to make multiple tool calls for partial reads |
| **Quick config/JSON edits (<50 lines)** | M4 Mac + Qwen3.5-27B (stay on same model) or fallback to 9B only if RAM constrained | Smaller models save RAM but add token overhead when switching — not worth it unless running parallel sessions simultaneously |

---

## Error Patterns That Signal System Switch Needed

### Pattern 1: "Response too long" Errors in VS Code Extension
**Symptom**: 
```
Sorry, your request failed. Please try again.
Copilot Request id: <uuid>
Reason: Response too long.: Error: Response too long. at Yj._provideLanguageModelResponse (/Users/tam0013/.vscode/extensions/github.copilot-chat-0.48.1/dist/extension.js:1709:13707)
```

**Root Cause**: VS Code's Copilot Chat extension has ~8K token output limit. Context accumulates across model switches within same session, hitting limits mid-task when reading large files or terminal logs.

**Fix Options (in order)**:
1. **Restart VS Code session** — clears accumulated context; try task again on M4 with Qwen3.5-27B
2. **Chunk the work** — split multi-file reads into smaller batches (<8K tokens per read); use `read_file` tool for specific line ranges instead of full files
3. **Switch to Ryzen system via SSH session** — larger context window models handle entire task without truncation

### Pattern 2: Token Truncation Mid-Analysis (Silent Failure)
**Symptom**: Agent stops reading file mid-way, misses critical sections; synthesis report incomplete or references missing code paths that exist in files.

**Root Cause**: Model's input token limit exceeded during large multi-file reads (>15K tokens total). Qwen3.5-27B on M4 has ~8K context window for inputs + outputs combined.

**Fix Options (in order)**:
1. **Use targeted file reads with line ranges** — `read_file` tool supports startLine/endLine parameters; read only relevant sections instead of entire files
2. **Switch to Ryzen system** — models like Llama 3.1 or Mixtral support larger context windows (up to 64K-128K tokens depending on model)

### Pattern 3: Model Switching Causes Context Loss Between Tasks
**Symptom**: Agent forgets earlier conversation details when switching from Qwen3.5-9B → Qwen3.5-27B mid-session; needs to re-read files already processed in previous turn.

**Root Cause**: VS Code extension doesn't preserve full context across model switches for local Ollama endpoints; each new model starts with truncated conversation history.

**Fix Options (in order)**:
1. **Stay on single model per session** — use Qwen3.5-27B as primary workhorse without switching models mid-session
2. **Document key context in status.md before task transitions** — write down critical findings so next agent can re-establish context from file instead of relying on conversation history

---

## Dual-System Workflow Patterns

### Pattern A: M4 Primary, Ryzen for Heavy Analysis (Recommended)
```bash
# 90% workflow stays on M4 with Qwen3.5-27B in VS Code session
ollama run qwen3.5:27b

# When hitting token limits or needing larger context window:
ssh user@ryzen-system-ip
cd /path/to/galaxyGame  # if synced via git clone or rsync
ollama run llama3.1:8b  # or mixtral, depending on available models and RAM

# After heavy analysis complete, return to M4 for implementation work
exit ssh session
```

**When this works best**: 
- Standard Luna Phase tasks (2-5 file edits) stay on fast M4 system
- Complex architectural reviews spanning multiple services move to Ryzen temporarily
- End-of-session full RSpec suite triage uses Ryzen's larger context window

### Pattern B: Parallel Sessions for Independent Tasks
```bash
# Session 1 — M4 Mac, Qwen3.5-27B (active implementation work)
ollama run qwen3.5:27b

# Session 2 — Ryzen system via SSH or separate VS Code window on that machine
ssh user@ryzen-system-ip
cd /path/to/galaxyGame
ollama run llama3.1:8b  # for research-heavy task running in parallel
```

**When this works best**: 
- Two independent tasks can proceed simultaneously (e.g., implementing Luna Phase 2 while researching architectural decisions for Phase 4)
- RAM constraints on M4 prevent running multiple large models at once; Ryzen handles second session without impacting primary workflow speed

---

## Model Selection Decision Tree

```
Task starts → Is this a standard implementation task (<5 files, targeted specs)? 
    ↓ YES → Use M4 Mac + Qwen3.5-27B (fast inference)
    ↓ NO → Does task require reading >10K tokens of context?
        ↓ YES → Switch to Ryzen system with larger context window model
        ↓ NO → Is this end-of-session full RSpec suite triage (>8K token log)?
            ↓ YES → Use Ryzen system for analysis, then M4 for implementation work based on findings
            ↓ NO → Stay on M4 + Qwen3.5-27B (default)

During task execution: Hit "Response too long" error?
    ↓ First occurrence → Restart VS Code session to clear accumulated context; retry with same model
    ↓ Second occurrence in same session → Switch to Ryzen system via SSH or separate window
```

---

## Testing Experience Summary (June 2026)

### What Worked Well:
- **Qwen3.5-27B on M4**: Handles Luna Phase tasks perfectly; fast enough for interactive work (~100ms/token); sufficient reasoning capability for Ruby/Rails code generation and spec writing
- **Single-model sessions**: Avoids token limit errors entirely when staying with one model per VS Code session

### What Didn't Work:
- **Multiple local models in same session** (Qwen3.5-9B → Qwen3.5-27B switches): Causes "Response too long" errors due to context accumulation; not worth the RAM savings for this project's complexity level
- **Small models (<10B parameters)**: Codestral 4B, Qwen2.5-3B — insufficient reasoning capability for complex service integrations; better suited for simple config edits only

### What Needs Testing:
- **Ryzen system with Llama 3.1 / Mixtral**: Not yet tested in production workflow; theoretical benefit is larger context window but inference speed unknown on that hardware configuration
- **Cloud fallback (GPT-5-mini)**: Only use after two local failures — token billing applies June 2026+

---

## Documentation Updates Needed

After testing Ryzen system with actual tasks, update this document with:
1. Real-world performance metrics (tokens/second on Ryzen vs M4)
2. Specific model recommendations based on available RAM and task complexity thresholds
3. SSH setup instructions for seamless workflow between systems if needed
