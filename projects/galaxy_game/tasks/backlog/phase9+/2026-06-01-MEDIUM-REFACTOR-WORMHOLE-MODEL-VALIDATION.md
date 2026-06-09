---
status: backlog
priority: MEDIUM
type: refactor
system_domain: UNITS | CONTROLLERS
mvp_alignment: SPEC_HEALTH
local_worker_safe: true
---

# TASK: Wormhole Model — Comprehensive Review & Validation

**Status**: BACKLOG  
**Priority**: MEDIUM  
**Type**: refactor  
**Created**: 2026-06-01  
**Last Updated**: 2026-06-01  

---

## Agent Assignment

**Assigned To**: Claude Sonnet (1x) or GPT-5 mini (0x)  
**Why This Agent**: Requires deep understanding of model architecture, associations, and validation logic.  
**Supervision Level**: standard

---

## Context

The `Wormhole` model represents interstellar travel routes between planets. It's a critical piece of infrastructure for the game's logistics and trade systems. This task involves reviewing the current implementation, identifying issues or gaps, and ensuring it aligns with architectural standards and operational requirements.

**Relevant Architecture Docs**:
- `docs/new_agent/rules/DECISIONS.md` — locked architectural decisions
- `docs/new_agent/rules/GUARDRAILS.md` — execution rules
- `galaxy_game/app/models/wormhole.rb` — current implementation

---

## Problem Statement

The wormhole model needs a comprehensive review to ensure:
1. All required associations are properly defined and bidirectional where needed
2. Validations are appropriate and comprehensive
3. Scopes and methods align with how wormholes are used in the codebase
4. No missing callbacks or scopes that other parts of the system expect

**Current behavior**: Model may have incomplete associations, missing validations, or deprecated patterns.

**Expected behavior**: Model should be fully validated, properly associated, and ready for integration with trade/logistics systems.

---

## Files Involved

### Primary Files — you will review these
| File | Purpose | Key Method/Section |
|---|---|-|
| `galaxy_game/app/models/wormhole.rb` | Wormhole model definition | Full file review |
| `galaxy_game/db/migrate/*_create_wormholes.rb` | Schema definition | Check columns vs model |

### Reference Files — read to understand usage
| File | Why You Need It |
|---|---|
| `galaxy_game/app/models/trade_route.rb` | May associate with wormholes |
| `galaxy_game/app/models/ship.rb` | May use wormholes for travel |
| `galaxy_game/app/controllers/wormholes_controller.rb` | API usage patterns |
| `galaxy_game/spec/models/wormhole_spec.rb` | Existing tests reveal expected behavior |

### Migration (if needed)
- [ ] No migration needed
- [ ] Migration needed: `[describe the schema change]`

---

## Implementation Steps

> 1x agents: use judgment to identify issues and prioritize fixes.
> 0x agents: follow a systematic audit approach, document every finding.

### Step 1 — Review Model Associations
Check for proper associations with related models:
- `belongs_to :origin_planet` (Planet)
- `belongs_to :destination_planet` (Planet)
- `has_many :trade_routes` (if applicable)
- Verify foreign keys match schema

### Step 2 — Audit Validations
Ensure comprehensive validations exist:
- Presence validations for required fields
- Uniqueness constraints where appropriate
- Custom validations (e.g., origin != destination)
- Numericality validations for distance/cost attributes

### Step 3 — Review Scopes & Methods
Check for useful scopes and instance methods:
- Scopes for filtering (by planet, status, etc.)
- Instance methods for calculations (travel time, cost)
- Class methods for lookups (find_between_planets)

### Step 4 — Check Callbacks
Review any before/after callbacks:
- Ensure they don't create circular dependencies
- Verify they're documented if complex

### Step 5 — Integration Test Review
Check existing specs to understand expected behavior:
- `galaxy_game/spec/models/wormhole_spec.rb`
- Look for failing or skipped tests

### Step 6 — Synthesis Report (before applying any fix)

```
SYNTHESIS REPORT
File: wormhole.rb

ISSUES FOUND
1. [Issue description] — line ~N
2. [Issue description] — line ~N

PROPOSED FIXES
[exact code changes]

RISK ASSESSMENT
[Any shared code affected, breaking changes]

READY TO APPLY? — waiting for approval
```

Do not apply fixes until the user explicitly approves.

---

## Acceptance Criteria
- [ ] All associations properly defined and documented
- [ ] Validations comprehensive and appropriate
- [ ] Useful scopes and methods present
- [ ] No failing tests introduced
- [ ] Code follows project conventions
- [ ] Review findings documented in completion report

---

## Stop Conditions — escalate to user immediately if:
- Fix causes new failures in specs you did not touch
- Root cause is in a shared concern or base class used across many specs
- A database migration is needed that wasn't anticipated
- Architectural decision required (e.g., major model restructuring)
- Fix requires changing more files than the task specifies

---

## Commit Instructions
Run git commands on **host**, not inside container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: wormhole.rb — [brief description of root cause and fix]"
git push
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update model documentation if new methods added
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none  
**Blocks**: Wormhole integration with trade/logistics systems  
**Related tasks**: [link related task files if any]

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]  
**Completion date**: YYYY-MM-DD  

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during review that weren't expected]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files reviewed] | [issues found] | [next action needed]