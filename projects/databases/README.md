# WVU Libraries Databases — Domain Context Guide
**Last Updated**: 2026-07-24
**Populated By**: GitHub Copilot & Agent Tasks System

> This file provides context for any agent working on the WVU Libraries Databases project.
> `@file` this into your session for any task related to this repo.

---

## Agent Tasks Folder Structure (This Repository)

**This agent-tasks project folder** (`/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_databases/`) contains:

| Folder/File | Purpose |
|---|---|
| `README.md` | This file — project context and domain knowledge |
| `status.md` | Living document — progress tracking, completed work, active tasks, blockers |
| `tasks/active/` | Tasks currently being worked on — read the task file to understand assignment |
| `tasks/completed/` | Archived tasks organized by month — reference for patterns and decisions |
| `tasks/backlog/` | Future work — not yet assigned |
| `summaries/` | **Agent session data** — synthesis reports, test results, handoff documents |
| `handoffs/` | Session continuity — what was done, what's next |

**Key Pattern**: Agents save synthesis reports to `summaries/` folder as `SYNTHESIS-[DESCRIPTION].md` BEFORE starting work. This allows data sharing between agents without copy-pasting from chat.

---

## What the Databases Project Is
A Ruby on Rails application for WVU Libraries that manages and indexes databases (likely a resource discovery/catalog system). It uses:

- **Framework**: Ruby on Rails
- **Database**: MySQL (with Docker support via `docker-compose`)
- **Search Indexing**: Custom rake task `search_index:database` for re-indexing
- **Testing**: RSpec + ShouldaMatchers
- **Documentation**: YARD (`yard doc`)
- **Authentication**: CAS (Central Authentication Service)
- **CAPTCHA**: Google reCAPTCHA v2

---

## Repository / Project Structure
- **Repository**: https://github.com/wvulibraries/databases
- **CI**: CircleCI
- **Docker**: `docker-compose.yml` + `docker-compose.dev.yml` for local dev
- **Key Scripts**: `up.sh`, `down.sh`, `startup.sh`, `setup.sh`, `cleanup.sh`

### Configurable Variables (`config/application.yml`)
| Variable | Purpose |
|---|---|
| Proxy URL | Proxy configuration |
| CAS Authentication | CAS server settings |
| Time Zone | Application timezone |
| Campus IP Range | WVU campus IP range for auth |
| Default HelpText / HelpURL | Fallback help content |
| Emails | Notification email addresses |

### Key Rake Tasks
```bash
rake search_index:database   # Re-index databases
```

---

## Application Setup (Docker)
1. `git pull`
2. Docker exec into app container:
   - `bundle install`
   - `bundle clean` (optional)
   - `bin/rails db:create`
   - `bin/rails db:schema:load`
   - `bin/rails db:seed` (dev testing data only)
   - `bin/rails assets:precompile`
   - `bin/rails restart`
   - `bin/rails search_index:database`
3. Load MySQL backup from host:
   ```bash
   docker exec -i db mysql -u root -pdocker databases_development < ./mysql-files/{backup}.sql
   ```

---

## Configuration Notes
- **Email**: Modify `config/environments/development.rb` and `production.rb` to point to your mail server
- **CAPTCHA**: Update `config/initializers/recaptcha.rb` with Google reCAPTCHA v2 API keys
- **Ports**: Verify port settings in environment configs match your needs

---

## Testing
```bash
RAILS_ENV=test bundle exec rspec                          # Full test suite
RAILS_ENV=test bundle exec rspec {directory_path}         # Subset of tests
RAILS_ENV=test bundle exec rspec {test_file}              # Single test file
```
