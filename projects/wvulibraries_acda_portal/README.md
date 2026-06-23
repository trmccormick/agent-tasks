# WVU Libraries ACDA Portal — Domain Context Guide
**Last Updated**: May 18, 2026
**Populated By**: GitHub Copilot
**Status**: Current production guide — updated from hydra_acda_portal_public repository
**Source**: `hydra_acda_portal_public` repository (GitHub public repo)

> This file provides domain context for any agent working on the ACDA Portal.
> `@file` this into your Continue session before starting any ACDA task.
> The ACDA Portal is a Hydra/Rails 7 application (modernized from the legacy Rails 5.2 stack).
> See also: `samvera_hyrax.md` and `wvulibraries_knapsack.md` for related WVU Libraries projects.

---

## What the ACDA Portal Is
The **American Congress Digital Archives (ACDA) Portal** is a digital repository web application that makes congressional archives available to the public. It provides search, browse, and discovery access to digitized congressional collections contributed by partner institutions. The application is built on the **Samvera Hydra framework** with **Rails 7**, **ActiveFedora**, **Fedora Commons 6.5**, and modern background job processing via **Sidekiq**.

Key facts:
- **Production URL**: https://congressarchives.org
- **Development URL**: https://congressarchivesdev.lib.wvu.edu
- **Repository**: `hydra_acda_portal_public` (public on GitHub)
- **OAI-PMH Endpoint**: `https://congressarchives.org/catalog/oai`
- **Admin Contact**: libdev@mail.wvu.edu (development), libsys@mail.wvu.edu (production)
- **License**: Likely institutional; check LICENSE file in repo
- **Stack**: Ruby 3.3.5, Rails 7.0.8, ActiveFedora, Blacklight, Hydra-Head, Solr 9.8, Fedora 6.5, PostgreSQL 16, Redis, Memcached, Sidekiq
- **Deployment**: Dockerized; multiple services — `app` (Rails web), `workers` (Sidekiq), `redis`, `db` (PostgreSQL), `fcrepo` (Fedora), `solr`, `memcached`

---

## Repository / Project Structure
The repository uses a layered layout separating application code, Docker services, data volumes, environment configuration, and supporting tooling.

```
hydra_acda_portal_public/
├── hydra/                    # Rails 7 application (mounted at /home/hydra in container)
│   ├── app/
│   │   ├── controllers/      # CatalogController (Blacklight + OAI-PMH), Viewers (PDF, Video, Audio, Image)
│   │   ├── jobs/             # AutomaticImportJob, thumbnail/image generation jobs, validation jobs
│   │   ├── models/           
│   │   │   ├── acda.rb       # Main ActiveFedora model (core domain object)
│   │   │   ├── acda_file.rb  # File attachment wrapper
│   │   │   ├── search_builder.rb  # Blacklight query builder
│   │   │   ├── solr_document.rb   # Search result document
│   │   │   ├── concerns/     # Shared concern modules
│   │   │   ├── bulkrax/      # Bulkrax customizations (csv_entry_decorator.rb)
│   │   │   └── user.rb       # Devise user model
│   │   ├── services/         # Business logic services
│   │   ├── presenters/       # View layer helpers for formatting/display
│   │   ├── views/            # ERB templates, view partials, Blacklight customizations
│   │   ├── channels/         # ActionCable WebSocket channels
│   │   ├── components/       # ViewComponent Ruby components (e.g., Wvu::SearchBarComponent)
│   │   ├── helpers/          # Rails view helpers
│   │   ├── indexers/         # Solr indexer configuration
│   │   ├── mailers/          # ActionMailer email templates
│   │   ├── factories/        # Test factories (FactoryBot)
│   │   └── assets/           # CSS/JS/images for Sprockets
│   ├── config/
│   │   ├── routes.rb         # Blacklight + Bulkrax + custom routes (Fedora, image viewers, validation)
│   │   ├── schedule.rb       # Whenever cron schedule (daily maintenance: log clear, tmp clear, restart)
│   │   ├── sidekiq.yml       # Sidekiq queue configuration with job priorities
│   │   ├── cable.yml         # ActionCable (WebSocket) config
│   │   ├── database.yml      # PostgreSQL adapter config
│   │   ├── application.rb    # Rails app configuration (Zeitwerk autoloader, Sidekiq queue adapter)
│   │   ├── boot.rb           # Bundler bootstrap
│   │   ├── fedora.yml        # Fedora REST client config
│   │   ├── redis.yml         # Redis config
│   │   ├── solr.yml          # Solr core connection config
│   │   ├── storage.yml       # ActiveStorage config
│   │   ├── puma.rb           # Puma application server config
│   │   ├── environments/     # Environment-specific settings (development.rb, production.rb, test.rb)
│   │   ├── initializers/     # Rails initializers (gems, middleware, factories, background jobs)
│   │   └── locales/          # i18n localization strings
│   ├── import/               # Import scripts (legacy, for manual use via `bin/rails r`)
│   │   ├── import.rb         # Manual import entry point (prompts for confirmation)
│   │   ├── cron_import.rb    # Automated cron-triggered import logic (checks for MFCS exports)
│   │   ├── delete_record.rb  # Single record removal from Fedora + Solr
│   │   ├── delete_records.rb # Batch record removal (with prompt)
│   │   ├── delete_records_noprompt.rb # Batch record removal (no prompt)
│   │   ├── delete_project.rb # Full project removal
│   │   ├── destroy_tombstones.rb # Fedora tombstone cleanup
│   │   ├── explode_fcrepo_solr.rb # Re-index all Fedora objects into Solr
│   │   └── clear-sidekiq-jobs.rb # Clear stuck Sidekiq jobs
│   ├── lib/
│   │   ├── import_library.rb # Shared import helpers (ImportLibrary module) for record creation/update
│   │   ├── tasks/            # Rake tasks (e.g., url_checks:cleanup, custom indexing tasks)
│   │   ├── templates/        # Email templates or view templates
│   │   └── assets/           # Additional asset paths
│   ├── spec/                 # RSpec test suite
│   ├── test/                 # Test data and test-specific fixtures
│   ├── db/
│   │   ├── schema.rb         # PostgreSQL schema snapshot
│   │   ├── seeds.rb          # Database seed data
│   │   └── migrate/          # ActiveRecord migration files
│   ├── public/               # Static public assets (404.html, 422.html, 500.html, robots.txt)
│   ├── scripts/              # Utility shell scripts
│   ├── log/                  # Rails application log files (bind-mounted to ./data/logs)
│   ├── tmp/                  # Temporary application files (imports, exports, images, PDFs, thumbnails)
│   ├── config.ru             # Rack application entry point
│   ├── Gemfile               # Ruby gem dependencies (Rails 7, Blacklight, ActiveFedora, Sidekiq, Bulkrax, etc.)
│   ├── Gemfile.lock          # Locked gem versions (reproducible builds)
│   ├── package.json          # Node.js dependencies (Webpack, Bootstrap, etc.)
│   ├── Rakefile              # Rake task definitions
│   ├── README.md             # Project documentation
│   └── bin/                  # Rails executable scripts (rails, rake, bundle, update, yarn)
├── data/                     # Bind-mounted data volumes (git-ignored; persistent between containers)
│   ├── logs/                 # Rails logs (dev/, fcrepo/)
│   ├── bundle/               # Bundled gems cache (speeds up `bundle install`)
│   ├── node_modules/         # Node.js packages cache
│   ├── imports/              # MFCS export drops (CSV, JSON, media files)
│   ├── exports/              # Bulkrax export outputs
│   ├── images/               # Derived image assets
│   ├── thumbnails/           # Derived thumbnail images
│   ├── pdf/                  # PDF originals
│   ├── downloads/            # Temporary downloaded files
│   ├── validations/          # Validation result files (CSV uploads)
│   ├── letter_opener/        # Email preview files (dev only)
│   ├── solr/                 # Solr index data (mounted to external Solr container)
│   ├── redis/                # Redis persistence data
│   └── test_csvs/            # Test CSV files for validation and import
├── env/                      # Environment variable files (git-ignored; contains secrets)
│   ├── env.dev.hydra         # Development Rails environment variables
│   ├── env.hydra             # Production Rails environment variables
│   ├── env.dev.redis         # Development Redis variables
│   ├── env.redis             # Production Redis variables
│   ├── env.fedora            # Fedora connection config
│   └── env.solr              # Solr connection config
├── solr9-setup/              # Solr 9 collection configuration (applied at initialization)
│   ├── conf/                 # Solr config files (schema.xml, solrconfig.xml, etc.)
│   ├── core.properties       # Solr core metadata
│   └── security.json         # Solr authentication/security config
├── fcrepo/                   # Fedora configuration files (mounted into fcrepo container)
│   ├── context.xml           # Tomcat application context
│   ├── logging.properties    # Fedora logging config
│   └── tomcat-users.xml      # Fedora Tomcat user credentials
├── scripts/                  # Host-side utility scripts
│   ├── startup.sh            # Container startup script (bundle install, service startup)
│   ├── startup-solr.sh       # Solr initialization script
│   ├── cleanup.sh            # Production cleanup
│   ├── cleanup-dev.sh        # Development cleanup
│   ├── fast-reset.sh         # Fast dev reset (deletes containers, volumes, recreates)
│   └── setup.sh              # Initial project setup
├── bin/                      # Repository root scripts
│   └── git-prm               # Git utility for PR management
├── docker-compose.yml        # Production service orchestration
├── docker-compose.dev.yml    # Development service orchestration (includes web, workers, services)
├── docker-compose.dev.debug.yml # Debug variant with extra ports exposed
├── Dockerfile                # Production image build (from ruby:3.3.5)
├── Dockerfile.dev            # Development image build (with JEMalloc, relaxed ImageMagick policy)
├── Dockerfile.fcrepo         # Fedora-specific image (if used locally)
├── DEVELOPMENT.md            # Development setup guide
├── README.md                 # Project overview and getting started
├── up.sh                     # Convenience script: `docker-compose -f docker-compose.dev.yml up`
├── down.sh                   # Convenience script: `docker-compose -f docker-compose.dev.yml down`
└── check_new_release.sh      # Script to check for new software releases
```

---

## Tech Stack
| Layer | Technology | Version | Notes |
|---|---|---|---|
| **Language** | Ruby | 3.3.5 | Pinned; base image `ruby:3.3.5` |
| **Web Framework** | Rails | 7.0.8 | Modern Rails 7 stack with Zeitwerk autoloader |
| **Repository Model** | ActiveFedora | — | Hydra/Fedora abstraction layer (ORM-like) |
| **Search** | Blacklight + RSolr | — | Discovery interface; OAI-PMH support via blacklight_oai_provider |
| **Advanced Search** | Blacklight Advanced Search | — | Multi-field search builder |
| **Batch Import** | Bulkrax | — | CSV/JSON import framework (replaces legacy import scripts) |
| **Object Storage** | Fedora Commons | 6.5.1 | External REST API service; fcrepo/fcrepo:6.5.1-tomcat9 image |
| **Indexing** | Solr | 9.8.0 | External service; schema in solr9-setup/conf/; separate indices for dev/prod |
| **Relational DB** | PostgreSQL | 16 (Alpine) | Stores users, bookmarks, Rails sessions, ActiveRecord models |
| **Caching** | Redis | Alpine (latest) | Session store, Sidekiq job queue backend |
| **HTTP Cache** | Memcached | 1.6.22 (Alpine) | Fragment caching for view performance |
| **Background Jobs** | Sidekiq | — | ActiveJob adapter; 5-10 concurrent workers (config in sidekiq.yml) |
| **Job Scheduling** | Whenever gem | — | Cron-like scheduling (schedule.rb); runs inside app container |
| **Media Conversion** | ImageMagick, FFmpeg, PDFtk | — | Image generation, PDF manipulation, video processing |
| **Memory Allocator** | JEMalloc | — | LD_PRELOAD in Dockerfile.dev for improved Ruby GC performance |
| **Web Server** | Puma | ~6.4.0 | Multi-threaded application server |
| **Frontend** | Bootstrap 5.1, Sprockets | — | Asset pipeline for CSS/JS compilation |
| **Email** | LetterOpener (dev) | — | Email preview UI at `/letter_opener` in development |

---

## Domain Model
The core domain model centers on the `Acda` ActiveFedora resource, representing a single congressional archive item (document, record, collection item, etc.).

### `Acda` (app/models/acda.rb)
Inherits from `ActiveFedora::Base`. Includes `Hydra::AccessControls::Permissions`, `ImportLibrary`, `ApplicationHelper`, `ThumbnailProcessable`.

**ID Assignment**: Overrides `assign_id` to use `identifier.gsub('.', '')` — strips dots from the source identifier (e.g., MFCS `idno`) to form clean Fedora URIs.

**Callbacks**:
- `before_create :prepare_record` — Calls `format_urls` (resolve redirects, normalize external URLs) and `clear_empty_fields` (remove blank relation values).
- `after_save :handle_thumbnail_generation, unless: :skip_thumbnail_update` — Triggers thumbnail generation when `preview` or `available_*` fields change.

**Key RDF Properties** (all indexed as `:stored_searchable, :stored_sortable, :facetable` unless noted):

| Property | RDF Predicate | Type | Notes |
|---|---|---|---|
| `identifier` | `DC.identifier` | String | Primary key; used as Fedora ID; enforced unique |
| `title` | `DC.title` | String | Human-readable title |
| `date` | `DC.date` | String | Display date (e.g., "July 1, 1992") |
| `edtf` | `lib.wvu.edu/hydra/edtf` | String | EDTF-formatted date (e.g., "1992-07-01") |
| `creator` | `DC.creator` | Multi-String | Author/creator; faceted |
| `contributing_institution` | `DC.contributor` | Multi-String | Partner institution (faceted); e.g., "Robert J. Dole Institute" |
| `collection` / `collection_title` | `lib.wvu.edu/hydra/collection` | String | Collection name (faceted) |
| `record_type` | `lib.wvu.edu/hydra/recordType` | Multi-String | Item type classification |
| `language` | `DC.language` | Multi-String | ISO 639-1 codes; faceted |
| `rights` | `DC.rights` | String | Copyright/rights statement |
| `rights2` | `lib.wvu.edu/hydra/rights2` | String | Secondary/supplemental rights info |
| `alternate_identifier` | `lib.wvu.edu/hydra/alternateIdentifier` | Multi-String | External IDs, catalog numbers |
| `publisher` | `DC.publisher` | String | Publishing entity |
| `subject_topical` | `DC.subject` | Multi-String | Topical subject headings (faceted as `topic`) |
| `subject_names` | (custom) | Multi-String | Name/person subject headings (faceted as `names`) |
| `subject_policy` | (custom) | Multi-String | Policy area subject headings (faceted as `policy_area`) |
| `coverage_congress` | (custom) | String | Congress session/number (faceted as `congress`) |
| `coverage_spatial` | `DC.coverage` | Multi-String | Geographic coverage (faceted as `location_represented`) |
| `type` | `DC.type` | String | Material type (e.g., "Text", "Image", "Audio") |
| `extent` | `DC.extent` | String | Physical/digital extent (e.g., "200 pages", "45 minutes") |
| `physical_location` | (custom) | String | Archive location info (faceted as `physical_location_ssi`) |
| `available_at` | (custom) | URL String | External URL to access the original resource (e.g., Preservica, DSpace, Hyrax) |
| `available_by` | (custom) | URL String | Alternative access URL |
| `preview` | (custom) | URL String | Thumbnail/preview image URL (auto-generated from `available_at`/`available_by`) |

**URL Handling Methods** (app/models/acda.rb):
- `format_urls` — Called in `prepare_record`; normalizes external URLs, resolves redirects via `resolve_redirect`.
- `format_url(url)` — Strips query params, handles Preservica URLs.
- `generate_preview(url)` — Transforms Preservica URLs to `/download/thumbnail` endpoints; returns `nil` for downloadable/PDF content.
- `resolve_redirect(url)` — Follows HTTP redirects to get final URL.

**Thumbnail Generation** (via `ThumbnailProcessable`):
- Triggered on `after_save` if `preview`, `available_at`, or `available_by` change.
- Dispatches `GenerateImageThumbsJob` (downloads image) → `ProcessThumbnailJob` (generates derivatives) → `DownloadAndSetThumbsJob` (stores locally).

**File Attachments** (via `AcdaFile < ActiveFedora::File`):
- `image_file` — Primary display image (JPG)
- `thumbnail_file` — Thumbnail image (JPG, small)
- `pdf_file` — Original PDF document
- `audio_file` — Audio asset (MP3)
- `video_file` — Video asset (MP4)

---

## Import Pipeline
Records originate from **MFCS** (legacy), **CSV uploads** (Bulkrax), or **direct HTTP API calls** (Preservica, DSpace, Hyrax, Omeka, etc.).

### Import Methods

#### 1. Bulkrax CSV Import (Primary — Modern)
- **Entry point**: `/bulkrax/importers` UI in the Rails app
- **Flow**: Upload CSV → Parse with `BulkraxCsvEntryDecorator` → Enqueue `ImporterJob` (via Sidekiq) → Batch create/update Acda records
- **File**: `app/models/bulkrax/csv_entry_decorator.rb` customizes Bulkrax's CSV parsing for ACDA fields
- **Advantages**: Web UI, job status tracking, rollback capability, batch error handling

#### 2. Cron Import (Legacy; Disabled in Current Setup)
- **Status**: Commented out in `config/schedule.rb`
- **Historical flow**: MFCS exports → NFS mount → Control file → Conversion → `AutomaticImportJob`
- **Used when**: Manual MFCS exports with pre-processed media files

#### 3. Manual CLI Import
Run inside the `app` container:
```bash
bin/rails r import/import.rb
```
Prompts for confirmation, then processes the most recent export from `./data/imports/`.

### Job Queue Architecture (Sidekiq)
Jobs are prioritized and queued in `config/sidekiq.yml`:
- **critical** (priority 3) — User-initiated, high-priority actions
- **default** (priority 2) — Standard background work
- **import** (priority 1) — Bulkrax import jobs (lower priority; can batch)
- **export** (priority 1) — Bulkrax export jobs

**Key Job Classes** (`app/jobs/`):
- `AutomaticImportJob` — Legacy: orchestrates cron import workflow
- `ImportRecordJob` — Creates/updates individual Acda record
- `GenerateThumbsJob` — Generates image thumbnails from URL
- `GeneratePdfThumbsJob` — Extracts first page from PDF as thumbnail
- `GenerateImageThumbsJob` — Downloads and prepares image for display
- `DownloadAndSetThumbsJob` — Stores generated thumbnail locally
- `ProcessThumbnailJob` — Applies image processing (resize, format conversion)
- `DownloadDlgImageJob` — Specialized: fetches images from DLG (Georgia digital library)
- `ValidateJob` — Validates uploaded CSV before import

### `ImportLibrary` (lib/import_library.rb)
Shared module with class methods for record operations:
- `import_record(id, obj)` — Creates new `Acda` record from hash, attaches files, calls `.save`. Includes retry logic for Fedora stale resource errors.
- `update_record(record, obj)` — Updates metadata and re-attaches files on existing record.
- `modify_record(export_path, json_record)` — Transforms source JSON (MFCS/Bulkrax/external) into `Acda` attribute hash; resolves file paths.
- `create_new_record(obj)` — Instantiates and persists `Acda` with provided attributes.

---

## Blacklight / Search Configuration
Configured in [CatalogController](../app/controllers/catalog_controller.rb) via `configure_blacklight`:

### Search Configuration
- **Query fields (qf)**: identifier, date, contributing_institution, policy_area, names, topic, congress, physical_location, location_represented, type, rights, language, extent
- **Default rows**: 10 results per page
- **Facets** (alphabetically):
  - Collection (collection_title_ssi)
  - Congress (coverage_congress_ssi)
  - Contributing Institution (contributing_institution_ssi)
  - Creator (creator_ssi)
  - Date (date_ssim, with range slider: 1100 to current year +2)
  - Language (language_facet)
  - Others: publisher, record_type, rights, coverage_spatial, subject_names, subject_policy, subject_topical, physical_location

### OAI-PMH Configuration
- **Repository name**: "American Congress Digital Archives Portal"
- **Repository URL**: https://congressarchives.org/catalog/oai
- **Record prefix**: https://congressarchives.org/catalog/
- **Admin email**: libdev@mail.wvu.edu
- **Sets**: Defined by `language_facet` (OAI set filtering)
- **Record limit**: 25 per request

### Custom Routes & Pages
| Route | Purpose |
|---|---|
| `/` | Search interface (Blacklight) |
| `/catalog` | Search results |
| `/catalog/:id` | Item detail view |
| `/about` | About page |
| `/contribute` | How to contribute |
| `/partners` | Partner institutions |
| `/policies` | Access policies |
| `/educationalresources` | Educational materials |
| `/featured` | Featured collections |
| `/image/:id` | Image viewer |
| `/thumb/:id` | Thumbnail view |
| `/audio/:id` | Audio player |
| `/video/:id` | Video player |
| `/pdf/:id` | PDF viewer |
| `/validate` (GET) | CSV upload form |
| `/validate` (POST) | CSV validation results |
| `/catalog_export` | Export search results as CSV |
| `/bulkrax/importers` | Batch import management UI |
| `/sidekiq` | Job queue monitoring (admin only) |
| `/letter_opener` | Email preview (dev only) |

### Blacklight Components
- **ViewComponents**: Wvu::SearchBarComponent, Wvu::FacetGroupComponent (custom WVU-branded UI)
- **Advanced Search**: Enabled; multi-field query builder
- **Results Tools**: Export to CSV, Sort, Pagination, Per-page selector
- **Document Tools**: Bookmark, Citation (Email and SMS disabled)

---

## Docker & Services

### Docker Compose Services (Development)

#### Primary Services
| Service | Container | Image/Source | Port | Purpose |
|---|---|---|---|---|
| `web` | `acda_portal` | Dockerfile.dev | 3000:3000 | Rails application (Puma server) |
| `workers` | `sidekiq` | Dockerfile.dev | — | Background job processing (Sidekiq) |
| `db` | (auto) | postgres:16-alpine | 5432 | PostgreSQL database |
| `redis` | redis | redis:alpine | 6379 | Session store, job queue backend |
| `memcached` | memcached | memcached:1.6.22-alpine | 11211 | Fragment/HTTP caching |
| `fcrepo` | (auto) | fcrepo/fcrepo:6.5.1-tomcat9 | 8080 | Fedora Commons repository |
| `solr` | (auto) | solr:9.8.0 | 8983 | Solr search index |

#### Service Dependencies
```
web (Puma Rails server)
├── redis (job queue)
├── db (PostgreSQL)
├── fcrepo (Fedora)
├── solr (Solr)
└── workers (Sidekiq)

workers (Sidekiq background jobs)
├── redis
├── db
├── fcrepo
└── solr
```

### Key Environment Variables
Location: `env/env.dev.hydra`

| Variable | Purpose | Example |
|---|---|---|
| `RAILS_ENV` | Environment | `development` / `production` |
| `RACK_ENV` | Environment | `development` / `production` |
| `SECRET_KEY_BASE` | Rails secret key | (random 128-char hex string) |
| `DATABASE_URL` | PostgreSQL URI | `postgresql://user:pass@db:5432/acda_portal_dev` |
| `REDIS_URL` | Redis connection | `redis://redis:6379/0` |
| `FEDORA_URL` | Fedora REST API | `http://fcrepo:8080/fcrepo/rest` |
| `FEDORA_USER` | Fedora username | `fedoraAdmin` |
| `FEDORA_PASSWORD` | Fedora password | (from secrets) |
| `SOLR_URL` | Solr core URL | `http://solr:8983/solr/hydra-development` |
| `SOLR_CORE` | Solr core name | `hydra-development` |
| `BULKRAX_ENABLED` | Enable Bulkrax | `true` |
| `SIDEKIQ_CONCURRENCY` | Worker concurrency | `2` (dev) / `10` (prod) |
| `SIDEKIQ_WORKERS` | Number of Sidekiq workers | `2` (dev) / `10` (prod) |

### Container Startup Command
Inside the `app` container:
```bash
bundle install
service cron start                      # Start cron for Whenever scheduling
gem install whenever
whenever --update-crontab              # Update system crontab
bundle exec rails s -p 3000 -b 0.0.0.0  # Start Puma server
```

Sidekiq command (inside `workers` container):
```bash
bundle install
bundle exec sidekiq -C config/sidekiq.yml  # Start Sidekiq worker pool
```

### Dev vs. Prod Compose Differences
- **Dev** (`docker-compose.dev.yml`):
  - Mounts source code volumes for live reloading
  - Includes development services (LetterOpenerWeb email preview)
  - Lower resource limits (2 Sidekiq workers)
  - Dockerfile.dev with JEMalloc + relaxed ImageMagick PDF policy
  - Exposes all ports for debugging

- **Prod** (`docker-compose.yml`):
  - Read-only volumes (no live reload)
  - Production-grade resource limits (10 Sidekiq workers)
  - Dockerfile with minimal overhead
  - Restricted port exposure (3000 only via reverse proxy)

### Solr Configuration
- **Setup**: `solr9-setup/` directory contains schema, config, and security settings
- **Schema**: Defines field types, copyFields, analysis chains for English/multilingual text
- **Security**: `solr9-setup/security.json` with authentication rules
- **Data volume**: `./data/solr/` (mounted; contains indices)
- **Cores** (by environment):
  - `hydra-development` (dev)
  - `hydra-test` (test suite)
  - `hydra-production` (prod)

### Fedora Configuration
- **Service**: `fcrepo` container (Tomcat 9 + Fedora 6.5.1)
- **REST API**: http://localhost:8080/fcrepo/rest (dev)
- **Config files** (in `fcrepo/` directory):
  - `context.xml` — Tomcat application context
  - `logging.properties` — Fedora logging
  - `tomcat-users.xml` — User credentials
- **Access**: Requires Fedora username/password (from env vars)

---

## Scheduled Tasks & Maintenance

### Cron Schedule (via Whenever + cron inside container)
Location: `config/schedule.rb`

**Daily tasks** (runs at midnight):
```ruby
every 1.day do
  rake "url_checks:cleanup"               # Clean up old URL redirect cache
  command "bundle exec rake log:clear"    # Rotate Rails logs
  command "bin/rails tmp:clear"           # Clear Rails tmp/ directory
  command "bin/rails tmp:create"          # Recreate tmp/ structure
  command "bin/rails restart"             # Gracefully restart Puma
end
```

### Manual Maintenance Scripts
Run via `bin/rails r import/<script>.rb` inside the `app` container:

| Script | Purpose |
|---|---|
| `delete_record.rb` | Remove a single record by identifier (prompts for ID) |
| `delete_records.rb` | Batch remove multiple records (with confirmation prompt) |
| `delete_records_noprompt.rb` | Batch remove without confirmation (dangerous!) |
| `delete_project.rb` | Remove all records for a specific project/collection |
| `destroy_tombstones.rb` | Clean up orphaned Fedora tombstones (unreferenced deleted objects) |
| `explode_fcrepo_solr.rb` | Full re-index: exports all Fedora objects and re-indexes in Solr |
| `clear-sidekiq-jobs.rb` | Remove stuck/failed jobs from Sidekiq queue |

---

## Development Workflow

### Local Setup
```bash
# Clone repository
git clone https://github.com/WVULibraryDevelopers/hydra_acda_portal_public.git
cd hydra_acda_portal_public

# Start services
./up.sh

# Wait for services to initialize (Fedora/Solr may take 30-60 seconds)

# Access application
# Web: http://localhost:3000
# Sidekiq dashboard: http://localhost:3000/sidekiq
# Email preview: http://localhost:3000/letter_opener (dev only)
```

### Common Tasks

#### Run Tests
```bash
docker exec acda_portal bin/rspec spec/
```

#### Import Records via Web UI
1. Navigate to http://localhost:3000/bulkrax/importers
2. Create a new "CSV" importer
3. Upload CSV file with columns matching `Acda` properties
4. Review mappings (configured in `csv_entry_decorator.rb`)
5. Submit → Enqueued as `ImporterJob` in Sidekiq
6. Monitor progress at http://localhost:3000/sidekiq

#### Access Fedora REST API
```bash
curl -u fedoraAdmin:PASSWORD http://localhost:8080/fcrepo/rest/
```

#### Query Solr
```bash
curl "http://localhost:8983/solr/hydra-development/select?q=*:*&rows=10&wt=json"
```

#### View Database
```bash
docker exec -it acda_portal_db psql -U postgres -d acda_portal_dev
```

#### Debug Logs
```bash
# Rails logs
tail -f data/logs/development.log

# Sidekiq logs
tail -f data/logs/sidekiq_development.log

# Fedora logs
tail -f data/logs/fcrepo/catalina.out
```

---

## Known Setup Notes

- **Public Repository**: This is a public GitHub repository; be mindful of secrets in env vars (use `.gitignore`).
- **System Dependencies**: The `ruby:3.3.5` base image bundles ImageMagick, FFmpeg, PDFtk — do not assume they need separate installation.
- **JEMalloc**: Enabled in development (Dockerfile.dev) via `LD_PRELOAD` for improved Ruby garbage collection performance.
- **ImageMagick PDF Policy**: Explicitly relaxed in Dockerfile.dev to allow PDF read/write; required for PDF derivative generation.
- **Fedora/Solr External**: In production, Fedora and Solr are typically hosted on separate infrastructure (not in the same Docker Compose). Development uses Docker-hosted instances for convenience.
- **PostgreSQL**: Replaced MySQL; uses ActiveRecord adapter `pg`.
- **Background Jobs**: All asynchronous work now uses Sidekiq + Redis instead of inline job execution. Check `/sidekiq` dashboard for job status.
- **Email in Dev**: LetterOpener provides an in-browser email preview UI at `/letter_opener` — no actual SMTP required.
- **Production URL**: Changed from `acda.lib.wvu.edu` to `congressarchives.org` (as of 2026).
