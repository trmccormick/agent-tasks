# CCDT — Domain Context Guide
**Last Updated**: September 10, 2026
**Populated By**: GitHub Copilot
**Status**: Development — Laravel 9 PHP application with CMS import system
**Source**: WVU Libraries CCDT repository

> This file provides domain context for any agent working on the CCDT application.
> Reference this before starting CCDT tasks.

---

## What CCDT Is
**CCDT (Collection Content Data Tools)** is a Laravel 9-based PHP web application designed for managing, importing, and searching collection metadata and records. It handles:

- **Dynamic table creation** from flat-file and CMS data sources
- **CSV/TSV/TAB-delimited import** with intelligent record merging for split lines
- **Full-text search** across collections
- **Collection management** with multiple data sources (local files, CMS systems)
- **Web-based record viewing** with search highlighting and file links

Key facts:
- **Repository**: `/Users/tam0013/Documents/git/ccdt`
- **Framework**: Laravel 9.52 (PHP 8.1+)
- **Database**: MySQL 8 (Docker)
- **Testing**: PHPUnit 9.6 with Laravel BrowserKit TestCase
- **Dev Environment**: Docker Compose with volume mounts for live code reload
- **Stack**: PHP 8.1-fpm, MySQL 8, Tika server (for text extraction), Node.js (for assets)
- **Deployment**: Docker containerized development environment

---

## Repository Structure

```
ccdt/
├── ccdt/                          # Main Laravel application
│   ├── app/
│   │   ├── Adapters/
│   │   │   ├── ImportAdapter.php          # Flat-file/CMS import processor (core)
│   │   │   ├── searchIndexAdapter.php     # Search index management
│   │   │   ├── updateSearchAdapter.php    # Search result updates
│   │   ├── Helpers/
│   │   │   ├── TableHelper.php            # Dynamic table schema management
│   │   │   ├── CSVHelper.php              # CSV parsing & type detection
│   │   │   ├── CMSHelper.php              # CMS data mapping
│   │   │   ├── CollectionHelper.php       # Collection management
│   │   │   ├── CustomStringHelper.php     # String utilities & highlighting
│   │   │   ├── FileViewHelper.php         # File display & rendering
│   │   ├── Models/
│   │   │   ├── Collection.php             # Collection metadata model
│   │   │   ├── Table.php                  # Table metadata model
│   │   ├── Http/
│   │   │   ├── Controllers/               # Web controllers
│   │   │   ├── Middleware/                # Auth & validation middleware
│   │   ├── Services/                      # Business logic services
│   ├── resources/
│   │   ├── views/                         # Blade templates
│   │   ├── assets/                        # CSS/JS source
│   ├── tests/
│   │   ├── AuthTest.php                   # Authentication tests
│   │   ├── RegFormTest.php                # Registration form tests
│   │   ├── ViewTest.php                   # View rendering tests
│   │   ├── Unit/Adapters/
│   │   │   ├── ImportAdapterUnitTest.php  # Import adapter unit tests
│   │   ├── TestHelper.php                 # Test fixture factory
│   ├── routes/
│   │   ├── web.php, api.php, console.php
│   ├── config/
│   │   ├── app.php, database.php, auth.php, etc.
│   ├── database/
│   │   ├── migrations/
│   │   ├── seeds/
├── data/                          # Data & runtime storage (mounted in Docker)
│   ├── flatfiles/                 # Flat-file import source directory
│   │   ├── 50000 Sales Records.csv
│   │   ├── test.dat
│   ├── exports/                   # Exported data directory
│   ├── logs/                       # Application & import logs
│   ├── vendor/                     # Composer dependencies (mounted)
├── docker-compose.dev.yml          # Dev Docker orchestration
├── Dockerfile.dev                  # Dev PHP container definition
├── docs/
│   ├── wiki/                       # Application documentation (7 pages)
│   ├── images/                     # Screenshots & diagrams
└── config/
    ├── php.ini                     # PHP configuration
    ├── my.cnf                      # MySQL configuration
    ├── vhost.conf                  # Apache vhost config
```

---

## Key Components

### **ImportAdapter.php** (Core Import Engine)
- **Purpose**: Processes flat-file and CMS data line-by-line with intelligent row merging
- **Key Methods**:
  - `process()` — Main import loop (reads file, detects delimiter, batches inserts)
  - `prepareLine()` — 4-step algorithm: skip blanks, validate merges, pad short rows, log long rows
  - `mergeLines()` — Pure function for concatenating split record tokens
- **Split Record Handling**: When a row has fewer fields than expected, it's saved and merged with the next line if result matches expected field count
- **Known Issues**: MIME type validation too strict, tests failing (see status.md)

### **TableHelper.php** (Schema Management)
- **Purpose**: Manages dynamic table creation, field type detection, metadata insertion
- **Key Methods**:
  - `setupNewTable()` — Full table creation pipeline (schema detection, type mapping, table creation)
  - `createTable()` — Uses Laravel Schema builder with fulltext indexes
  - `crteTblInCollctn()` — Inserts table metadata record into 'tables' table
- **Recent Fix**: Removed automatic file deletion on schema errors (files should be preserved)

### **CSVHelper.php** (CSV Processing)
- **Purpose**: Tokenizes CSV/TSV/DAT files, detects delimiters, infers field types
- **Key Methods**:
  - `tknze()` — Tokenizes line by delimiter
  - `fltrTkns()` — Filters out empty/whitespace tokens
  - `determineTypes()` — Scans N lines to infer field types (numeric, string, date)
  - `schema()` — Extracts header row
- **MIME Type Validation**: Currently accepts `text/*` or `application/octet-stream`

### **Collection & Table Models**
- **Collection**: Represents a collection of records with metadata (name, isCms flag, enabled status)
  - Has many Tables relationship
  - **Recent Fix**: Added `$fillable` array for Laravel 9 mass assignment
- **Table**: Represents dynamically-created table with metadata (name, field count, collection_id)
  - Belongs to Collection relationship
  - Links to actual database table created via Schema builder

---

## Development Environment

### Docker Setup
- **docker-compose.dev.yml**: Orchestrates PHP, MySQL, Tika services
- **Volume Mounts** (for live reload):
  - `./ccdt:/var/www` — Application code (IMPORTANT: enables live code changes)
  - `./data/flatfiles:/var/www/storage/app/flatfiles` — Import source files
  - `./data/exports:/var/www/storage/exports` — Export destination
  - `./data/logs:/var/www/storage/logs` — Application logs
  - `./data/vendor:/var/www/vendor` — Composer dependencies
- **Dockerfile.dev**: NO code copy (removed `ADD ccdt /var/www` for live reload support)
  - Installs dependencies, configures PHP, MySQL client, Node.js
  - Container runs PHP-FPM on port 9000

### Code Reload Behavior (Post-Fix)
- **BEFORE**: Dockerfile had `ADD ccdt /var/www`, code copied at build time → code changes needed container rebuild
- **AFTER**: `./ccdt:/var/www` volume mount only → code changes immediately available without rebuild
- **Important**: Always edit files on host; container automatically sees changes

---

## Testing Infrastructure

### Test Files Location
- **Source**: `./data/flatfiles/` (mounted from host)
- **Test Files**:
  - `50000 Sales Records.csv` — Large CSV file for import testing (~5.9 MB)
  - `test.dat` — TAB-delimited file for split record merge testing

### Test Execution
```bash
docker exec ccdt_php vendor/bin/phpunit                    # Full suite
docker exec ccdt_php vendor/bin/phpunit --filter="ImportAdapter"  # Single test
```

### Current Test Status (as of 2026-09-10)
- **testProcessEmptyFile**: ✅ PASSING
- **testProcessFileLargeTestCSV**: ❌ FAILING (MIME type validation)
- **testProcessFileWithSplitRecord**: ❌ FAILING (MIME type validation)
- **Issue**: MIME type check rejecting test files (likely `application/octet-stream` MIME)

---

## Recent Changes (Session 2026-09-10)

### Code Improvements Applied
1. **ImportAdapter.php refactoring**:
   - mergeLines() → pure function (no instance state mutation)
   - prepareLine() → 4-step algorithm with logging
   - process() → EOF logging for unresolved rows

2. **Bug Fixes**:
   - CSVHelper MIME type check: `text/*` + `application/octet-stream` acceptable
   - TableHelper: Removed `Storage::delete()` on schema errors (preserve files)
   - Collection model: Added `$fillable = ['clctnName', 'isCms', 'isEnabled', 'cmsId']`
   - show.blade.php: Fixed @inject compilation with `app(Class::class)` syntax

3. **Infrastructure Fixes**:
   - docker-compose.dev.yml: Removed unnecessary `./data/files` mount
   - Dockerfile.dev: Removed `ADD ccdt /var/www` to enable live code reload
   - Test data files: Sourced from GitHub original branch

### Known Issues
1. **MIME Type Validation** (BLOCKER):
   - Test files being rejected by CSVHelper::createFltFleObj()
   - Currently accepts `text/*` or `application/octet-stream`
   - May need to accept any non-empty file OR investigate actual MIME types

2. **File Cleanup**: Test files correctly deleted by tearDown() (intended behavior)

3. **Dev Setup**: Fixed — code changes now immediately available in container

---

## Navigation
- **Full Status**: See `status.md` for task tracking and active issues
- **Task Management**: See `tasks/` folder for specific task files
- **Local Workflow**: Follow agent-tasks workflow (see `/Users/tam0013/Documents/git/agent-tasks/README.md`)
