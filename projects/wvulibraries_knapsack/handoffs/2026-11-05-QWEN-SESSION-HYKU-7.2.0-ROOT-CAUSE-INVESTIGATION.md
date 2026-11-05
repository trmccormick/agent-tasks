# Qwen Session Handoff — Hyku 7.2.0 Local Testing Strategy Planning
**Date:** 2026-11-05  
**Session Type:** Planning session (design local testing strategy)  
**Status:** Ready for Qwen to begin  
**Token Saved:** Switched to local Qwen model (Claude Haiku reserved for expensive operations)  
**Session Focus:** Plan, don't execute (implementation in follow-up session)

---

## Current State

### Environment
- **hykudev:** v7.1.3 running, web container stable
- **Gem versions:** Fixed (blacklight 7.41.0 + blacklight_advanced_search 7.0.0 via locked Gemfile.lock)
- **Records:** Displaying in Solr (pre-existing data from production)
- **Search:** Empty results despite facets loading (collection mismatch suspected)
- **Snapshot:** Fresh baseline taken (v7.1.3 clean, before 7.2.0 testing)

### What's Been Discovered
1. ✅ **Gem version drift** — Fixed via Gemfile.lock (dev had 8.1.0, prod had 7.0.0)
2. ✅ **Architecture mapped** — Valkyrie indexing writes to one collection, search queries another
3. ✅ **Collection mismatch hypothesis** — Strong evidence but not yet confirmed
4. ⏳ **Root cause of 7.2.0 bug** — Likely in SolrEndpoint or collection naming logic

### Why Search Returns Empty (Working Theory)
```
Indexing Path: Hyrax.index_adapter.save(resource: work) 
  → Uses SolrEndpoint.connection_options['core'] 
  → Writes to collection "???" (likely "uuid" or tenant-based name)

Search Path: CatalogSearchBuilder.build_query 
  → Uses Blacklight.connection_config 
  → Queries from collection "???" (likely "hydra-production")

If collections don't match → facets work (show counts from index), 
but search returns 0 results (queries empty collection)
```

---

## Next Steps (Qwen's Planning Tasks)

### Task 1: Design Local Testing Phases (30 min)
**Objective:** Create a structured local testing plan with clear phases

Steps:
1. Define environment setup requirements (what state does Stack Car need to be in?)
2. Plan v7.1.3 baseline testing (what tests prove it works?)
3. Plan v7.2.0 broken testing (same tests to show difference)
4. Plan comparative analysis (how to systematically compare versions)
5. Identify diagnostic data to collect at each phase
6. Plan risk mitigation and fallback strategy

**Success Criteria:** 
- Clear phase structure (setup → baseline → broken → analysis)
- Specific commands/procedures documented
- Success/failure criteria defined for each phase
- Data capture procedures detailed

### Task 2: Plan Diagnostic Data Collection (20 min)
**Objective:** Design what data will be collected to understand the bug

Questions to address:
- Which logs should be captured? (Rails, Solr, Docker)
- Which Solr queries should we run? (collection list, doc counts, sample docs)
- Should we inspect Rails logs to see which collection is used?
- How should we capture code diffs between versions?
- What metrics/measurements are comparable?
- How should findings be documented for hand-off?

**Success Criteria:**
- Comprehensive checklist of diagnostic commands
- Clear procedure for capturing and comparing results
- Template for documenting findings

### Task 3: Plan Risk & Implementation Sequencing (15 min)
**Objective:** Identify risks and plan what comes next

Questions to address:
- What could go wrong during local testing?
- How do we safely revert if something breaks?
- Should we take snapshots? When?
- After local testing, should we test locally further or move to hykudev?
- Are there blocking dependencies?
- Should this work happen in parallel with other work?

**Success Criteria:**
- Risk assessment document
- Fallback/rollback procedures defined
- Clear handoff to next phase (implementation)
- Timeline estimate with contingencies

---

## Files to Read (Before Starting)

**High Priority** (read first):
- `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/handoffs/2026-11-05-HYKU-UPGRADE-TESTING-STRATEGY.md` (4 testing lessons, architecture overview)
- `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/status.md` (current project status)

**Context** (reference as needed):
- Conversation summary in VS Code session (has architecture details, previous findings)
- `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/tasks/active/` (active task files if any)

---

## VM Access & Resources (For Reference)

If planning requires checking specific details:

**Local Stack Car Access:**
```bash
cd /Users/tam0013/Documents/git/wvu_knapsack
# Can browse code, check branches, view recent commits
git log --oneline feature/hyku-v7.2.0-upgrade | head -20
git log --oneline main | head -20
```

**Docker Compose Files:**
- `docker-compose.yml` (main development)
- `docker-compose.local.yml` (hykudev production)
- `docker-compose.production.yml` (reference for production config)

---

## Success Criteria for Planning Session

✅ **Minimal Success:**
- Clear testing plan with 3-4 phases defined
- Specific diagnostic commands documented
- Risk mitigation strategy identified
- Handoff ready for implementation session

✅ **Ideal Success:**
- Comprehensive testing plan with success/failure criteria for each phase
- Data collection procedures detailed
- Code comparison strategy outlined
- Risk assessment and fallback procedures documented
- Timeline estimate with parallel work identification
- Clear hypothesis about expected root cause
- Recommendations for next phase (implementation approach)

---

## Planning Session Structure

This session produces a **test plan document** that will be:
1. Reviewed/approved by human stakeholder
2. Handed off to implementation agent in follow-up session
3. Executed by implementation agent on local Stack Car
4. Results used to inform next phase (code fix and hykudev testing)

---

## Notes for Qwen

This is a **planning session**, not execution:
- Your job is to DESIGN a test strategy, not run tests
- Think systematically about methodology and data collection
- Document assumptions and success criteria clearly
- Plan for risk mitigation and fallback procedures
- Make it executable (next agent will follow your plan)

**Key Principles:**
- Test locally FIRST (Stack Car is safe environment)
- v7.1.3 = baseline (should work); v7.2.0 = broken (should fail)
- Compare systematically to isolate the exact code change
- Collect ALL diagnostic data (logs, Solr state, Rails output)
- Document everything for hand-off

**Context:**
- hykudev is production (treat carefully)
- Snapshot taken (safe fallback exists)
- Jr dev confirmed local setup works (reference baseline)
- Gem version drift is already fixed

**Time Estimate:** 45-60 min for comprehensive planning

**Deliverable:** Detailed testing plan ready for implementation session

