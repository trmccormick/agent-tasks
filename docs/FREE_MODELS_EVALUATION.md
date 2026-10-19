# Free Models Evaluation Framework

**Purpose:** Document and evaluate free web and local models for our workflow, ranking by performance and effectiveness on our type of work (agent coordination, task file drafting, codebase analysis, documentation, open source projects).

**For Planning Agents:** This is a live tracking document. Before dispatching an open-source project task, research the candidate model(s) online (LLM Arena, Reddit/HN discussions, model release notes) and incorporate external evidence into your recommendation. Update this document with both internal evaluations AND external findings. Models evolve frequently—your online research informs what we should try next.

**Initial Focus:** Open source projects (Galaxy Game, Samvera projects, etc.) before WVU/Samvera-specific tools.

---

## Local Models (Already in Use)

| Model | Capabilities | Current Status | Notes |
|-------|--------------|-----------------|-------|
| **Qwen 3.6 35b** | Excellent reasoning, code understanding, instruction following | ✅ Primary local | Performing well; good baseline for comparisons |

---

## Free Web Models (Candidates for Integration)

### Tier 1: High Priority to Evaluate

| Model | Provider | Capabilities | Limitations | Best For | Evaluation Status |
|-------|----------|--------------|------------|----------|-------------------|
| **DeepSeek (free web)** | deepseek.com | Strong reasoning, code analysis, competitive with Claude on complex tasks | No file uploads, Chinese model (occasionally biased) | Complex reasoning, code analysis, second opinions, fallback for high-complexity tasks | 🔄 To evaluate |
| **Claude (free web)** | claude.ai | Writing, reasoning, instruction following (in current use) | Standard context length | Task file drafting, instruction refinement, first-pass reasoning | ✅ Current primary |
| **Grok (free web)** | x.com (Twitter) | Brainstorming, quick exploration, edgy perspectives | Less structured than Claude, can drift | Brainstorming solutions, exploring alternatives, quick feedback | ✅ In rotation |

### Tier 2: Secondary Candidates

| Model | Provider | Capabilities | Limitations | Best For | Evaluation Status |
|-------|----------|--------------|------------|----------|-------------------|
| **Mistral Chat (free)** | mistral.ai | Smaller but capable, fast responses, good reasoning | Smaller model; less context than Claude | Quick iteration, brainstorming, code snippets | 🔄 To evaluate |
| **Llama 3** | Meta (via Replicate, HuggingFace) | Strong open model, good reasoning and code | Availability varies by provider | Code analysis, reasoning, open-source focus | 🔄 To evaluate |
| **Gemini (free)** | google.com | Excellent context length, brainstorming | No repo access | Long-context coordination, brainstorming architectures | ✅ In rotation |
| **Perplexity (free)** | perplexity.ai | Long context, cross-agent coordination | No file uploads, no repo access | Coordination, synthesizing from pasted content | ✅ In rotation |

### Tier 3: Experimental / Niche

| Model | Provider | Capabilities | Limitations | Best For | Evaluation Status |
|-------|----------|--------------|------------|----------|-------------------|
| **Together AI (free tier)** | together.ai | Access to multiple models, limited free usage | Rate-limited | Experimentation with various OSS models | 🔄 To explore |
| **HuggingFace Spaces** | huggingface.co | Model playgrounds for experimentation | Varies by space | Ad-hoc testing, experimentation | 🔄 Available on demand |

---

## Evaluation Framework

### Dimensions to Track

For each model tested on open source projects, evaluate and score (1-5):

1. **Code Understanding** — How well does it understand our codebase context?
   - Recognizes Ruby/Rails patterns
   - Understands project structure from descriptions
   - Catches potential bugs or issues

2. **Reasoning Quality** — Depth and accuracy of problem-solving
   - Breaks down complex tasks
   - Identifies edge cases
   - Proposes alternatives

3. **Instruction Following** — Does it follow your task requirements precisely?
   - Follows formatting requirements
   - Handles constraints and constraints
   - Completes all acceptance criteria

4. **Writing Quality** — Clarity and usability of output
   - Task file quality
   - Documentation clarity
   - Instruction precision

5. **Speed** — Response time and iteration efficiency
   - Fast initial response
   - Quick to iterate on feedback
   - Suitable for urgent tasks

6. **Cost/Value** — How much value per token/dollar (for free: perception of value)
   - Works within free tier limits
   - Sufficient context for our tasks
   - Doesn't require excessive prompting

7. **Context Handling** — Can it maintain context across multiple turns?
   - Remembers earlier decisions
   - Tracks state correctly
   - Avoids contradictions

### External Evidence Sources

**Planning agents should research before dispatch to inform recommendations:**

| Source | What to Look For | Update Frequency |
|--------|------------------|------------------|
| **LLM Arena** (huggingface.co/spaces/lmsys/chatbot-arena) | Side-by-side comparisons, community voting, code understanding scores | Daily updates |
| **OpenRouter** (openrouter.ai) or **Replicate** | Model cards, community feedback, usage patterns, performance metrics | Weekly+ |
| **Model Release Notes** | New capabilities, context expansion, bug fixes, performance improvements | Per release |
| **Reddit** (r/LocalLLMs, r/MachineLearning) | Real-world experiences, gotchas, hidden capabilities, hidden limitations | Ongoing |
| **Hacker News** | Discussion threads on new model releases, performance analysis | Per release |
| **Twitter/Bluesky** | Quick takes from researchers, unexpected discoveries, usage patterns | Real-time |
| **Model Cards on Hugging Face** | Intended use, strengths/weaknesses, benchmarks, caveats | Per release |

### Scoring Legend
- **5** — Excellent; consistently strong; preferred choice
- **4** — Good; reliable; acceptable alternative
- **3** — Adequate; works but has gaps
- **2** — Limited; notable deficiencies
- **1** — Poor; not recommended for this task
- **N/A** — Not yet evaluated

---

## Evaluation Log

### Model: [Model Name]

**Date:** YYYY-MM-DD  
**Evaluator:** [Name/Agent]  
**Project:** [Project Name]  
**Task Type:** [e.g., task file drafting, code analysis, brainstorming]  
**External Research Done:** [Y/N] — If yes, list sources checked (LLM Arena, Reddit threads, release notes, etc.)

| Dimension | Score | Internal Notes | External Evidence |
|-----------|-------|-----------------|-------------------|
| Code Understanding | N/A | | |
| Reasoning Quality | N/A | | |
| Instruction Following | N/A | | |
| Writing Quality | N/A | | |
| Speed | N/A | | |
| Cost/Value | N/A | | |
| Context Handling | N/A | | |

**Overall Assessment:** [Summary of strengths and weaknesses based on internal testing]

**External Consensus:** [What do online sources say? Is it consistent with our findings?]

**Recommended For:** [Specific use cases where this model excels]

**Recommended Against:** [Use cases to avoid]

**Next Steps:** [What to test next, if applicable]

**Status:** [Ready to use / Needs more testing / Not recommended]

---

## Current Workflow Assignments

### Web Agents (Free)
1. **Claude** — Primary drafting, reasoning, task files
2. **Grok** — Brainstorming, quick iteration
3. **Gemini** — Long context, complex architectures
4. **Perplexity** — Cross-agent coordination

### Under Evaluation
- **DeepSeek** — Candidate for reasoning/code analysis pairing

### To Evaluate Soon
- **Mistral** — Quick iteration, code snippets
- **Llama 3** — Open-source focus evaluation

### Local
- **Qwen 3.6 35b** — Baseline for comparisons

---

## Next Session Checklist

- [ ] Evaluate DeepSeek on an open-source project task
  - Suggested: Try on Galaxy Game task (reasoning-heavy)
  - Compare output quality to Claude
  - Rate on code understanding and reasoning dimensions

- [ ] Test Mistral on quick iteration scenarios
  - Suggested: Brainstorming session
  - Compare speed to Grok

- [ ] Document Qwen 3.6 local performance baseline
  - Run on sample task
  - Create comparison reference

- [ ] Propose new agent rotations based on findings
  - Update PLANNING_AGENT_SESSION_START.md
  - Add top performers to pairing strategies

---

## Rankings (Updated as Evaluations Complete)

### By Dimension (Highest to Lowest)

**Code Understanding**
1. [To be filled]

**Reasoning Quality**
1. [To be filled]

**Writing Quality**
1. [To be filled]

**Speed**
1. [To be filled]

**Overall Recommended Stack (So Far)**
1. Claude — Primary
2. [To be filled]
3. [To be filled]
