---
name: "Archive Critical Terrain Data Assets"
priority: HIGH
phase: 6
created: 2026-06-21
status: backlog
type: maintenance
---

# TASK: Archive Critical Terrain Data Assets

**Priority**: HIGH  
**Phase**: 6  
**Type**: maintenance/data-safety  
**Created**: 2026-06-21  

## Problem Statement

GeoTIFF terrain data (~140MB processed files) is irreplaceable if online NASA sources disappear. Need safe archival before optimization or deletion attempts.

**Current**: No archive; data at risk of loss  
**Expected**: Complete backed-up archive with restoration capability

## Acceptance Criteria

- [ ] All processed GeoTIFF files archived safely
- [ ] Archive structure and documentation complete
- [ ] SHA256 checksums created and verified
- [ ] Restoration scripts for each body working
- [ ] Processing documentation preserved
- [ ] Archive integrity tested
- [ ] Restoration validated on sample data

## Implementation Steps

1. Create data/geotiff/archive/ directory structure
2. Copy all processed GeoTIFF files to archive
3. Create archive/README.md with sources and processing
4. Document GDAL commands for recreation
5. Create restoration scripts for each body
6. Generate SHA256 checksums
7. Test restoration and verify data integrity
8. Document archive procedures for future use

## Commit Instructions

```bash
git add data/geotiff/archive/ scripts/terrain/
git commit -m "chore: archive critical terrain data with restoration procedures"
```