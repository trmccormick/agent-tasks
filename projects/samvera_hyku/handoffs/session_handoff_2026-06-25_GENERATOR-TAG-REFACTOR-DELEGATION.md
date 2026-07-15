# Handoff: Generator Tag Override Refactoring

**To**: Qwen  
**Date**: 2026-06-25  
**Branch**: fix/generator-tag-hyku-identification  
**Status**: ACTIVE TASK

---

## Context

Claude identified that our generator tag fix is overengineered: we're maintaining a full 41-line file override (`app/views/layouts/_head_tag_content.html.erb`) just to change one line of HTML meta tag output.

**Single line we're changing**:
```erb
<meta name="generator" content="Samvera Hyku <%= ::Hyku::VERSION %>" />
```

**Current solution**: Full file override at `app/views/layouts/_head_tag_content.html.erb`

**Problem**: Brittle maintenance — if Hyrax updates their partial, we won't inherit those changes.

---

## Current State

- ✅ Generator tag fix implemented (commit d9d34a85)
- ✅ RSpec tests comprehensive (15 tests, covers all cases)
- ⏳ **File structure** needs refactoring to be minimal

### Files to Review

1. [app/views/layouts/_head_tag_content.html.erb](app/views/layouts/_head_tag_content.html.erb) — 41 lines, full override
2. [spec/views/layouts/_head_tag_content.html.erb_spec.rb](spec/views/layouts/_head_tag_content.html.erb_spec.rb) — RSpec tests (keep these)

---

## Investigation Needed

**Question**: Can we achieve the generator tag override more minimally?

### Option 1: Hyrax Configuration Hook
- Investigate `Hyrax.config` — does it expose generator tag setting?
- Check Hyrax initializer patterns
- **If yes**: Replace file override with one-line config in `config/initializers/hyrax.rb`

### Option 2: Helper Method Injection
- Create `app/helpers/layouts_helper.rb` with generator tag method
- Find Hyrax hook point to inject it
- **Minimal file**: Just the helper, not the full partial

### Option 3: Monkey-Patch Initializer
- Override generator tag generation in `config/initializers/hyrax.rb`
- Pro: One file, minimal code
- Con: Monkey-patching (fragile)

### Option 4: Keep Full Override + Document
- Rationale: Rails view override is standard pattern, acceptable tradeoff
- Add comment linking to root cause (Hyrax #6397)
- Accept maintenance burden as known tech debt

---

## Decision Matrix

| Option | Lines of Code | Maintenance | CI/CD | Recommendation |
|--------|---|---|---|---|
| Config hook | 1-2 | Excellent | Easy | ✅ Best if exists |
| Helper method | 5-10 | Good | Easy | ✅ Good alternative |
| Monkey-patch | 3-5 | Fair | OK | ⚠️ Fragile |
| Keep override | 41 | OK | Easy | ⚠️ Current state |

---

## Deliverables

Choose **one** approach and implement:

### Path A: Config Hook (PREFERRED)
1. Document how to configure generator tag via `Hyrax.config`
2. Implement in `config/initializers/hyrax.rb`
3. Delete `app/views/layouts/_head_tag_content.html.erb`
4. Update RSpec tests to validate config approach
5. Commit with message: "refactor: Use Hyrax config hook for generator tag"

### Path B: Helper Method
1. Create minimal helper method
2. Document how it's injected into Hyrax partial
3. Delete full override file if possible
4. Commit with message: "refactor: Use helper method for generator tag override"

### Path C: Monkey-Patch
1. Create `config/initializers/hyrax_generator_tag.rb`
2. Add override logic (3-5 lines)
3. Delete full override file
4. Add code comment explaining why + link to Hyrax #6397
5. Commit with message: "refactor: Use initializer monkey-patch for generator tag"

### Path D: Document + Accept
1. Add maintenance comment to partial explaining tradeoff
2. Link to upstream Hyrax issue
3. Mark as known tech debt
4. Keep as-is
5. Commit with message: "docs: Document generator tag override rationale"

---

## Testing

RSpec tests already cover all scenarios:
- ✅ Generator tag renders "Samvera Hyku"
- ✅ Version is included correctly
- ✅ "Samvera Hyrax" is NOT present
- ✅ HTML5 format valid

After refactoring:
1. Run: `bundle exec rspec spec/views/layouts/_head_tag_content.html.erb_spec.rb`
2. All 15 tests must still pass
3. No changes needed to spec file (unless config hook approach changes test setup)

---

## Root Cause Reference

- **Upstream Issue**: Hyrax #6397 (Paul Walk, Nov 2023)
- **Commit**: 5eeb00929 in samvera/hyrax repo
- **Problem**: Hardcoded `<meta name="generator" content="Samvera Hyrax">`
- **Impact**: Hyku repositories reported as Hyrax in search engine metadata

---

## Token Budget Note

User is at **88% premium remaining** until reset June 30. Delegate investigation + implementation to preserve tokens. Focus on minimal, maintainable solution.

---

## Next Steps

1. Investigate which approach is feasible (start with Option 1: Hyrax config)
2. Implement chosen approach
3. Run RSpec tests to confirm still passing
4. Commit to branch `fix/generator-tag-hyku-identification`
5. Reply with summary of refactoring decision + new file structure

**Questions?** Check Hyrax documentation or previous commits for Hyrax initialization patterns.
