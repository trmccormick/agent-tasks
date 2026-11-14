# WVU Libraries Directory — Project Status & Task Tracking
**Last Updated:** 2026-07-24

---

## Project Overview
WVU Libraries Directory — Rails 7.0.8 / Ruby 3.3.2 application for managing and displaying faculty/staff directory information. Uses MySQL 8, Elasticsearch 7.17.10, Docker Compose for dev environment.

**Context**: Initial project setup in agent-tasks tracking system. Project is a library employee/department directory rebuild application with search indexing capabilities.

---

## Current Status
- **Status:** ✅ **DEV/CI ENVIRONMENT STABILIZED** — CI passing after environment alignment
- **Active Branches:** `feature/dev-environment-updates`
- **Last Session:** 2026-07-24
- **Last Update:** 2026-07-24 — CI recovered; Docker and CircleCI environment updates validated

---

## 🆕 INITIALIZED — Agent Tasks Setup (2026-07-23)

**Objective**: Create project tracking structure for WVU Libraries Directory in the agent-tasks repository.

**Actions Taken**:
- Created `projects/wvulibraries_library_directory/` folder
- Created subdirectories: `tasks/active/`, `tasks/backlog/`, `tasks/completed/`, `summaries/`
- Created `README.md` with project context (Rails 7, Ruby 3.3, MySQL, Elasticsearch)
- Created `status.md` for progress tracking

**Next Steps**:
- Add active tasks as they are assigned
- Document blockers and completed work in status.md
- Create synthesis reports in summaries/ for each session

---

## ✅ RESOLVED — Dev + CI Environment Updates (2026-07-24)

**Objective**: Align local Docker and CircleCI execution environments and stabilize failing CI runs.

**Key Changes Applied**:
- Updated MySQL to `8.4.10` in local compose and pinned CircleCI MySQL image to `cimg/mysql:8.4.10`
- Switched Selenium image to `selenium/standalone-chromium:132.0` for Apple Silicon compatibility
- Removed obsolete `version` key from `docker-compose.dev.yml`
- Removed duplicate `bundler` gem entry and added `ostruct` gem for Ruby 3.5 compatibility warning
- Updated startup flow to ensure yarn dependencies are installed at container start (`yarn install --check-files`)
- Upgraded CircleCI config to `version: 2.1` and modernized cache syntax
- Fixed CI instability by lowering Elasticsearch heap in CircleCI (`ES_JAVA_OPTS: -Xms512m -Xmx512m`)

**Outcome**:
- ✅ Local development stack builds and runs
- ✅ Database setup and seeding verified locally
- ✅ Latest CircleCI run passed after Elasticsearch memory tuning

**Notes / Follow-up**:
- Keep CI service memory settings as-is unless test workload significantly changes
- Revisit Elasticsearch service settings if future suite growth causes memory pressure
