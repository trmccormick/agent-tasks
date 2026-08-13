# TASK: Fix Oversized Thumbnails on Search/Browse Pages

**Status**: ACTIVE
**Priority**: MEDIUM
**Type**: bug-fix
**Created**: 2026-08-12
**Last Updated**: 2026-08-12

---

## Summary

Thumbnails from Hyku partner sites display at full size on ACDA Portal search/browse pages, breaking layout. Issue does NOT affect catalog detail pages (which scale correctly). CSS constraint needed on `.full-size-responsive` class.

---

## Context

ACDA Portal aggregates records and thumbnails from multiple partners including Hyku. Current behavior:
- **Catalog detail pages**: Thumbnails scale correctly (working)
- **Search/browse results**: Thumbnails too large, breaking grid layout (broken)
- **Root cause**: CSS class lacks `max-width` constraint

Reported by: Team during testing (2026-08-12)
Dev environment: https://congressarchivesdev.lib.wvu.edu/

---

## Issue Details

### Current Behavior
- URL example: `https://digitalhistory.lib.wvu.edu/downloads/0a88b09e-ccac-4a4e-a8ce-fcd986f46dae?file=thumbnail`
- Page: https://congressarchivesdev.lib.wvu.edu/?q=&search_field=all_fields&sort=identifier_ssi+asc
- Images have `class="full-size-responsive"` but no size constraint
- Result: Images stretch full-width on search results

### Desired Behavior
- Thumbnails should maintain consistent size on search results
- Responsive on mobile/tablet/desktop (match catalog page behavior)
- Should work for Hyku URLs and internal thumbnails

---

## Investigation Findings ✅

**File Identified**: `hydra/app/assets/stylesheets/partials/_image_wrappers.scss`

**Current CSS**:
```scss
.full-size-responsive { 
	width:100%; 
	padding:20px; 
	height:auto;
	text-align: center;
}
```

**Problem**: `width: 100%` with no `max-width` → full-width display on search results

**Proposed Fix**:
```scss
.full-size-responsive { 
	width:100%; 
	padding:20px; 
	height:auto;
	text-align: center;
	max-width: 250px;
	margin: 0 auto;
}
```

**Rationale**:
- `max-width: 250px` — constrains thumbnail to reasonable size on search results
- `margin: 0 auto` — centers the constrained image
- Maintains responsive behavior (on mobile: 100% until 250px breakpoint)

---

## Workflow

### Prerequisites
1. ACDA Portal repository: `/Users/tam0013/Documents/git/hydra_acda_portal_public`
2. Current branch: `main` (verified 2026-08-12)
3. Dev environment running with test data for visual verification

### Step-by-Step

1. **Branch Creation**
   ```bash
   cd /Users/tam0013/Documents/git/hydra_acda_portal_public
   git checkout main
   git pull origin main
   git checkout -b fix/thumbnail-sizing-search-results
   ```

2. **Apply CSS Fix**
   - File: `hydra/app/assets/stylesheets/partials/_image_wrappers.scss`
   - Update `.full-size-responsive` class (see Proposed Fix above)

3. **Local Testing** (critical step)
   - Start ACDA Portal dev environment with sample data
   - Navigate to search/browse page: `/?q=&search_field=all_fields`
   - Verify thumbnails display at appropriate size
   - Check responsive behavior: mobile (320px), tablet (768px), desktop (1366px+)
   - Test catalog detail page still works: `/catalog/[record_id]`

4. **Commit**
   ```bash
   git add hydra/app/assets/stylesheets/partials/_image_wrappers.scss
   git commit -m "fix: Constrain full-size-responsive thumbnails on search results

   Added max-width: 250px and margin: 0 auto to .full-size-responsive class.
   Prevents oversized thumbnails from partner sites (Hyku, etc.) breaking 
   search results layout while maintaining responsive design.
   
   Tested: Mobile (320px), Tablet (768px), Desktop (1366px+)
   Catalog detail pages verified unaffected."
   ```

5. **Push to Dev Branch**
   ```bash
   git push origin fix/thumbnail-sizing-search-results
   ```

6. **Create PR**
   - Target: `main` branch
   - Include visual test results in PR description
   - Link any related GitHub issues

---

## Acceptance Criteria

- [ ] Thumbnails display at max 250px width on search/browse pages
- [ ] Responsive behavior maintained (100% width on mobile until 250px breakpoint)
- [ ] Catalog detail pages unaffected
- [ ] Tested on mobile (320px), tablet (768px), desktop (1366px+)
- [ ] CSS change committed with clear message
- [ ] PR created and reviewed before merge to main

---

## Testing Checklist

Use this locally with dev environment running and sample data imported:

```
Search Results Page: https://congressarchivesdev.lib.wvu.edu/?q=&search_field=all_fields&sort=identifier_ssi+asc
- [ ] Thumbnails fit within grid (not oversized)
- [ ] Responsive on 320px (mobile)
- [ ] Responsive on 768px (tablet)
- [ ] Responsive on 1366px+ (desktop)

Catalog Detail Page: https://congressarchivesdev.lib.wvu.edu/catalog/am1414_b01_f01_0007
- [ ] Images still scale properly
- [ ] Layout unchanged

Thumbnail URLs:
- [ ] Hyku URLs (digitalhistory.lib.wvu.edu/downloads/...)
- [ ] Internal thumbnails (/thumb/...)
- [ ] Both types display correctly
```

---

## Notes

- **Do NOT merge without local testing** — CSS changes require visual verification
- The `max-width: 250px` value may be adjusted based on design preferences
  - Current catalog grid appears to use similar constraints
  - If different size preferred, update and retest
- This is CSS-only change — no JS or template modifications needed
- No database migrations or dependencies

---

## Related

- Issue reported: 2026-08-12
- Partner integration: Hyku (digitalhistory.lib.wvu.edu)
- Similar issue fixed on: [List any related catalog detail page fixes]

---

## Supervisor Notes (Human Review Only)

- Investigate reported during team testing session
- Low-risk CSS fix with clear acceptance criteria
- Ready for local dispatch once agent has dev environment available
- No architecture changes, no dependencies on other projects
