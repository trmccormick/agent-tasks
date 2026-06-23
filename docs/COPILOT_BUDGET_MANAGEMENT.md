---
title: Copilot Pro Budget Management (Active Tracking)
date: 2026-05-31
budget_per_month: $10 (= 10 AI Credits)
overage_policy: NO OVERAGES — access halts when depleted
target_spending: <$5/month (conservative buffer)
---

# Copilot Pro Budget Management

**You're on the $10/month Copilot Pro plan with NO OVERAGES — when the budget depletes, access stops until next billing cycle.**

This document is your **active tracking system** to prevent mid-month access loss.

---

## The Budget

| Item | Amount | Notes |
|---|---|---|
| Monthly allotment | $10 | Hard limit, no exceptions |
| Per-credit cost | ~$0.01 | Average; varies by model |
| Total monthly credits | ~10 | Assume 1 credit = $0.01 |
| **Overage policy** | **Access halts only** | No charges, no fallback — just stops working |
| **Target monthly spend** | **<$5** | Leaves 50% buffer for emergencies |
| **Danger zone** | **>$8 spent** | Less than 20% budget remaining |

---

## Spending by Task Type (ESTIMATES)

These are rough estimates based on official policy. **You must track real spending to refine:**

| Task Type | Estimated Cost | Rationale |
|---|---|---|
| Simple RSpec fix | $0.10-0.30 | Lightweight input, short interaction |
| Single model layer fix | $0.20-0.50 | Moderate tokens, focused scope |
| Single controller fix | $0.15-0.40 | Brief, specific scope |
| Complex multi-file refactor | $2-5 | Long context, multiple files, iteration |
| Architecture decision | $1-3 | High reasoning, multiple options |
| Backlog burn session | $3-7 | Multiple tasks in one session |

**Key insight**: Simple fixes are cheap; complex multi-file work is expensive. Maximize local model use for simple work.

---

## Weekly Monitoring (Critical)

### Action: Check Budget Every Friday

1. **Go to GitHub** → Settings → Billing & Plans → Copilot Pro
2. **Look at "Billing Preview"** — shows current month's spend
3. **Record in this document** (see sections below)
4. **Evaluate status**:
   - ✅ Under $3: Safe, continue normal operations
   - ⚠️ $3-$5: Yellow alert, reduce non-essential Copilot use
   - 🔴 $5-$8: Red alert, emergency use only
   - ❌ Over $8: Critical, do not use unless absolutely necessary

### Template (Copy & Fill Weekly)

```
**[Week of DATE]**
- Current spend: $X
- Budget remaining: $Y
- Status: ✅ Safe / ⚠️ Yellow / 🔴 Red / ❌ Critical
- Action taken: [describe any usage restrictions you're implementing]
- Notes: [anything unusual about spending this week]
```

---

## Current Month Tracking (May 2026)

| Week | Spend | Remaining | Status | Action | Notes |
|---|---|---|---|---|---|
| May 3-9 | — | — | — | — | *Start tracking* |
| May 10-16 | — | — | — | — | — |
| May 17-23 | — | — | — | — | — |
| May 24-31 | — | — | — | — | — |
| **Month Total** | — | — | **—** | — | — |

---

## Usage Alerts (Set These Up)

### Yellow Alert: $5 Spent (50% Budget Used)

**When triggered**: You've used half your monthly budget

**Action**:
- Pause Copilot use for routine work
- Only use for emergency escalations
- Double-check that each use is truly necessary
- Increase local model routing (Codestral/Qwen3.5)
- Consider waiting until next month if possible

**Prevention**: Monitor weekly so you catch this early.

### Red Alert: $8 Spent (80% Budget Used)

**When triggered**: You're in the danger zone

**Action**:
- STOP all non-emergency Copilot use
- Any complex work must use local models only
- Emergency escalations only if blocking critical task
- Plan to preserve final $2 for unexpected needs
- Notify yourself: "Budget critical, restrict Copilot"

**Prevention**: If you hit Red alert multiple months, reassess routing strategy.

### Access Lost: $10 Spent (100% Budget Used)

**When triggered**: Copilot Pro access halts (you're locked out)

**Recovery**:
- No access until next billing cycle (~24h later typically)
- Use local models exclusively for 24h
- When budget resets: Review what caused overspending, adjust strategy

**Prevention**: Never let this happen. Yellow alert at $5, Red alert at $8 — don't proceed past Red.

---

## Local Model Routing (Prevents Budget Overrun)

### When Local Models Should Be Used (Always)

| Situation | Route To | Cost | Reason |
|---|---|---|---|
| Simple RSpec fix | Codestral or Qwen3.5 | $0 | Can complete locally, no Copilot needed |
| Model layer issue | Qwen3.5 | $0 | Local expertise sufficient |
| Controller bug | Codestral | $0 | Localized scope, local can handle |
| Syntax error | Qwen3.5 | $0 | Mechanical fix, no reasoning needed |
| JSON/config edit | Qwen2.5-3B | $0 | Lightweight, local fast |

### When Copilot Pro May Be Needed (Careful)

| Situation | Criteria | Route To |
|---|---|---|
| Local failed after 1 attempt | AND urgent AND under budget | Copilot Pro |
| Complex multi-file design | AND cannot wait AND under budget | Codestral (try first) → Copilot if fails |
| Architecture decision | AND blocking work AND budget >$3 | Copilot Pro |
| Novel pattern | AND time-sensitive AND budget >$2 | Copilot Pro |

**Key rule**: Only escalate to Copilot if local genuinely failed AND you have budget AND it's urgent.

---

## Monthly Checklist

### Start of Month (1st-3rd)

- [ ] Budget resets to $10
- [ ] Review previous month's overspending pattern (if any)
- [ ] Reset target: Keep spend under $5 this month
- [ ] Clear this month's row in budget table (below)

### During Month (Every Friday)

- [ ] Check GitHub Billing page
- [ ] Update weekly tracking table
- [ ] Verify status (Safe / Yellow / Red / Critical)
- [ ] Adjust routing if needed

### End of Month (28th-31st)

- [ ] Final budget check
- [ ] Calculate total month spend
- [ ] Record final spending in COPILOT_USAGE_LOG.md
- [ ] Note any months over $5 (indicator of high usage)

---

## Monthly Spending Summary

| Month | Total Spent | Target | Over/Under | Trend | Notes |
|---|---|---|---|---|---|
| May 2026 | — | <$5 | — | — | *First month, baseline* |
| June 2026 | — | <$5 | — | — | — |
| July 2026 | — | <$5 | — | — | — |
| Aug 2026 | — | <$5 | — | — | — |
| Sep 2026 | — | <$5 | — | — | — |
| Oct 2026 | — | <$5 | — | — | — |
| Nov 2026 | — | <$5 | — | — | — |
| Dec 2026 | — | <$5 | — | — | — |
| Jan 2027 | — | <$5 | — | — | — |
| Feb 2027 | — | <$5 | — | — | — |
| Mar 2027 | — | <$5 | — | — | — |
| Apr 2027 | — | <$5 | — | — | — |

---

## Emergency Fund Strategy

**Reserve $2-3 of your $10 monthly budget for true emergencies.**

**What counts as "emergency"**:
- Production bug blocking deployment
- Critical task blocking team work
- Cannot be solved locally and cannot wait

**What does NOT count**:
- Routine RSpec fixes (use local)
- Backlog work (use local, can wait)
- Learning/experimentation (use local, free)
- Nice-to-have optimizations (use local, defer)

**Reserve protection**:
- When budget reaches $7-8 spent: STOP all non-emergency Copilot use
- Preserve final $2-3 for true emergencies only
- If full month passes without emergencies: Good sign, local models handling work

---

## Red Flags (Indicates Budget Problem)

Watch for these patterns. If you see them, adjust routing:

| Red Flag | Indication | Action |
|---|---|---|
| Hitting Yellow alert ($5) before mid-month | Using Copilot too much | Increase local model use, triage better |
| Multiple Red alerts per month | Chronic overspending | Review which tasks are escalating; improve local routing |
| Access lost (hit $10) | Budget exhausted | Major strategy change needed; consider Path B (Free tier) |
| Emergency fund depleted repeatedly | No buffer left | Immediate action: reduce Copilot escalations 50% |
| Backlog tasks in Copilot queue | Burning budget on backlog | Change strategy: backlog = local only, never Copilot |

---

## Decision: When Copilot Spend is Too High

**If you consistently exceed $5/month** (even before mid-month), that signals:

1. Local models may not be sufficient for your workflow
2. OR routing decisions need improvement (escalating too quickly)
3. OR backlog work is consuming budget (shouldn't happen)

**At renewal (30 days before expiration date)**:
- If average spend >$5/month: Consider keeping Copilot Pro ($10/month may be sustainable)
- If average spend ≤$5/month: Downgrade to Free tier ($0/month), local models proven sufficient
- If average spend >$10/month: Critical issue, major routing overhaul needed

---

## Links to Related Docs

- [COPILOT_USAGE_LOG.md](COPILOT_USAGE_LOG.md) — Detailed task-by-task escalation tracking
- [GITHUB_COPILOT_POLICY_TRACKING.md](GITHUB_COPILOT_POLICY_TRACKING.md) — Full policy reference and renewal decision framework
- [rules/AGENT_ROUTING.md](/Users/tam0013/Documents/git/agent-tasks/rules/AGENT_ROUTING.md) — Routing strategy (local-first)

---

## Quick Reference: Budget Status Right Now

**Today's date**: May 31, 2026

**Current month**: May 2026

**Current spend**: *Check GitHub Billing page*

**Budget remaining**: *= $10 - current spend*

**Status**: 
- ✅ Under $3? Safe
- ⚠️ $3-$5? Yellow
- 🔴 $5-$8? Red
- ❌ Over $8? Critical

**Action**: [Update this weekly]

---

## Quarterly Review (Every 3 Months)

**Perform this review every quarter to assess strategy**:

1. **Average monthly spend** (add up 3 months, divide by 3): $___
2. **Trend** (increasing? decreasing? stable?): ___
3. **Local model reliability** (are escalations due to local failure or convenience?): ___
4. **Emergency fund usage** (was it needed? how often?): ___
5. **Backlog impact** (is Copilot speeding backlog work?): ___

**Decision based on quarterly review**:
- If average <$3/month: Local models working great, consider Path B
- If average $3-$5/month: Balanced, Path A (Copilot Pro) sustainable
- If average >$5/month: High usage, may need strategy rethink or Path A renewal
