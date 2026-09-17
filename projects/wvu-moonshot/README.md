# WVU Moonshot - October 2026 Event Application

**Project Context:** One-off event application for WVU Libraries' October 2026 Moonshot event (donor check-in and waiver system). Built with Ruby on Rails 8.1.3, PostgreSQL 16, Docker, Hotwire/Stimulus.js, and Tailwind CSS 4.

**Architecture:** JSONB-first flexible schema designed to handle ANY CSV structure without migrations. All donor demographic data (Foundation CSV columns) stored in jsonb `data` field.

**Key Technologies:**
- Rails 8.1.3 (built-in authentication, no Devise)
- PostgreSQL 16 (JSONB for flexible schema)
- Hotwire (Stimulus.js) + Tailwind CSS 4
- RSpec 41 tests (all passing)
- Docker & Docker Compose (dev hot-reload, production)

**Current Status:**
- Foundation CSV import working (ID, LAST NAME, PRIMARY CONSTITUENCY, CLASSOF, education fields)
- Basic check-in workflow functional
- Event date: October 2026 (ONE-OFF but architected for potential future reuse)

**Recent Work:**
- Added "ID" column mapping to DonorCsvLoader
- Documented project architecture in docs/SCHEMA_DESIGN.md and docs/ARCHITECTURE.md
- Updated README.md with accurate Docker workflow

**Outstanding Work (3 Major Features):**
1. **is_alumni Boolean Flag** - Convert PRIMARY CONSTITUENCY → searchable checkbox
2. **Education History Display** - Parse repeating columns into condensed format (Year + College)
3. **Manual Entry Form** - Quick intake form for deans, staff, "plus ones" not on CSV

---

## Architectural Decisions (FINAL - September 11, 2026)

### 1. Alumni Flag
- **Implementation:** New `is_alumni` boolean column on `graduate_records` table
- **Source:** PRIMARY CONSTITUENCY field from CSV (maps "Alumni" → true, others → false)
- **Display:** Simple checkbox in check-in workflow
- **Searchable:** Yes, used to identify alumni vs. other donors

### 2. Education History
- **Database Storage:** Keep ALL raw education fields in jsonb (INSTITUTIONNAME, CLASSOF, EDUCATIONALCOLLEGECODE, etc.)
- **Display Format:** Condensed "College Name (Year)" via `education_history` model method
- **Example:**
  ```
  Database: {
    "education_1": {
      "INSTITUTIONNAME": "West Virginia University",
      "CLASSOF": "2007",
      "EDUCATIONALCOLLEGECODE": "Business & Economics"
    }
  }
  
  Display: "Business & Economics (2007)"
  ```
- **Limit:** Up to 3 repeating education blocks per person
- **UI:** Hide extra fields from view (show only condensed format)

### 3. Manual Entry Form
- **Purpose:** Quick intake for people not on Foundation CSV (deans, staff, invitees, "plus ones")
- **Form Fields:**
  - First Name (required)
  - Last Name (required)
  - Alumni checkbox (optional)
  - Waiver checkbox (optional)
  - Education: Not included (optional, can add later)
- **CRM ID:** Left blank for manual entries (system generates internal ID)
- **Source Tracking:** Stored in database to distinguish "csv" vs "manual" entries
- **CSV Export:** Manual entries included in export (tagged as source for Foundation reconciliation)
- **Workflow Integration:** Appears in search and check-in workflow alongside CSV records

---

## Key Files & Architecture

**Models:**
- `GraduateRecord` - Main model (crm_id, first_name, surname, waiver_completed, notes, data[jsonb], is_alumni, entry_source)

**Services:**
- `DonorCsvLoader` - CSV import with flexible column mapping, will handle is_alumni conversion and education parsing

**Controllers:**
- `GraduateRecordsController` - List view, detail view, and NEW manual entry create action

**Tests:**
- `spec/services/donor_csv_loader_spec.rb` - CSV parsing tests (includes Foundation CSV format)
- `spec/models/graduate_record_spec.rb` - Model tests for is_alumni, education_history methods

---

## October 2026 Event Requirements
- [x] CSV import from Foundation data
- [ ] **is_alumni boolean field (tasks/backlog/)**
- [ ] **Education history condensed display (tasks/backlog/)**
- [ ] **Manual entry form for non-CSV people (tasks/backlog/)**
- [ ] All 41 existing tests passing with new features
- [ ] CSV export capability (including manual entries)

**Timeline:** All features must be complete by end of September for October event.

---

## Development Workflow

**Start container:**
```bash
./up.sh
docker exec moonshot sh scripts/setup.sh
```

**Access app:**
```
http://localhost:3000
```

**Run tests inside container:**
```bash
docker exec moonshot bundle exec rspec
```

**Reset dev environment:**
```bash
./down.sh
./cleanup-dev.sh
docker compose -f docker-compose.dev.yml up -d
docker exec moonshot sh scripts/setup.sh
```

---

**See:** [docs/ARCHITECTURE.md](../../docs/ARCHITECTURE.md) | [docs/SCHEMA_DESIGN.md](../../docs/SCHEMA_DESIGN.md)
