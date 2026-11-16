# Agent Assignment Guide — Galaxy Game Project

**Last Updated**: June 1, 2026  
**Lesson Learned**: May 31-June 1 (9B model inefficiency experiment)

---

## Quick Decision Matrix

| Task Type | Complexity | Agent | Reason |
|-----------|-----------|-------|--------|
| **Documentation** | Low | 9B Qwen | Fast, low error rate |
| **Refactoring** | Low | 9B Qwen | Mechanical changes, easy to validate |
| **Test Writing** | Medium | 9B Qwen / 3.5 Haiku | Linear work, easy to review |
| **Simple Bugs** | Low-Medium | 9B Qwen | Straightforward fixes |
| **Architecture** | High | 3.5 Haiku / Qwen 27b | Needs reasoning across systems |
| **Complex Implementation** | High | 3.5 Haiku / Qwen 27b | Avoid correction cycles |
| **Debugging** | High | 3.5 Haiku / Qwen 27b | Trace root causes, test setup |
| **Multi-Service Integration** | Very High | 3.5 Haiku / Qwen 27b | Needs big picture understanding |

---

## Current Project Assignments

### Luna Shortage Detection Services (May 31 - June 1)

**Phase 1: Investigation** → Me (Planner) ✅
- Ran Docker introspection
- Documented findings
- Saved significant time

**Phase 2: Implementation** → 9B Qwen ❌ (FAILED — too many corrections)
- Had 4+ logic errors
- Burned tokens on cycles
- Should have used 3.5 Haiku

**Lesson**: Complex domain implementation needs 3.5 Haiku or better, not 9B

### Current Remaining Work (June 1)

**11 RSpec Failures (Debugging)** → **USE: Claude 3.5 Haiku / GPT-4o Mini**
- Requires root cause analysis
- Test setup investigation
- Multiple code paths to trace
- Perfect for capable model, overkill for 9B

---

## Token Efficiency Analysis

### Yesterday (May 31) — 9B Model Approach
```
Tokens Used: High (investigation + 3 rounds of corrections)
Useful Work: 25% (code works but required constant fixes)
Efficiency: LOW ❌
```

### Recommendation for Future Complex Work
```
Tokens Used: Medium (direct implementation, fewer corrections)
Useful Work: 95% (code right first time)
Efficiency: HIGH ✅
```

**Cost**: Small model + correction cycles burns MORE tokens than using capable model upfront.

---

## For Your Next Delegation

1. **Read task requirements** (complexity level)
2. **Match to agent capability** (see matrix above)
3. **DO NOT use 9B for**:
   - Complex debugging
   - Multi-service integration
   - Architecture decisions
   - Test setup investigation

4. **9B works great for**:
   - Documentation
   - Simple refactors
   - Linear test writing
   - One-file changes

---

## Agent Availability Check

Before delegating:
- ✅ Confirm agent is available
- ✅ Verify capability matches task
- ✅ Check if task requires local execution (Docker, Rails env)
- ❌ Don't force low-capability model on high-complexity work

**Current Status**: Task 2026-06-01-FIX-REMAINING-11-FAILURES.md awaiting **Claude Haiku 4.5** assignment.
