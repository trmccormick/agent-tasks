# Samvera Hyku — Agent Work Notes & Discoveries

> Technical findings, gotchas, and contextual discoveries made during agent work on this project.
> Updated by agents as they discover project-specific insights.

---

## 2026-06-24: HTTP Basic Auth — Application-Level Security

**Discovery**: Testing agents encounter HTTP Basic Auth popup when accessing non-public tenants.

**Root Cause**:
- Location: `app/controllers/application_controller.rb`
- Method: `authenticate_if_needed` (lines 65-79)
- Trigger condition: `hidden?` is true (Account.is_public? == false) AND not in test mode
- Default credentials: `samvera` / `hyku`

**Technical Details**:
```ruby
def authenticate_if_needed
  return true if Rails.env.test?
  return unless (hidden? || staging?) && !api_or_pdf?
  authenticate_or_request_with_http_basic do |username, password|
    username == http_basic_auth_username && password == http_basic_auth_password
  end
end

def hidden?
  current_account.persisted? && !current_account.is_public?
end
```

**Solution for Local Development** (recommended):
Set test tenant to public in Rails console:
```bash
sc sh
bundle exec rails console

account = Account.find_by(cname: 'testing-hyku')
account.update(is_public: true)
exit
```

**Alternative Solutions**:
- Use curl with basic auth: `curl -u samvera:hyku https://testing-hyku.localhost.direct/`
- Enter credentials when prompted by browser

**Context**: This is application-level authentication (not Traefik/proxy). It's by design for production staging and hidden repositories. Future agents should check Account.is_public? status on testing tenants first before troubleshooting access issues.

---

## 2026-06-23: Batch Edit Descriptions CSS Fix Location

**Discovery**: CSS fix for batch edit descriptions labels (rendering vertically) is correctly placed in `app/assets/stylesheets/hyrax.scss`, not in Hyrax gem itself.

**Details**:
- File: `app/assets/stylesheets/hyrax.scss` (lines 53-73)
- Target: `.descriptions_display` and `#descriptions_display` CSS classes
- Rules: `display: inline !important`, `white-space: nowrap !important`
- Reasoning: Batch edit template comes from Hyrax gem; Hyku overrides via hyrax.scss

**Verification**: 
- CSS compiles successfully via `bundle exec rails assets:precompile`
- Labels render horizontally on tenant domain (verified visually on testing-hyku.localhost.direct)
- No local batch edit specs in Hyku (tests reside in Hyrax gem itself)

**Reference**: #2990, original fix #2527

---
### Batch Edit Issue #2990 — Upstream Investigation Required (2026-06-24)

**Community Feedback Summary:**
- LaRita Robinson found that arrow should be on same line as property (like prior implementation)
- Located prior fix in Hyrax: https://github.com/samvera/hyrax/commit/60807663
- Issue appears to be deeper than CSS styling in Hyku
- Edit functionality not even working in Hyrax on pg.nurax (broader problem)

**Current Status**: Hyku CSS fix on hold pending upstream investigation
**Decision**: May need to:
1. Investigate Hyrax commit 60807663 to understand what changed
2. Determine if fix should go upstream to Hyrax instead of Hyku
3. Clarify whether Edit is broken in Hyrax core or just specific deployments

**This is a teaching moment**: Sometimes what looks like a Hyku CSS problem is actually an upstream component issue. The working group's community review caught that this needs deeper investigation before merging.

---
## Stack-Aware Patching: Hyku's Role in the Dependency Chain

**Important**: Hyku is at the middle of a three-layer stack:
- **Layer 1 (Upstream)**: Hyrax gem (samvera_hyrax)
- **Layer 2 (Middle)**: Hyku application (samvera_hyku) — built on Hyrax
- **Layer 3 (Downstream)**: WVU Knapsack (wvulibraries_knapsack) — pulls Hyku as submodule

### Patch Direction Rules

**When fixing an issue:**

1. **Is it a Hyrax core bug?**
   - Fix in: `samvera_hyrax`
   - Automatically inherited by: Hyku and Knapsack (when they update gem/dependency)

2. **Is it a Hyku-specific bug?**
   - Fix in: `samvera_hyku` (this repo)
   - Automatically inherited by: Knapsack (when it updates the hyrax-webapp submodule)

3. **Is it a WVU-specific bug or enhancement?**
   - Fix in: `wvulibraries_knapsack`
   - Consider: Could this be upstreamed to Hyku or Hyrax? If yes, coordinate upstream migration later.

4. **Bug appears in Knapsack but is actually Hyku/Hyrax code?**
   - Don't duplicate the fix in Knapsack
   - Fix it upstream (Hyku/Hyrax)
   - Update Knapsack's submodule when upstream is merged

### Migration Pattern

Fixes made in downstream projects (Knapsack) can be contributed upstream for community review:

```
Fix made in Knapsack (WVU-specific or generally useful)
  ↓
Submit PR/issue to Hyku (or Hyrax if applicable)
  ↓
Community review and discussion
  ↓
If accepted: merged to Hyku/Hyrax main
  ↓
Knapsack updates submodule when ready
  ↓
Remove WVU override, inherit upstream fix (if merged)
```

**Important**: Upstreaming requires community review and acceptance. Not all Knapsack-specific fixes need to go upstream — only those that would benefit the broader Samvera ecosystem.

**Issue tracking**: Each project has its own GitHub issue tracker for feature requests and discussions:
- Hyku issues: https://github.com/samvera/hyku/issues
- Hyrax issues: https://github.com/samvera/hyrax/issues
- Knapsack issues: (internal WVU project)

### Example: Batch Edit Descriptions Fix (#2990)
- **Location**: `samvera_hyku` (this repo)
- **Why**: Bug is in Hyku's view/style rendering, not WVU-specific
- **Migration**: Knapsack automatically gets fix when it updates hyrax-webapp submodule
- **No WVU override needed**: Fix is clean upstream, no customization required

---

## Future Agent Template

When adding new discoveries, use this format:

```
## YYYY-MM-DD: [Title of Discovery]

**Discovery**: [What was found/problem encountered]

**Root Cause**: [Why it happens]

**Solution**: [How to fix or work around it]

**Context**: [Why this matters for future work]
```

Keep notes concise and actionable for future agents.
