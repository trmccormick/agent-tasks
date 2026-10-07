# Samvera Hyrax — Project Status & Task Tracking
**Last Updated:** 2026-10-07

---

## Project Overview
Samvera Hyrax — flexible, extensible institutional repository framework.

---

## Current Status
- **Status:** Active — Session completed with issue #7655 fix
- **Last Session:** 2026-10-07 — Issue #7655 metadata profile version display (completed)

---

## Active Tasks
- **Fix M3 default profile for based_near (issue #7285)** — ✅ Completed & pushed as PR #7693. Fixed `display_label.default` and `view.render_term` in 4 YAML files: `config/metadata_profiles/m3_profile.yaml`, `.dassie/config/metadata_profiles/m3_profile.yaml`, `.koppie/config/metadata_profiles/m3_profile.yaml`, `spec/fixtures/files/m3_profile-allinson.yaml`.
- **Fix metadata profile version display (issue #7655)** — ✅ Completed & pushed as PR #7694. Added `profile_version` accessor to read semantic version from profile data instead of DB id. Updated export to preserve real version, fixed admin UI columns for clarity (M3 Version vs Profile Version). All 22 specs passing. Ready for review.

---

## Backlog
- To be populated from the new issue

---

## Task Order & Priority Notes
- All task management lives in `/Documents/git/agent-tasks/projects/samvera_hyrax/tasks/`
- Follow agent workflow rules as documented in agent-tasks repo

---

## Special Warnings & Conventions
- Follow all agent workflow and commit protocols as documented in projects/samvera_hyrax/README.md

---

## Session Notes
- **2026-10-07**: Fixed metadata profile version display (issue #7655, PR #7694). Root cause: `FlexibleSchema#version` returned DB primary key instead of semantic version from `profile['profile']['version']`. Added `profile_version` accessor, updated `#version` and `#title` methods, fixed export action to preserve real version (no overwrite), updated admin UI columns to clarify M3 Version vs Profile Version. Updated FlexibleSchema specs to assert correct behavior. All 22 specs passing (18 model + 4 controller). No migration needed. Branch: `fix/7655-metadata-profile-version`.
- **2026-10-07**: Issue #7410 investigation (closed). Traced full call chain from PDF upload → derivative persistence → Solr indexing. Root cause: `Frigg::Persister#save` was an empty stub, making all FileSet saves no-ops in Postgres environments (Koppie/Dassie). Produced a minimal fix and regression spec, but your coworker's PR #7691 supersedes this work. Working tree is clean — no uncommitted changes. Notes retained in `/memories/session/issue_7410_investigation.md` for reference.
- **2026-10-07**: Fixed based_near property definition in M3 default profile (issue #7285, PR #7693). Two-line fix per file: `display_label.default → blacklight.search.fields.show.based_near_label_tesim`, `view.render_term → based_near_label`. Applied to 4 YAML files across main repo and derived projects (.dassie, .koppie) plus test fixture. Branch `fix/7285-m3-based-near-profile` rebased onto main and pushed to origin.
