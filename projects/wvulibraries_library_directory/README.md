# WVU Libraries Directory — Domain Context Guide
**Last Updated**: 2026-07-23
**Populated By**: GitHub Copilot & Agent Tasks System
**Source**: `docs/agent/`

> This file provides context for any agent working on the WVU Libraries Directory project.
> `@file` this into your Continue session for any WVU-specific task.

---

## Agent Tasks Folder Structure (This Repository)

**This agent-tasks project folder** (`/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_library_directory/`) contains:

| Folder/File | Purpose |
|---|---|
| `README.md` | This file — project context and domain knowledge |
| `status.md` | Living document — progress tracking, completed work, active tasks, blockers |
| `tasks/active/` | Tasks currently being worked on — read the task file to understand assignment |
| `tasks/completed/` | Archived tasks organized by month — reference for patterns and decisions |
| `tasks/backlog/` | Future work — not yet assigned |
| `summaries/` | **Agent session data** — synthesis reports, test results, handoff documents |

**Key Pattern**: Agents save synthesis reports to `summaries/` folder as `SYNTHESIS-[DESCRIPTION].md` BEFORE starting work. This allows data sharing between agents without copy-pasting from chat.

**Example workflow**:
1. Agent reads task file → creates synthesis report
2. Agent saves synthesis to `summaries/SYNTHESIS-[DESCRIPTION].md`
3. Agent implements fixes, saves results to `summaries/RESULTS-[DESCRIPTION].md`
4. Next agent reads both files to understand what was done and test results
5. All summaries committed to agent-tasks repo for continuity

---

## What the Library Directory Is
The WVU Libraries Directory is a Rails application that manages and displays faculty/staff directory information for West Virginia University Libraries. It provides search, browse, and contact information capabilities for library employees and departments.

**Key aspects**:
- **Repository**: Hosted at https://github.com/wvulibraries/library_directory
- **Framework**: Rails 7.0.8 / Ruby 3.3.2
- **Database**: MySQL 8
- **Search**: Elasticsearch 7.17.10
- **Testing**: RSpec, Capybara, Selenium, SimpleCov, CircleCI, Code Climate
- **Docker**: docker-compose for dev environment (Rails, MySQL, Elasticsearch, Selenium)

---

## Repository / Project Structure
- **Core Rails Structure**:
  - `app/` — Controllers, models, views, helpers, serializers, uploaders
  - `config/` — Application config, database, routes, Elasticsearch
  - `db/` — Schema, seeds, migrations
  - `lib/` — Custom rake tasks (search indexing)
  - `exporting/` + `importing/` — Employee data import/export scripts
  - `spec/` — RSpec test suite (controllers, factories, features, models, requests, routing, views)

- **Key Components**:
  - **Elasticsearch** — Search and indexing of Rails data via callbacks; reindex via `rake search_index:all`
  - **MySQL** — Primary database with Docker-based dev setup
  - **Docker Compose** — Dev environment with Rails, MySQL, Elasticsearch, Selenium containers
  - **CircleCI** — CI/CD pipeline for test automation

- **Data Persistence**: Docker volumes (`mysql`, `search_data`) for DB and Elasticsearch data.

---

## Key Workflows
- **Run tests**: `RAILS_ENV=test bundle exec rspec`
- **Reindex search**: `rake search_index:all`
- **Import employees**: `bin/rails r importing/import-employees.rb` (inside container)
- **Export employees**: `bin/rails r exporting/export-employees.rb` (inside container)
- **Load MySQL backup**: `docker exec -i db mysql -u root -pdocker library_directory_development < ./mysql-files/{backup}.sql`

---

## Known Issues / Gotchas
- Test dependencies can be flaky inside Docker containers — try removing env vars from docker-compose if dependencies appear missing
- JavaScript is difficult to test independently
- Elasticsearch default rake tasks may index disabled models — use custom rake tasks in `lib/` instead
