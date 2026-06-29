# Task: Refactor Generator Tag Override Implementation

**Status**: ACTIVE  
**Priority**: MEDIUM  
**Assignee**: Qwen  
**Date Created**: 2026-06-25  
**Branch**: fix/generator-tag-hyku-identification (rebase after this work)

## Problem

Current implementation requires a full file override at `app/views/layouts/_head_tag_content.html.erb` (41 lines) just to change one line:

```erb
<meta name="generator" content="Samvera Hyku <%= ::Hyku::VERSION %>" />
```

This creates maintenance burden: if Hyrax updates the partial, we won't inherit changes.

## Goal

Find/implement a minimal, maintainable approach to override only the generator tag without duplicating the entire partial.

## Investigation Required

1. **Check Hyrax configuration** — Does Hyrax provide a config hook for generator tag?
2. **Check Hyku architecture** — Are there existing patterns for minimal Hyrax partials overrides?
3. **Evaluate approaches**:
   - Helper method to inject correct generator tag (how to hook it?)
   - Monkey-patch in initializer (fragile but minimal)
   - Hyrax gem PR upstream (long-term solution, not immediate)
   - Keep full override but document why + add comment linking to Hyrax #6397

## Deliverables

Choose best approach and either:
- A. Implement minimal override solution, update RSpec tests
- B. Document rationale for full override + add maintenance notes
- C. Create upstream Hyrax issue/PR + interim solution

## Context

- Root cause: Paul Walk commit 5eeb00929 (Hyrax #6397) hardcoded "Samvera Hyrax" tag
- Current branch: `fix/generator-tag-hyku-identification` (commit d9d34a85)
- RSpec tests already comprehensive; focus on file structure only
- Related: generator tag incorrectly identified Hyku repositories as Hyrax

## References

- Hyrax upstream: https://github.com/samvera/hyrax/commit/5eeb00929
- Current spec: spec/views/layouts/_head_tag_content.html.erb_spec.rb
