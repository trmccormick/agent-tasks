# Samvera Hyku — Domain Context Guide
**Last Updated**: May 14, 2026
**Populated By**: GitHub Copilot
**Source**: `docs/agent/`

> This file provides domain context for any agent working on Hyku.
> `@file` this into your Continue session before starting any Hyku task.
> Hyku is a multi-tenant repository application built on Hyrax.
> See also: `agent_guides/samvera_hyrax.md` for Hyrax engine context.

---

## What Hyku Is
Hyku is an open-source, multi-tenant digital repository application built on the Samvera ecosystem. It serves as a "Hydra-in-a-Box" solution, providing a ready-to-deploy platform for institutions to manage, preserve, and share digital collections. Hyku leverages the Hyrax framework (part of Samvera) for core repository functionalities like metadata management, file storage, search, and access controls. It is designed for scalability, supporting multiple independent tenants (e.g., institutions or departments) on a single infrastructure, while allowing customization through themes, flexible metadata schemas, and integrations like IIIF for images, OAI-PMH for harvesting, and analytics tools.

Hyku was developed by the Hydra-in-a-Box Project (a collaboration between DPLA, DuraSpace, and Stanford University) under an IMLS grant. It is maintained by the Samvera community and licensed under Apache 2.0. The application emphasizes ease of deployment via Docker, Kubernetes, or AWS, making it suitable for production environments in libraries, archives, and research institutions.

---

## Multi-Tenancy Model
Hyku's multi-tenancy is implemented using the Apartment gem, which creates isolated PostgreSQL schemas for each tenant. This ensures data separation while sharing application code and infrastructure. Key aspects:

- **Tenant Creation**: Each tenant is represented by an `Account` model with a unique UUID. When created, it generates:
  - A PostgreSQL schema (via Apartment).
  - A dedicated Solr collection for indexing.
  - A Fedora container for object storage.
  - A Redis namespace for caching and jobs.
  - A `Site` singleton model for tenant-specific configuration (e.g., application name, branding).

- **Data Isolation**: Most models (e.g., works, users, roles) are scoped to the tenant's schema. Global models like `Account` and `User` (for cross-tenant auth) are excluded from Apartment scoping.

- **Tenant Switching**: The `Account#switch!` method activates the tenant's context by switching the database schema, Solr collection, Fedora endpoint, and Redis namespace. This is done dynamically based on the request's domain (e.g., `tenant.example.com`).

- **Domain-Based Routing**: Tenants are accessed via subdomains (e.g., `%{tenant}.example.com`), determined by the `HYKU_DEFAULT_HOST` env var. An admin interface (e.g., `admin-hyku.example.com`) manages tenant creation.

- **User and Role Management**: Users and roles are per-tenant. Superadmins can manage all tenants, while site admins are limited to their tenant. Authentication can be shared across tenants if configured.

- **Single-Tenant Mode**: Optional mode (`HYKU_MULTITENANT=false`) for non-multi-tenant deployments, using a single schema and domain.

This model allows efficient resource sharing (e.g., one server hosts multiple repositories) while maintaining isolation.

---

## Tech Stack
Hyku is a Ruby on Rails application layered on top of the Hyrax framework. It follows a modular, service-oriented architecture:

- **Core Framework**:
  - **Rails**: Web framework handling routing, controllers, views, and models.
  - **Hyrax**: Provides repository-specific features like work models, file uploads, indexing, and workflows. Hyku extends Hyrax with multi-tenancy and theming.
  - **Blacklight**: Search interface and Solr integration for discovery.

- **Persistence Layer**:
  - **Valkyrie/Wings**: Abstraction for data storage. Wings bridges ActiveFedora (legacy) to Valkyrie (modern). Supports Fedora 4 for metadata/objects and PostgreSQL for relational data.
  - **Solr**: Indexing and search engine, with per-tenant collections.
  - **Fedora**: RDF-based repository for digital objects, with per-tenant containers.
  - **Redis**: Caching, sessions, and background job queues (namespaced per tenant).

- **Key Components and Services**:
  - **Models**: `Account` (tenant management), `Site` (tenant config), `User`/`Role` (auth), `SolrDocument` (search results), `GenericWork` (digital objects).
  - **Controllers**: `CatalogController` (search), `Hyrax::WorksController` (CRUD for works), admin controllers for tenant management.
  - **Services**: `Bulkrax` (import/export), `Hyrax::FileSetDerivativesService` (file processing), analytics services.
  - **Initializers**: Configure Hyrax, Valkyrie, Apartment, and integrations (e.g., `config/initializers/hyrax.rb`, `apartment.rb`).
  - **Jobs**: Background processing via ActiveJob (Sidekiq by default) for indexing, derivatives, and bulk operations.
  - **Storage Adapters**: Valkyrie adapters for disk, S3, or MinIO storage.

- **Deployment Options**:
  - **Docker**: Containerized setup with `docker-compose` for local dev/prod.
  - **Kubernetes/Helm**: Charts for scalable deployments.
  - **AWS**: CloudFormation templates for EC2, RDS, etc.

- **External Integrations**: IIIF viewers, OAI-PMH endpoints, SWORD deposits, email (SMTP), and analytics.

The architecture supports both ActiveFedora (legacy) and Valkyrie (modern) persistence, with a migration path for upgrading.

**Prerequisites** (from Configuration):
- Ruby 3.3.x, Rails 7.2.x.
- PostgreSQL, Solr 8.x, Fedora 4.x, Redis.
- Optional: FITS for characterization, Tesseract for OCR, LibreOffice for derivatives.

---

## ⚠️ CRITICAL ARCHITECTURE REMINDER

**Admin domain has NO repository features.** The admin tenant (`admin-hyku.localhost.direct`) is ONLY for:
- Creating new tenants
- System-wide configuration
- Superadmin account management

**It does NOT have:**
- Works list
- Batch edit
- Collections
- File uploads
- Search
- Any repository functionality

**To test repository features (batch edit, works, collections, etc.), you MUST:**
1. Create a separate tenant via admin interface
2. Access that tenant via its subdomain (e.g., `testing-hyku.localhost.direct`)
3. Test features on the TENANT domain, never the admin domain

This is by design — admin and tenants are completely separated by Apartment gem schema isolation.

---
- **README**: Overview, getting started, links to docs/wiki.
- **Docs**: `docs/getting-started.md` (installation), `docs/configuration.md` (env vars), `docs/using-hyku.md` (usage), `docs/wiki/` (detailed guides on multi-tenancy, themes, etc.).
- **Architectural Decisions**:
  - Valkyrie adoption for future-proofing (vs. ActiveFedora).
  - Apartment for schema-based multi-tenancy (scalable, isolated).
  - UUIDs for identifiers (faster than NOIDs).
  - Queued indexing for performance.
  - M3 for metadata flexibility (UI-driven, no dev intervention).

For full details, refer to the [Hyku GitHub Wiki](https://github.com/samvera/hyku/tree/main/docs/wiki) and [Samvera Docs](https://samvera.atlassian.net/wiki/spaces/hyku).

---

## Key Concepts
- **Multi-Tenant Architecture**: Supports isolated tenants with shared infrastructure, each with its own data schema, search index, and storage.
- **Repository Management**: Ingest, store, and manage digital objects (works, filesets, collections) with support for various file types (PDFs, images, audio, video).
- **Search and Discovery**: Powered by Blacklight and Solr, offering faceted search, advanced querying, and OAI-PMH harvesting.
- **Metadata Flexibility**: Configurable metadata schemas using M3 (Machine-readable Metadata Modeling) profiles, allowing UI-based customization without code changes.
- **File Handling and Derivatives**: Automatic generation of derivatives (thumbnails, OCR, etc.) using tools like FITS and Tesseract.
- **Access Controls and Permissions**: Role-based access (e.g., site admins, superadmins) with integration for authentication via LDAP, Shibboleth, or OAuth.
- **Theming and Branding**: Customizable UI themes, logos, and appearance settings per tenant.
- **Import/Export**: Bulkrax gem for CSV, XML, and OAI-based bulk imports/exports.
- **Analytics and Reporting**: Integration with Google Analytics 4, Matomo, or custom providers for usage statistics.
- **IIIF Support**: Image viewing and annotation via IIIF manifests.
- **API and Integrations**: RESTful APIs, SWORD for deposit, and support for DOIs via DataCite.
- **Internationalization**: Multi-language support with I18n.
- **Background Processing**: Asynchronous jobs via Sidekiq or GoodJob for tasks like indexing and derivatives.

---

## WVU Deployment Context
WVU uses a customized instance of Hyku called "WVU Knapsack", which isolates WVU-specific customizations (themes, work types, authorities) from the core Hyku application. This allows transparent overrides without modifying upstream code, ensuring maintainability as Hyku updates can be pulled via git submodule.

See `agent_guides/wvulibraries_knapsack.md` for details on WVU-specific overrides, deployment, and local development setup.

---

## Stack Car — Local Development Tool
**Stack Car** (`sc`) is the standard Hyku local development orchestration tool. It is NOT a normal Docker application—it uses Traefik for DNS/TLS proxy routing and requires specific commands.

### Prerequisites
- Docker Desktop running
- Stack Car installed: `gem install stack_car`
- Stack Car proxy running: `sc proxy up`
- Verify proxy at https://traefik.localhost.direct/dashboard/#/

### Essential Stack Car Commands
| Command | Purpose |
|---------|---------|
| `sc sh` | Open an interactive shell in the web container (use this, not `docker exec bash`) |
| `sc exec <command>` | Run a command in the web container without interactive shell |
| `sc exec bundle exec rails <cmd>` | Run Rails commands (console, migrate, etc.) |
| `sc exec rake <task>` | Run Rake tasks (e.g., asset precompilation) |
| `sc logs web -f` | Watch web container logs in real-time |
| `sc up -d` | Start the stack |
| `sc down` | Stop the stack |

### Common Development Tasks

**Asset Compilation (CSS/JavaScript changes)**:
```bash
# After modifying SCSS or JavaScript
sc exec bundle exec rails assets:precompile
# Then restart the web container
sc down && sc up -d
```

**Create a Superadmin Account**:
```bash
sc exec rake hyku:superadmin:create
# Follow the prompts for email and password
```

**Rails Console**:
```bash
sc sh
# Inside the shell:
bundle exec rails console
```

**Run Migrations**:
```bash
sc exec bundle exec rails db:migrate
```

### Multi-Tenant Access
Hyku with Stack Car uses subdomain-based tenant routing via Traefik:

| URL | Purpose | What You Can Do |
|-----|---------|-----------------|
| `https://admin-hyku.localhost.direct` | **Admin/Proprietor interface only** | Create new tenants, manage accounts, configure system-wide settings. **NO works, NO batch edit, NO repository operations.** |
| `https://{tenant}-hyku.localhost.direct` | **Individual tenant** (working repository) | Create works, batch edit, upload files, search, all repository operations. **This is where you test features.** |

**Critical**: You MUST use a tenant domain (not admin domain) to access batch edit, works list, or any repository features. The admin domain only manages tenant creation.

To create a test tenant:
```bash
sc sh
# Inside shell:
bundle exec rake hyku:superadmin:create        # Create superadmin account
# Then via admin interface: admin-hyku.localhost.direct → create new tenant
# Then access tenant at: https://new-tenant-hyku.localhost.direct
```

### Important Notes
- **DO NOT use raw `docker` commands** for this setup. Always use `sc` commands.
- `.env.development` is pre-configured with Stack Car values; do not modify unless needed.
- Assets are compiled to `public/assets/` in the container; changes to SCSS require recompilation.
- Use `sc sh` for interactive work; use `sc exec` for one-off commands.
- Container restart: Use `sc down && sc up -d`, not `docker restart`.

**For WVU Knapsack**: The detailed setup and troubleshooting guide is at `/Users/tam0013/Documents/git/wvu_knapsack/HYKU_BUILD_GUIDE.md` — refer there for comprehensive Stack Car workflows and issue resolution.

---

## Default Superadmin Account

The system is pre-seeded with a default superadmin account. **You do not need to create a new user.**

| Field | Value |
|-------|-------|
| Email | `admin@example.com` |
| Password | `testing123` |

**To login:**
1. Navigate to: `https://admin-hyku.localhost.direct/users/sign_in`
2. Enter email: `admin@example.com`
3. Enter password: `testing123`
4. Create tenants and manage the system

This default account exists in both **Hyku main repo** and **WVU Knapsack** deployments.

---

## Common Workflows
- **Setup Steps**:
  1. Clone the repo: `git clone https://github.com/samvera/hyku.git`.
  2. For Docker with Stack Car: Ensure proxy is running, then run `sc up -d`.
  3. Watch logs: `sc logs web -f` until "Listening on" appears.
  4. ✅ **Use default admin account** (already seeded):
     - Email: `admin@example.com`
     - Password: `testing123`
     - Access at: `https://admin-hyku.localhost.direct/users/sign_in`
  5. Create a test tenant via admin interface, then access at `https://{tenant}-hyku.localhost.direct`
  6. For local (non-Docker): Install dependencies, run `bin/setup`, then `rails s` and background services (Solr, Fedora via wrappers).
  7. Seed database: `rails db:seed` (creates default admin user).
  8. Generate work types: `rails generate hyrax:work_resource MyWork`.
  9. Configure Hyrax (e.g., in `config/initializers/hyrax.rb`): Set paths (e.g., `config.fits_path`), enable features, register workflows.
  10. For CSS/SCSS changes: Modify, then run `sc exec bundle exec rails assets:precompile && sc down && sc up -d`.

- **Key Env Vars**:
  - `HYKU_MULTITENANT`: Enable multi-tenancy (default: true).
  - `DB_*`: Database config.
  - `SOLR_*`: Solr connection.
  - `FCREPO_*`: Fedora config.
  - `HYKU_DEFAULT_HOST`: Tenant subdomain pattern.
  - `HYRAX_FLEXIBLE`: Enable flexible metadata.
  - `REPOSITORY_S3_STORAGE`: Use S3 for files.

- **Production**: Use Kubernetes/Helm or AWS templates. Ensure SSL, backups, and monitoring.

---

## What Not To Do
- Avoid modifying core Hyku code directly; use decorators or overrides for customizations to maintain upstream compatibility.
- Do not mix tenant-scoped and global data without proper Apartment exclusion.
- Avoid hardcoded tenant logic; rely on dynamic switching via `Account#switch!`.
- Do not use legacy ActiveFedora patterns if Valkyrie is adopted; migrate to modern persistence.
- Avoid bypassing role-based permissions; always check user context in multi-tenant environments.
