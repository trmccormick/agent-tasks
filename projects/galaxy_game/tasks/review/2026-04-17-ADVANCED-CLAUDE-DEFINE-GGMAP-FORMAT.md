---
name: "Define GGMap Format Specification"
priority: MEDIUM
phase: phase8+
created: 2026-06-21
status: backlog
type: architecture/research
---

# TASK: Define and Document the .ggmap Map Format (Advanced/Research)

**Priority**: MEDIUM  
**Phase**: phase8+ (Cycler Arc)  
**Type**: architecture/research  
**Created**: 2026-06-21  

## Problem Statement

Galaxy Game requires a next-generation map format beyond FreeCiv/Civ4, supporting scientific, strategic, terraforming, and scenario layers. Format must enable non-destructive, hierarchical editing and support large, high-resolution datasets. No current format meets these needs; this is a foundational task for future terrain and gameplay systems.

**Current**: No .ggmap format specification exists  
**Expected**: Complete .ggmap format specification with layer definitions, data structures, and Ruby reader/writer classes

## Evidence of Incompleteness

```bash
find galaxy_game -name "*.ggmap*" 2>/dev/null | head -3
# Likely returns no results — format not defined
```

## Files to Create

| File | Purpose | Key Section |
|------|---------|-------------|
| `docs/architecture/ggmap_format_spec.md` (new) | Complete format specification | Layer definitions, schema |
| `galaxy_game/lib/ggmap.rb` (new) | Ruby reader/writer classes | Format I/O logic |
| `data/schemas/ggmap.schema.json` (new) | Hierarchical JSON schema | Validation rules |

## Acceptance Criteria

- [ ] Complete .ggmap format specification document with all layer definitions
- [ ] Data structures support scientific, strategic, and gameplay data
- [ ] Hierarchical, non-destructive layer system defined
- [ ] Ruby classes designed for reading/writing/validation
- [ ] Integration points identified in terrain service and monitor interface
- [ ] Format supports all current and planned terrain/map features
- [ ] All design decisions and research are documented

## Implementation Steps

1. **Format Specification**
   - Define header structure (magic number, version, metadata fields)
   - Specify data sections: terrain grid, elevation, biome, and all required metadata
   - Select/justify compression algorithm for large datasets
   - Document planetary parameters, creation date, author info, and extensibility hooks
2. **Hierarchical Layer System**
   - Design base (terrain/elevation), scientific, strategic, terraforming, and scenario layers
   - Specify coordinate system, resolution options, and data integrity rules
   - Ensure each layer builds non-destructively on previous layers
3. **Data Schema Design**
   - Create a hierarchical JSON schema with metadata, dimensions, and layered data
   - Define validation rules and extensibility mechanisms
4. **Implementation Planning**
   - Design Ruby reader/writer classes for .ggmap files
   - Identify integration points in terrain service, monitor interface, and map editors
   - Plan for tool support (map editor, conversion utilities)
5. **Research & Reference**
   - Review existing map formats (FreeCiv, Civ4, NASA, GIS) for best practices and pitfalls
   - Document rationale for all design decisions

## Commit Instructions

```bash
git add docs/architecture/ggmap_format_spec.md galaxy_game/lib/ggmap.rb data/schemas/ggmap.schema.json
git commit -m "docs: define .ggmap map format specification and Ruby implementation"
```
