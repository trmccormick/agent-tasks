# Model Selection Guide for Planning Agents

**Purpose**: Decision framework for choosing which model to use for each work category, based on remaining premium budget and task type.

**Target Audience**: Planning agents making dispatch decisions; Timothy (human) managing budget across 10-day cycles.

---

## Quick Decision Table

| Work Type | Primary Model | Secondary Model | Premium Cost | When to Use |
|-----------|---------------|-----------------|--------------|------------|
| **Planning & Coordination** | Haiku | Claude 3.5 Sonnet (if <40% budget) | HIGH | Strategy, authority decisions, workflow design |
| **Monitoring & Dispatch** | Haiku | Claude 3.5 Sonnet (if <40% budget) | MEDIUM | Track agent progress, prepare next cycles |
| **Foundation Templates** | Haiku | — | HIGH | Work that affects all future tasks (worth the cost) |
| **Code Implementation** | Qwen (free) | — | FREE | Implementation agents only; all coding/editing |
| **Review Synthesis** | Qwen (free) | — | FREE | Agent synthesis reports; isolated in fresh sessions |
| **Critical Reviews** | Haiku | Claude 3.5 Sonnet (if <50% budget) | HIGH | Assess authority chain, validate workflow, novel problems |
| **Routine Documentation** | Claude 3.5 Sonnet | — | FREE | Status updates, standard additions, mechanical edits |
| **Cleanup & Maintenance** | Claude 3.5 Sonnet | — | FREE | File management, duplicate removal, simple refactoring |

---

## Premium Budget Phases

### Phase 1: 0–65% Usage (Abundant Time)

**Model Strategy**: Stay on Haiku for planning/foundation work
- Use Haiku for: Planning, authority decisions, foundation templates
- Use Free (Claude 3.5 Sonnet) for: Routine documentation, cleanup
- Use Free (Qwen) for: All implementation and synthesis

**Indicators**:
- Premium burn is 1–2% per full task cycle
- Foundation work is still ongoing
- Multiple task cycles remain (3+ cycles before reset)

**Decision Points**:
- Dispatch Tasks 1–3 normally
- After Tasks 2–3 validate workflow, prepare to shift Phase 2 model mix

---

### Phase 2: 65–75% Usage (Moderate Caution)

**Model Strategy**: Start mixing models based on task urgency
- Use Haiku for: Complex planning, authority disputes, critical reviews
- Use Claude 3.5 Sonnet for: Routine planning, standard documentation
- Use Free (Qwen) for: All implementation

**Indicators**:
- Premium burn accelerating or stable
- Foundation work complete; entering routine cycle
- 2+ task cycles remain before reset

**Decision Points**:
- New planning: Evaluate complexity first; use free model if routine
- New implementation: Continue dispatching to Qwen (free)
- New reviews: Batch multiple completions for single Haiku review (compress handoffs)

---

### Phase 3: 75%+ Usage (Critical Reserve)

**Model Strategy**: Preserve premium for critical decisions only
- Use Claude 3.5 Sonnet for: All planning and routine work
- Use Free (Qwen) for: All implementation
- **Reserve Haiku for**: Novel problems, workflow breakage, tie-breaking decisions

**Indicators**:
- Premium burn approaching reset
- 1–2 task cycles remain
- Foundation work complete; in maintenance/scaling phase

**Decision Points**:
- New planning: Use Claude 3.5 Sonnet unless truly novel
- New implementation: Continue Qwen (free)
- New reviews: Use Claude 3.5 Sonnet unless critical
- Do NOT dispatch new major tasks; focus on closing current work

---

## Cost-Benefit Analysis

### Why Haiku for Planning & Foundation?

**High-Value Decisions** (justify premium cost):
- Authority model revisions (affect all future agents)
- Workflow coordination (prevent defects in other tasks)
- Critical reviews (validate multi-agent interaction)
- Cross-project decisions (set standards for other projects)

**Example**: Foundation review-gate task (HIGH priority, foundation-level)
- Cost: ~2–3% premium
- Benefit: All new tasks inherit review-gate automatically; prevents systematic defect
- ROI: Worth the cost (prevents 5+ tasks from having same bug)

### Why Claude 3.5 Sonnet for Routine?

**Low-Risk Decisions** (don't need premium):
- Update status.md (simple append)
- Add documentation section (follow existing pattern)
- Create file cleanup handoff (mechanical)
- Approve routine tests (no novel judgment)

**Example**: Status.md maintenance
- Cost: 0% (free model)
- Benefit: Same result as Haiku
- ROI: Infinite (same work, no premium burned)

### Why Qwen for Implementation?

**Isolated Cost** (no premium burn):
- Qwen in fresh session
- Implementation + synthesis + closeout all in one session
- Only premium cost is Copilot review (separate from implementation)
- If Qwen errors, easy to re-run or switch agents

**Example**: Task 2 implementation
- Cost: 0% premium (Qwen free model)
- Benefit: Full task cycle with synthesis report
- ROI: Infinite (work completed at no premium cost)

---

## Decision Flowchart

```
PLANNING AGENT: New work arrives

1. Check current premium usage
   ├─ 0–65%? → Go to [Phase 1 Strategy]
   ├─ 65–75%? → Go to [Phase 2 Strategy]
   └─ 75%+? → Go to [Phase 3 Strategy]

2. Phase 1 (Abundant): Classify work type
   ├─ Foundation/Authority/Critical? → USE HAIKU ✓
   ├─ Routine/Cleanup? → USE CLAUDE 3.5 SONNET
   └─ Implementation/Synthesis? → USE QWEN
   └─ → DISPATCH

3. Phase 2 (Moderate): Classify work type + urgency
   ├─ Complex + HIGH urgency? → USE HAIKU
   ├─ Routine + ANY urgency? → USE CLAUDE 3.5 SONNET
   ├─ Implementation/Synthesis? → USE QWEN
   └─ → BATCH similar work if possible
   └─ → DISPATCH

4. Phase 3 (Critical): Classify work type + novelty
   ├─ Novel problem + MUST solve? → USE HAIKU (critical reserve)
   ├─ Routine or familiar? → USE CLAUDE 3.5 SONNET
   ├─ Implementation/Synthesis? → USE QWEN
   └─ → FOCUS on closing current work
   └─ → DO NOT DISPATCH new major cycles
```

---

## Template Language for Dispatch

**When dispatching to Qwen** (implementation):
```
Dispatch to: Qwen (fresh session, free model)
Scope: [Task description]
Return with: Synthesis report (summaries/), handoff (handoffs/), STOP at review-gate
```

**When dispatching to Haiku** (planning/review):
```
Dispatch to: Copilot (Haiku, planning review)
Scope: [Decision required]
Authority: [What's at stake]
Expected: [GO/REVISE/STOP or approval]
```

**When dispatching to Claude 3.5 Sonnet** (routine):
```
Dispatch to: Claude 3.5 Sonnet (free model, routine work)
Scope: [Simple task description]
Expected: [Completion confirmation]
```

---

## Monitoring & Adjustment

### Monthly Budget Check

**Every 3 days** (or after each major task):
1. Check current premium usage in VS Code settings
2. Update session memory with current usage %
3. Compare against expected burn rate
4. Decide if Phase 1 → Phase 2 transition needed

**If usage exceeds expectations**:
- Audit recent work: Was free model used for premium tasks?
- Adjust next dispatch: Switch to free model for upcoming routine work
- Compress handoffs: Avoid pasting full context; use git links instead

### Budget Tracking File

Update `/memories/session/copilot-review-context-[DATE].md` with:
```markdown
## PREMIUM USAGE TRACKING — [DATE]

**Current Usage**: [X]%
**Expected at this date**: [Y]%
**Variance**: [+/- Z]%
**Next decision point**: [Phase X if usage hits Z%]
```

---

## FAQ for Planning Agents

**Q: Can I use Haiku for routine documentation?**
A: Yes, if premium usage is <30% (abundant budget). No, if >65% (save for critical work).

**Q: Should I batch reviews to save premium?**
A: Yes, after ~3 tasks complete. Send Copilot 3 handoffs + 3 implementation summaries in one review request.

**Q: What if a Qwen task needs Haiku review?**
A: That's OK. Qwen executes (free), Haiku reviews (paid). This is the optimal split.

**Q: Can I switch models mid-task?**
A: No. Once dispatched to an agent, complete the cycle. Switch models on the NEXT task.

**Q: What if I run out of premium before plan reset?**
A: All work goes to Claude 3.5 Sonnet. Quality is still good for planning, just slower.

---

## Reference Files

- **SESSION_CLOSEOUT.md**: Handoff creation procedure (ensures continuity for next planning agent)
- **REVIEW_AGENT_GUIDE.md**: Copilot (Haiku) review authority and scope
- **IMPLEMENTATION_AGENT_SESSION_CLOSEOUT.md**: How implementation agents stop at review-gate
- **SIMPLE_HANDOFF_TEMPLATE.md**: Minimal dispatch format (one screen, copy-paste ready)

---

**Last Updated**: 2026-10-20
**Planning Agent Cycle**: 1 (foundation + validation)
**Next Review**: After Task 3 completes (workflow validation)
