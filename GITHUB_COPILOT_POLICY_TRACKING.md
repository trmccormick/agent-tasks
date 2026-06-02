---
title: GitHub Copilot June 1, 2026 Policy Changes — Tracking & Planning
status: POLICY_RELEASED
date_created: 2026-05-18
last_updated: 2026-05-18
policy_release_date: 2026-06-01
subscriber_status: ANNUAL_PREPAID
subscription_expiration: UNKNOWN (check GitHub billing page)
---

# GitHub Copilot June 1, 2026 Changes — Usage-Based Billing Model

**Status**: ✅ POLICY RELEASED — Effective June 1, 2026  
**Your Subscription**: 📌 **ANNUAL PREPAID** (exact expiration date unknown)  
**Billing Model**: **$10/month = $10 AI Credits** (1:1 ratio, ~$0.01 per credit)  
**Critical Constraint**: ⚠️ **NO OVERAGES — when depleted, access halts until next billing cycle** (no fallback, no charges)  
**Where to Check**: GitHub Account → Billing & Plans → Subscriptions (shows renewal date + current spend)  
**Current Implication**: Path A (Copilot Pro) is locked in until renewal. No action needed before expiration date.

---

## What We Know (June 1, 2026 - OFFICIAL POLICY)

### CRITICAL: Death of the 0x Free Tier

**Major Policy Change**: GitHub is removing the 0x free tier for ALL agents. Every model now has a cost.

**Current State (May 31, 2026)**:
```
GPT-4.1           = 0x (unlimited, FREE) ← PRIMARY EXECUTION
Haiku             = 0.33x (paid)
Claude Sonnet     = 1x (paid)
Claude Opus       = 1.5x (paid)
Local Codestral   = 0 tokens (local, unlimited)
Local Qwen3.5     = 0 tokens (local, unlimited)
```

**Expected After June 1 (ASSUMPTION - NEEDS VERIFICATION)**:
```
GPT-4.1           = 0.5x? (paid, but cheaper than Sonnet) ← NEW COST UNKNOWN
Haiku             = 0.33x (paid, remains cheapest premium)
Claude Sonnet     = 1x (paid, standard)
Claude Opus       = 1.5x (paid, premium)
Local Codestral   = 0 tokens (local, unlimited) ← BECOMES PRIMARY
Local Qwen3.5     = 0 tokens (local, unlimited) ← BECOMES PRIMARY
```

**Impact**: Your unlimited GPT-4.1 0x tier is **GONE**. All execution work now has a cost UNLESS you use local models.

### GitHub Copilot Pro: Usage-Based Billing Model (OFFICIAL)

**Pricing & Credits**:
- Subscription fee: **$10/month** (unchanged from old pricing)
- Monthly credit allotment: **$10 in AI Credits** (1:1 dollar to credit ratio)
- Credit consumption: Based on **input tokens + output tokens + cached tokens**
- Cost per credit: **~$0.01 per credit**, but varies significantly by model
- When depleted: **Agent usage halts completely** — NO fallback to cheaper models

**What's Free** (doesn't consume credits):
- ✅ Standard inline code completions
- ✅ "Next Edit Suggestions"
- ✅ Basic autocomplete in VS Code

**What Costs Credits**:
- ❌ Standard chat & coding agents (all multi-step interactions)
- ❌ Code Review via GitHub Actions (ALSO consumes Actions minutes at GitHub standard rates)
- ❌ Complex multi-file agent sessions (burn through credits "significantly faster")

**Credit Consumption Rates by Model Type** (from official policy):
- Lightweight models: Fraction of a credit per task
- Standard models (Sonnet): Variable, roughly 1-2 credits per moderate task
- Complex multi-file sessions: 5-10+ credits per task
- **GPT-4.1 rates: NOT SPECIFIED** in official policy

**Annual Plan Subscribers**:
- Old annual plans remain valid until expiration
- Updated with "model multipliers" (method for computing quota consumption not specified)
- After expiration: Move to Copilot Free tier or upgrade to monthly Pro

**Other Plans** (for reference):
- Copilot Pro+: $39/month = $39 in AI Credits
- Copilot Business: $19/user/month + $30 promo credits through August
- Copilot Enterprise: $39/user/month + $70 promo credits through August

### What Changed From Old Policy

**Old System (Pre-June 1)**:
- Premium Request Units (PRUs)
- Fallback to cheaper model when limits reached
- Unclear cost structure

**New System (June 1+)**:
- GitHub AI Credits ($1 = 100 credits? NO — $1 = 1 credit, 1:1 ratio)
- NO fallback mechanisms — stops completely when depleted
- Clear: ~$0.01 per credit, varies by model
- All 0x free tiers eliminated

### Tracking & Billing

**How to monitor**:
- GitHub Copilot Billing Settings page
- "Billing Preview" shows estimated spend
- Real-time view of which models/tasks consuming credits
- Hard spending limits available for organizations (not individual Pro accounts)

---

## Your Status: Annual Prepaid Plan (Expiration Date Unknown)

**What This Means**:
- ✅ You locked in Copilot Pro before June 1 via annual prepaid subscription
- ✅ June 1 policy change does NOT affect your billing until prepaid expires
- ✅ You are NOT subject to the $10/month usage-based credit system until renewal
- ✅ You have unlimited Copilot Pro access until your renewal date

**Where to Find Your Expiration Date**:
1. Go to GitHub → Settings → Billing & Plans
2. Look for "Subscriptions" section
3. Find "GitHub Copilot Pro" → shows renewal/expiration date
4. **UPDATE THIS DOCUMENT** once you know the exact date

**Your Implications**:
- **NOW ([until expiration date])**: Use Copilot Pro freely, no credit tracking needed
- **30 days before [expiration date]**: Decide whether to renew at new usage-based rates ($10/month credits)
- **Path A locked in**: You're automatically on Path A (Copilot Pro) until prepaid ends
- **Can ignore**: June 1-3 testing, credit budgeting, route optimization for Pro tier (until expiration)
- **Can focus on**: Testing local models (Codestral/Qwen3.5) in parallel to prep for potential Path B

**When You Need To Decide**:
- Decision window: Last 30 days before [expiration date]
- Question: Is $10/month AI Credits worth it, or downgrade to Copilot Free + local models?
- Information needed: How many Copilot tasks do you actually need per month?

---

## Analysis & What's Still Unknown

### What We Now Know (Concrete)

1. ✅ **$10 credit budget, period.** No "1,000 credits" ambiguity — $10 = 10 credits at ~$0.01/credit
2. ✅ **No fallback.** When depleted, agent usage STOPS until next billing cycle
3. ✅ **Lightweight models use fractions of a credit** (good for cheap tasks)
4. ✅ **Code completions remain free** (no credit consumption)
5. ✅ **Avoid Code Review** (costs both Actions minutes + credits)
6. ✅ **Complex multi-file sessions are expensive** ("burn through credits significantly faster")

### Critical Remaining Unknown #1: GPT-4.1 Availability & Pricing

**The Policy Says**: "Standard Chat & Coding Agents consume AI Credits at varying API rates per model"

**What This Means**: Each model has a different cost multiplier.

**What's Missing**: 
- ❌ Is GPT-4.1 available in Copilot Pro at all?
- ❌ If yes, what's its cost multiplier? (Cheaper than Sonnet? Same as Sonnet? More?)
- ❌ Is there any 0x equivalent in Copilot Pro?

**Why It Matters**: If GPT-4.1 is expensive (>Sonnet) or unavailable, Path A (keep Copilot Pro) becomes unviable.

### Critical Remaining Unknown #2: Model Multiplier Mapping

**The Policy Hints**: Annual plans get "updated usage multipliers" for computing quota consumption

**What This Means**: Different models cost different amounts

**What's Missing**:
- ❌ Exact multiplier for Haiku (0.33x equivalent? Less?)
- ❌ Exact multiplier for Sonnet (1x equivalent?)
- ❌ Exact multiplier for Opus (1.5x equivalent?)
- ❌ Do these multipliers apply to monthly Pro credits too, or only annual plans?

**Why It Matters**: Budget sustainability depends on knowing which model to use for which task.

### What You CAN Infer Now

**From "~$0.01 per credit" and "complex multi-file sessions burn through significantly faster"**:

A simple fix might cost:
- 0.1-0.5 credits (cheap models)
- 0.5-2 credits (standard models)

A complex multi-file refactor might cost:
- 5-10+ credits (at standard rates)

**At $10/month**:
- Best case: 20+ simple fixes per month (if lightweight models cheap enough)
- Worst case: 1-2 complex tasks per month (if multi-file expensive)
- Most likely: 5-10 mixed tasks per month

### Decision Framework (Updated)

**The fundamental question**: Is local Codestral/Qwen3.5 execution faster or more economical than Copilot Pro?

**Path A Decision Rule** (Keep Copilot Pro):
- IF GPT-4.1 available in Copilot Pro
- AND GPT-4.1 costs ≤ Sonnet equivalent cost
- AND simple fixes cost <0.5 credits
- THEN keep Copilot Pro ($10/month sustainable)

**Path B Decision Rule** (Downgrade to Free):
- IF GPT-4.1 not available
- OR GPT-4.1 costs > Sonnet equivalent
- OR simple fixes cost >1 credit
- THEN downgrade to Free, use local Codestral/Qwen3.5 exclusively

**Test Metrics** (June 1-3):
1. Assign 1 simple RSpec fix to Copilot Pro
2. Check Billing Preview: How many credits consumed?
3. Calculate: $10 ÷ credits_per_task = tasks_sustainable_per_month
4. Compare to: How many tasks can M4 Codestral handle daily?
5. Choose path based on which is faster/cheaper

---

## Decision: Revised Routing Strategy (Post-June 1 — CRITICAL FOR PREPAID)

**THE FUNDAMENTAL SHIFT**: $10/month budget with NO overages = must manage usage actively. Access loss mid-month is possible if spending not tracked.

**Your $10 Budget Reality** (NO OVERAGES):
- $10/month in credits, hard stop
- When depleted: No access to Copilot Pro agents until next month
- Simple fixes: likely 0.1-0.5 credits each (20-100 tasks if cheap, but risky)
- Complex multi-file: likely 5-10+ credits each (1-2 tasks max)
- **Sustainable without risk**: 3-5 tasks/month (leaves $5-7 buffer)
- **Aggressive but possible**: 5-10 tasks/month (higher risk of mid-month exhaustion)

**Access Loss Prevention Strategy**:
- Target spend: <$5/month (50% buffer)
- Yellow alert: >$5 spent (reduce non-essential use)
- Red alert: >$8 spent (emergency use only)
- If hit $10: No Copilot until next billing cycle

**Two Possible Workflows**:

### Path A: Copilot Pro Becomes Primary Execution (IF GPT-4.1 Available + Cheap)

```
Planning: Gemini (0)
Triage: Qwen3.5 (0, local)
Validation: Perplexity (0)

IMPLEMENTATION (if GPT-4.1 cheap):
├─ Simple fixes (RSpec, model, controller)
│  └─ Copilot Pro ($10/month = 5-10 tasks sustainable)
│
├─ Complex multi-file work
│  └─ Local Codestral (unlimited) [Copilot too expensive for this]
│
└─ Rare architectural work
   └─ Claude 1x (premium, expensive)
```

**Sustainability**: 5-10 Copilot Pro tasks/month, 20+ local Codestral tasks/month  
**Budget risk**: Medium — if GPT-4.1 is expensive or unavailable, Path A fails

### Path B: Local Models Become Primary Execution (IF Copilot Pro Too Expensive)

```
Planning: Gemini (0)
Triage: Qwen3.5 (0, local)
Validation: Perplexity (0)

IMPLEMENTATION (primary execution):
├─ All mechanical work (90%)
│  └─ Local Codestral (unlimited) OR Qwen3.5 (unlimited)
│
├─ When local models overwhelmed
│  └─ Copilot Pro (for urgent work, if budget permits)
│
└─ Rare architectural work
   └─ Claude 1x (premium, expensive)
```

**Sustainability**: Unlimited local work, minimal Copilot/Claude spend  
**Budget risk**: Low — local unlimited, $10 saved monthly

### Updated AI Stack (June 1+, Post-Decision)

**OPTION A: Keep Copilot Pro** (if GPT-4.1 available + cost ≤ Sonnet)

| Tier | Agent | Cost | Role | Monthly Capacity |
|---|---|---|---|---|
| **0-Token** | Gemini | 0 | Planner | Unlimited |
| **0-Token** | Qwen3.5 (local) | 0 | Detail, triage | Unlimited |
| **0-Token** | Perplexity | 0 | Validation | Unlimited |
| **Paid** | Copilot Pro | $10/mo | Primary execution (simple work) | 5-10 tasks |
| **Local** | Codestral (M4) | 0 | Complex/large context | Unlimited |
| **Premium** | Claude 1x | 1x cost | Rare architectural | 1-2 tasks |

**OPTION B: Downgrade to Copilot Free** (if Pro too expensive)

| Tier | Agent | Cost | Role | Monthly Capacity |
|---|---|---|---|---|
| **0-Token** | Gemini | 0 | Planner | Unlimited |
| **0-Token** | Qwen3.5 (local) | 0 | Detail, triage, execution | Unlimited |
| **0-Token** | Codestral (M4) | 0 | Primary execution | Unlimited |
| **0-Token** | Perplexity | 0 | Validation | Unlimited |
| **Free** | Copilot Free | 0 (completions only) | Inline suggestions | Unlimited |
| **Premium** | Claude 1x | 1x cost | Rare architectural | 1-2 tasks |

---

## Workflow Impact: Before vs After June 1

### Before June 1 (Current: Unlimited 0x Tier)
```
Gemini (0)           → Plan
   ↓
Qwen3.5 (0, local)   → Triage & detail
   ↓
Perplexity (0)       → Validate
   ↓
GPT-4.1 0x (0, FREE) → PRIMARY EXECUTION (unlimited)
   ↓
Claude 1x (premium)  → Complex work only
```

**Characteristics**:
- ✅ Unlimited GPT-4.1 execution
- ✅ No budget constraints
- ✅ Route ALL mechanical work to GPT-4.1 0x
- ✅ Copilot Pro not in workflow (experimental/unknown)

### After June 1 (OPTION A: Copilot Pro + Local Models)

**Assumption**: GPT-4.1 available in Copilot Pro at reasonable cost

```
Gemini (0)           → Plan
   ↓
Qwen3.5 (0, local)   → Triage & detail
   ↓
Perplexity (0)       → Validate
   ↓
IMPLEMENTATION (choose based on work type):
├─ Simple fix        → Copilot Pro (budget: 5-7 tasks/month)
├─ Complex work      → Local Codestral (unlimited)
└─ Rare/architecture → Claude 1x (premium)
```

**Characteristics**:
- ✅ Limited Copilot Pro budget ($10/month = ~10 credits at $0.01/credit)
- ✅ Local Codestral/Qwen3.5 as overflow for complex work
- ✅ Must route strategically: simple → Copilot, complex → local
- ⚠️ Need to track credit usage monthly (can run out mid-month)
- ⚠️ When depleted: NO fallback, Copilot stops working

### After June 1 (OPTION B: Local-First + Claude Premium)

**Assumption**: Copilot Pro too expensive or GPT-4.1 not available

```
Gemini (0)           → Plan
   ↓
Qwen3.5 (0, local)   → Triage & detail
   ↓
Perplexity (0)       → Validate
   ↓
IMPLEMENTATION (primary execution):
├─ 90% mechanical    → Local Codestral/Qwen3.5 (unlimited)
├─ Emergency backup  → Copilot Free (completions only)
└─ Rare/complex      → Claude 1x (premium, limited use)
```

**Characteristics**:
- ✅ Primary execution fully unlimited (local models)
- ✅ Minimal budget constraints
- ✅ $10/month saved (downgrade to Free)
- ✅ Depends on local model quality

---

## June 1 Decision Framework (UPDATED FOR PREPAID STATUS)

**Your Situation**: Prepaid annual plan = no decision needed until expiration

**Revised Action Plan**:

### Phase 1: NOW through Prepaid Expiration (Manage $10/month Budget Actively)

**What you can do**:
- ✅ Route complex work to Copilot Pro but monitor spending daily
- ✅ Test local Codestral/Qwen3.5 in parallel (preparing for potential Path B)
- ✅ Measure: How much Copilot work do you actually do per month?
- ✅ Collect: Data on local model performance for same tasks
- ✅ **Track spending**: Update COPILOT_USAGE_LOG.md weekly to prevent access loss

**What you DON'T need to do**:
- ❌ Test June 1-3 (prepaid = not affected)
- ❌ Make Path A vs B decision now (do it 30 days before expiration)
- ❌ Hoard credits

**Critical for prepaid period**:
- ⚠️ Monitor spending weekly
- ⚠️ Set alerts at 50% ($5) and 80% ($8) spent
- ⚠️ Prevent hitting $10 mid-month (would lose access)

### Phase 2: 30 Days Before Prepaid Expiration (Make Renewal Decision)

**At that point, decide**:
- **Option 1**: Renew at new usage-based rates ($10/month = ~$10 AI Credits/month)
  - Only viable if you use <5-10 Copilot tasks/month on average
  - Measure this data during Phase 1
  
- **Option 2**: Downgrade to Copilot Free + use local models exclusively
  - Costs $0/month
  - Requires local Codestral/Qwen3.5 to be reliable (test during Phase 1)

**Decision Criteria**:
- **Keep Copilot Pro if**: Average 1-2 tasks/month actually need Copilot (vs local)
- **Switch to Free if**: Local models handle 95%+ of your execution work

---

## Next Steps (May 18 - Prepaid Expiration)

### Phase 1: NOW through May 2027 - Strategic Credit Conservation

**Strategic Approach**: Use local models (Codestral/Qwen3.5 via Continue) as PRIMARY execution. Reserve Copilot Pro credits for hard work only.

**What to do**:

1. **Route by default to local models**:
   - Simple RSpec fixes → Codestral (local, free)
   - Model layer issues → Qwen3.5 (local, free)
   - Boilerplate, controllers → Codestral (local, free)
   - Only escalate to Copilot Pro if local fails

2. **Track which tasks REQUIRE Copilot Pro**:
   - Record: Task name, complexity level, why local model wasn't sufficient
   - Pattern: Complex multi-file reasoning? Architectural decisions? Novel patterns?
   - Goal: Identify when Copilot Pro is truly needed vs. optional convenience

3. **Measure credit efficiency**:
   - Copilot Pro budget: ~$10 AI Credits (prepaid, spread across 12 months)
   - Target: Use <1 credit/month on average (save credits for emergencies)
   - If achievable: Demonstrates local models can handle 95%+ of work

**Why**: At renewal (April/May 2027), if you averaged <1 credit/month usage, downgrading to Free is a no-brainer. You'll have proven local models work for your workflow.

### Phase 2: 30 Days Before Prepaid Expiration (Likely Late April 2027)

**Decision Window Opens**:
1. [ ] Review Phase 1 data: Average monthly Copilot usage?
2. [ ] Calculate: At new $10/month credit rate, is it sustainable?
3. [ ] Compare: Local model cost (time/compute) vs. $10/month
4. [ ] Decide: Renew Path A (Pro) or switch Path B (Free + local)?

**Example Decision Tree**:
```
IF average usage >5 Copilot tasks/month
AND local models struggled in testing
THEN renew Copilot Pro ($10/month)

ELSE IF average usage <5 tasks/month
AND local models handled 95% of work
THEN downgrade to Copilot Free ($0) + local primary
```

---

## June 1, 2026 - What Changes for You (Prepaid)

**You**: Nothing changes immediately. Your prepaid subscription continues.

**Everyone Else on monthly billing**: Moves to usage-based $10/month credits, limited budget.

**Implication**: You have an unfair advantage during June 1 - August 2026 (roughly). Everyone else is struggling with credit budgets; you're unlimited.

**Smart Move**: Use this prepaid period to:
1. Establish safe spending patterns (target <$5/month)
2. Test Codestral/Qwen3.5 extensively (local backup)
3. Identify which tasks genuinely need Copilot vs. local
4. Build buffer to prevent mid-month access loss

---

## Phase 1 Routing Strategy: Local-First + Conservative Budget Management

**Philosophy**: $10/month budget is HARD LIMIT with access loss if depleted. Prioritize local models to preserve Copilot credits for true emergencies.

**Target Monthly Spending**: <$5 (conservative 50% buffer to prevent access loss)

**Updated AI Stack (From Now Until Expiration)**:

| Tier | Agent | Cost | Role | When To Use |
|---|---|---|---|---|
| **Primary** | Codestral (M4 local) | 0 | Mechanical work, RSpec, controllers | Throughout month (default first) |
| **Primary** | Qwen3.5 (local) | 0 | Detail, triage, model layer | Throughout month (default first) |
| **0-Token** | Gemini | 0 | Planning, prioritization, coordination | Always in workflow |
| **0-Token** | Perplexity | 0 | Validation, research | When needed |
| **Secondary (CONTROLLED)** | Copilot Pro | $0.1-0.5 per task | Emergency escalations only | (1) Local failures anytime, (2) Only when critical |
| **Premium** | Claude 1x | 1x cost | Rare architectural decisions | <1% of tasks (emergencies only) |

**Decision Logic**:

**During the Month** (Local-First, Conservative Spending):
```
Task arrives:
├─ Simple (RSpec, models, controllers)
│  └─ Route to Codestral/Qwen3.5 (local, free)
│
├─ Complex (multi-file refactor, architecture)
│  └─ Try local first if you have time
│     ├─ Success? Done.
│     └─ Failure AND NOT URGENT? Queue for weekend or next month
│     └─ Failure AND URGENT? Check budget status:
│         ├─ Under $3 spent: OK to escalate to Copilot Pro
│         ├─ $3-$5 spent: Reconsider, try local one more time
│         └─ Over $5 spent: ONLY if truly critical (emergency)
│
└─ Backlog task
   └─ DO NOT burn Copilot on backlog (preserve budget for emergencies)
```

**Budget Monitoring** (Weekly):
1. Check GitHub Billing page
2. Update COPILOT_USAGE_LOG.md with current spend
3. Alert thresholds:
   - ⚠️ Yellow: >$5 spent (halt non-essential Copilot use)
   - 🔴 Red: >$8 spent (emergency use only)
   - ❌ Stop: Would hit $10 (wait for next billing cycle)

**Tracking & Target**:
- Track month-end burn separately from emergency escalations
- Mark in COPILOT_USAGE_LOG.md: "Emergency escalation" only
- **DO NOT track "month-end burns"** (changed strategy due to hard budget limit)
- Measure: Total monthly spend (target <$5)
- Prevent: Spending >$8 (danger zone for access loss)

---

## Renewal Decision Reference (At Expiration Date)

### Path A (Renew Copilot Pro) — Most Likely Outcome

**Keep if** (expected at renewal):
- ✅ Emergency escalations stayed low (<1-2/month): Local models reliable for urgent work
- ✅ Month-end burns cleared meaningful backlog: Copilot accelerated backlog work
- ✅ $10/month is acceptable cost for productivity boost
- ✅ Premium agents (Copilot Pro) provide measurable value vs local-only approach

**Outcome**: Renew Copilot Pro at $10/month = $10 AI Credits/month

**Keep because**: Local models handle production work reliably. Copilot Pro accelerates backlog clearance. $10/month is small cost for that acceleration.

### Path B (Downgrade to Free) — If Local Models Exceed Expectations

**Switch if** (unlikely but possible at renewal):
- ❓ Emergency escalations were zero (local models never failed)
- ❓ Backlog cleared so fast via month-end burns that you questioned the value
- ❓ $10/month felt wasteful despite productivity gains
- ❓ New local models (Qwen4, etc.) surpassed Copilot Pro in your tests

**Outcome**: Downgrade to Free ($0/month), all work to local Codestral/Qwen3.5

**Unlikely because**: Premium agents provide consistent value; $10/month is minimal cost

---

## Critical Unknowns Summary (For Future Reference)

| Unknown | Why It Matters | Test Method |
|---|---|---|
| Is GPT-4.1 available in Copilot Pro? | If NO: Path B only; If YES: Path A possible | Check Copilot Pro model selector |
| What's the cost multiplier for GPT-4.1? | If 0.5x: ~10 tasks/mo; If 1x: ~5 tasks/mo; If 2x: too expensive | Run test task, check GitHub Billing |
| Do token multipliers apply to Copilot credits? | If YES: Haiku cheaper; If NO: all same cost | Compare Haiku vs. Sonnet task costs |
| Is there a Copilot Pro+ tier? | If YES: different pricing might be worth it | Check GitHub Copilot pricing page |

**Decision Tree**:
```
GPT-4.1 available in Copilot Pro?
├─ YES → What's the cost multiplier?
│         ├─ ≤0.5x → Keep Copilot Pro (Path A)
│         ├─ 1x → Marginal, test both paths
│         └─ >1x → Downgrade to Free (Path B)
│
└─ NO → Downgrade to Copilot Free (Path B mandatory)
```

---

## Immediate Action Items (Before Expiration Date)

### Priority 1: Find Your Exact Expiration Date (THIS WEEK)

**Action**:
1. [ ] Go to GitHub → Settings → Billing & Plans → Subscriptions
2. [ ] Find "GitHub Copilot Pro" subscription
3. [ ] Note the renewal/expiration date
4. [ ] Update this document (replace "[expiration date]" with actual date)
5. [ ] Add to calendar: Renewal decision deadline (30 days before expiration)

**Why**: Everything else depends on knowing when your prepaid ends

### Priority 2: Implement Local-First Routing (This Week)

**Goal**: Set up Codestral/Qwen3.5 as default agents in Continue + AGENT_ROUTING.md

**Action**:
1. [ ] Update docs/new_agent/rules/AGENT_ROUTING.md: Add "Local First" section
   - Simple tasks (RSpec, models, controllers) → Codestral
   - Triage/detail → Qwen3.5
   - Only escalate to Copilot Pro on local failure
2. [ ] Test one RSpec fix with local Codestral (verify it works well)
3. [ ] Confirm docs/new_agent/COPILOT_USAGE_LOG.md is ready for tracking

**Why**: Establishes the baseline for measuring credit efficiency over prepaid period

### Priority 3: Track Every Copilot Escalation (Ongoing)

**Goal**: Collect data on when/why Copilot Pro is actually needed

**Action**:
1. [ ] Every time local model fails, escalate to Copilot Pro
2. [ ] Log in COPILOT_USAGE_LOG.md: Date, task name, why local failed, credits used
3. [ ] Review monthly: Identify patterns in escalation types
4. [ ] Target: <1 credit/month average (8-12 escalations/month)

**Why**: At renewal, this data proves whether downgrading to Free is viable

### Priority 4: No Credits-Tracking Burden Until 30 Days Before Expiration

**NOT required now**:
- ❌ Weekly credit audits (your prepaid is unlimited until expiration)
- ❌ Monthly budget warnings (no monthly resets until renewal)
- ❌ Immediate routing doc overhauls (you have time)
- ❌ Credit optimization obsession (focus on local reliability instead)

**Actually focus on**: Making sure local models work well for your tasks

---

## FAQ: Prepaid Copilot Pro + Local-First Strategy + Month-End Burn

**Q: Should I try to use up my prepaid Copilot budget?**  
A: YES, at month-end. During the month, prioritize local models to gather reliability data. At month-end, review queued backlog tasks and assign them to Copilot Pro until credits are ~depleted. You've already paid for it — use it productively on backlog work.

**Q: How do I decide which backlog tasks to burn on Copilot at month-end?**  
A: Pick tasks that are moderately complex but clearly benefiting from Copilot (multi-file refactors, tricky patterns, etc.). Avoid trivial fixes (those stay local) and avoid impossible tasks (Copilot won't help). Target tasks that would take Codestral longer but Copilot could handle faster.

**Q: What if I don't have queued backlog at month-end?**  
A: Then don't artificially create work. You've proven local models work — that's a win. Leave credits unspent that month (though this is unlikely given your project size).

**Q: What if I hit the $10 limit mid-month?**  
A: Access halts. No overages, no charges, just stopped until next billing cycle. Prevent this by monitoring weekly and keeping spend under $5/month on average.

**Q: What if local Codestral fails on a task?**  
A: Escalate to Copilot Pro. Log it in COPILOT_USAGE_LOG.md. This data is gold for your renewal decision.

**Q: Should I compare Codestral vs Qwen3.5 performance?**  
A: Yes, try both on similar tasks. You might find one is better for RSpec fixes, the other for model layer work. Document which works best.

**Q: What if local models frequently fail?**  
A: That would argue for renewing Copilot Pro (Path A). But based on your strategy, you're expecting <1 credit/month, so frequent failures are unlikely if local models are reasonable.

**Q: How do I prevent access loss?**  
A: Monitor spending weekly. Target <$5/month average (leaves $5 buffer). Use GitHub Billing page to check current spend. Update COPILOT_USAGE_LOG.md. Yellow alert at $5, red alert at $8. Never allow it to hit $10 mid-month.

**Q: How do I know if a task is "hard enough" for Copilot Pro?**  
A: If local model output is completely wrong or non-functional after 1-2 attempts, escalate. If it's 80% right and just needs tweaks, use local. Copilot Pro is for "local failed" not "local suboptimal".

**Q: Should I track time spent on each agent too?**  
A: Good idea. If Codestral takes 10 minutes (wrong output), Qwen3.5 takes 5 minutes (right output), and Copilot Pro takes 2 minutes (faster)... you have a cost/benefit case for renewal.

**Q: What if my M4 Mac gets overwhelmed by local models?**  
A: Unlikely. Codestral/Qwen3.5 are reasonable models. If they're causing system slowdown, that's a hardware limitation, not a model quality issue. Still use them; just accept slower response time.

**Q: Will my local models improve by renewal date?**  
A: Maybe. New local models might release over time. By renewal, you can evaluate latest options (Qwen4, DeepSeek improvements, etc.) and decide if local-first is still best for your workflow.

**Q: Since I prepaid for a year, how does that affect June 1 policy?**  
A: Not at all. June 1 policy only affects new monthly subscribers. Your prepaid continues unchanged. You have an unfair advantage for 12 months.

**Q: Should I mention to other team members they need to watch June 1?**  
A: Absolutely. They'll hit the $10 credit limit hard if on monthly billing. Your local-first strategy is a good template for them to consider too.

---

## Summary: What Prepaid Means for You

| Factor | Impact |
|---|---|
| **Urgency to decide** | Low — decide at renewal (30 days before expiration) ✅ |
| **Action now** | Establish safe <$5/month spending pattern; collect local model reliability data 📊 |
| **Budget constraint** | $10/month hard limit — NO overages, just access loss 🚨 |
| **Expected renewal** | Path B likely (downgrade to Free) — if local models prove reliable ✅ |
| **Local models role** | Primary execution; Copilot as emergency backup only 🔍 |
| **Advantage** | Prepaid period lets you test patterns before renewal; NO billing risk ✅ |
| **Next step** | Execute month-end burn strategy, track both emergency + burn |

**Recommendation**: At renewal, you're likely keeping Copilot Pro. $10/month is minimal cost for productivity boost. Use this prepaid period to:
1. Test local models (Codestral/Qwen3.5) on production work — are they reliable?
2. Measure month-end burn effectiveness — how much backlog clears?
3. Track emergency escalations — do you need Copilot Pro for urgent work?
4. Build confidence — prove which tasks local models handle well

**At Renewal Decision**: If local models are reliable for urgent work + month-end burns clear meaningful backlog, renewal is justified. $10/month is worth the acceleration.

---

## Old Action Items (Deprecated - These Were for Monthly Billing Users)

The following testing tasks were written for users on monthly plans starting June 1. Since you have prepaid, these don't apply now:

- ❌ Test credit consumption June 1-3
- ❌ Verify GPT-4.1 availability immediately
- ❌ Update routing docs for June 1 cutover
- ❌ Create monthly credit tracking June 1 (defer to April 2027)

These will become relevant when your prepaid year expires (likely April/May 2027).

