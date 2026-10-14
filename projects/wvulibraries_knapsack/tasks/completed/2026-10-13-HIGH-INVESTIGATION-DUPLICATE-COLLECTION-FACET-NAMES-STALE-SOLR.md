# PRODUCTION-DUPLICATE-COLLECTION-FACET-NAMES

**Date:** 2026-10-13  
**Severity:** HIGH (Production)  
**Source:** LibAnswers ticket 19322691  
**Site:** https://digitalhistory.lib.wvu.edu  

---

## Symptom

Collection facet on the homepage left sidebar shows collections multiple times — both old and current collection names are displayed. Users see duplicate/conflicting entries in the facet list.

---

## Hypothesized Root Cause

Collection renames produced stale `member_of_collections_*` title strings on works indexed in Solr. After a collection was renamed:
- Old name remains in existing work records' Solr fields (e.g., `member_of_collections_ssim`, `member_of_collections_tesim`)
- New name is also indexed → facet shows both versions

This is a known Hyku/Hyrax pattern — renaming a Collection model object does **not** automatically update references on dependent Work records in Solr. The fix direction is to reindex affected Works.

---

## Confirm Before Acting

### Prerequisites (HARD STOP until complete)
- [ ] VM snapshot confirmed by Tracy for both production VMs (LibAnswers 19322691, Steve's request)
- **Do NOT proceed** until snapshots are verified — no reindex, no Solr deletes, no recovery work

### Investigation Steps
1. **Verify on site** — load https://digitalhistory.lib.wvu.edu and check the Collection facet for duplicate names
   - Identify which collections show duplicates (old + current)
   - Check if this is consistent across multiple collections or isolated
2. **Confirm Solr field state** — query Solr for affected Works to see `member_of_collections_*` fields contain both old and new names
3. **Identify scope** — how many collections/works are affected?

### Fix Plan (pending snapshot confirmation + investigation)
1. Identify all Work records referencing renamed Collections
2. Reindex those Work records (update Solr documents to reflect current Collection titles)
   - Hyku Commons precedent: reindex after collection rename resolves duplicate facet names
3. Verify Collection facet no longer shows duplicates on the site

---

## Unrelated Notes

- **Knapsack #11 (Facet Limiter)** — unrelated issue. The #11 work targets "More" link generation and Solr limit+1, not stale facet values. Do not conflate these two problems.
- **hykudev / HYKU-720** — separate track. hykudev is broken from a failed Hyku 7.2.0 update (zzz_temporary_hyku_version_override NameError). hykudev recovery is parallel/secondary to the prod fix unless needed for dry-running the reindex command locally first.

---

## References

- LibAnswers ticket: 19322691
- Hyku Commons precedent: collection rename → reindex (Samvera/Hyku pattern)
- Related Solr fields: `member_of_collections_ssim`, `member_of_collections_tesim`
