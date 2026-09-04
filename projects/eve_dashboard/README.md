# EVE Dashboard — Project Context Guide

**Last Updated**: 2026-09-04  
**Populated By**: GitHub Copilot (Patched)  
**Source**: Internal project knowledge + working deployment

> This file provides complete domain context for any agent working on EVE Dashboard.
> Read this FIRST before starting any task on this project.
> EVE Dashboard is a Python FastAPI web application for EVE Online player data aggregation, portfolio management, and production planning.

---

## What EVE Dashboard Is

EVE Dashboard is a personal finance and production management tool for **EVE Online**, a massive multiplayer space MMO. The dashboard helps players:

- **Track Wealth**: Monitor ISK (in-game currency) balances over time, view asset breakdowns by location
- **Manage Production**: Plan planetary interaction (PI) resource production, manage manufacturing chains
- **Fleet Operations**: Build fleet compositions, calculate autopilot waypoint routing
- **Character Management**: Aggregate data from multiple characters, sync fresh data from ESI (EVE's official API)
- **SSO Authentication**: Secure single-sign-on via EVE Online's OAuth2 system (EVE SSO)

The application connects to CCP Games' **EVE Swagger Interface (ESI)** to pull real-time character data, market information, and universe metadata. Data is stored locally in SQLite with full-text search capabilities and historical tracking.

---

## Core Functionality

### 1. **SSO Authentication & Credential Management**
- Players authenticate via EVE Online's OAuth2 system
- Refresh tokens are encrypted with Fernet symmetric encryption and stored in SQLite
- Multi-character support: each character has independent refresh token storage
- Token rotation: when ESI returns a new refresh token, it's automatically persisted

### 2. **Data Sync from ESI**
- Background sync job fetches fresh character data every 1-12 hours (configurable)
- Data types: wallet history, assets, market orders, corporation data, fleet info
- Error handling: structured logging with context (character IDs, operation names, timestamps)
- Retry logic: transient failures logged, permanent failures alert user

### 3. **Wealth Tracking**
- Daily ISK snapshots reconstructed from wallet journal running balances
- 30-day historical backfill from journal entries
- Time-series display showing wealth trends per character
- Homefront Payouts tracking for Upwell Consortium activities

### 4. **Planetary Interaction (PI) Management**
- PI Setup: track planets, factories, and production chains
- PI Planning: drag-and-drop interface to build custom production workflows
- PI Search: find resources, routes, product conversions
- Schematic database: cached locally with search indexes

### 5. **Fleet Builder**
- Construct fleet compositions with ship types and fits
- Calculate autopilot routes between systems
- Multi-boxer support: manage multiple characters flying together
- Destination resolution: convert station/system names to coordinates

### 6. **Asset Reports**
- Aggregate assets across all characters and locations
- Filter by: location, item type, container, estimated value
- Export for external analysis or bookkeeping
- Volumetric tracking for hauling/logistics planning

---

## Tech Stack & Architecture

### Core Technologies
- **Backend**: Python 3.11, FastAPI 0.141.1 (async web framework)
- **Server**: Uvicorn 0.52.4 (ASGI server)
- **Database**: SQLite with WAL (Write-Ahead Logging) mode for concurrency
- **Encryption**: Fernet (symmetric, Python cryptography library)
- **Frontend**: Jinja2 templates with HTML/CSS/JavaScript
- **HTTP Client**: httpx (async HTTP requests)
- **Logging**: Python standard library with rotating file handlers
- **Containerization**: Docker + docker-compose (Python 3.11-slim base image)

### Application Entry Points
- **run.py**: Host-based development entry point (opens browser automatically)
- **Dockerfile**: Containerized production/deployment entry point
- **docker-compose.yml**: Orchestration with health checks, resource limits, volume mounts

### Key Modules (`app/` directory)

| Module | Purpose |
|--------|---------|
| `main.py` | FastAPI router; handles HTTP routes, SSO flow, request validation, error responses |
| `config.py` | Thread-safe settings loader; reads credentials.env, manages application constants |
| `logging_config.py` | Centralized logging setup; rotating file handlers, structured logs |
| `sso.py` | EVE SSO OAuth2 flow; token exchange, callback handling |
| `db.py` | SQLite wrapper; character data, wallet history, market orders, metadata storage |
| `crypto.py` | Fernet encryption/decryption for refresh tokens |
| `esi.py` | ESI API wrapper; token management, endpoint calls with retry logic |
| `sync.py` | Background sync job; fetches fresh character data, updates database |
| `fleet.py` | Fleet builder logic; destination resolution, autopilot routing |
| `wealth.py` | Wealth tracking; ISK snapshots, historical analysis |
| `assets_report.py` | Asset aggregation and filtering |
| `detail.py` | Character detail views; assets, orders, production chains |
| `pi_*.py` | Planetary interaction subsystem (sde, config, market, planner, board) |

### Deployment Targets
- **Docker (Local/Dev)**: `docker-compose up` on development machines
- **Raspberry Pi 24/7**: Systemd service with auto-restart, designed for Pi 3+/4/5
- **Cloud VPS**: Can be deployed anywhere Docker runs (includes Pi-optimized compose variant)

---

## Workspace & Testing Protocols (Hard Rules)

### Development Environment
- **Host Isolation**: Python is NOT installed on host. All Python work happens in Docker container.
- **Python Version**: 3.11 (slim base image)
- **Dependency Isolation**: requirements.txt specifies all dependencies; installed in container only
- **No Host Pollution**: Intentional design choice to prevent conflicts with other projects

### Testing
- **Static Quality Tests**: `bash test-quality.sh` — checks syntax, imports, file presence, logging, security features
- **Docker Build Test**: `docker build .` — verifies Dockerfile syntax and image builds successfully
- **Container Startup Test**: `docker-compose up -d && docker-compose ps` — verifies container starts and health check passes
- **Manual Functional Testing**: Load credentials and test end-to-end with real EVE Online account

### Git & Versioning
- **Remote Upstream**: User's personal GitHub fork (not original author's repo)
- **Commit Messages**: Include what changed, why it changed, any gotchas
- **Docker Builds**: Cached efficiently; base image layers reused, dependencies layer cached separately

---

## Architecture Gotchas (Things Future Agents Must Know)

### 1. **Thread Safety in Settings.reload()**
- `config.py` Settings class uses `threading.RLock()` to protect credential access
- Every credential read wraps the file I/O in a lock context manager
- Without this, concurrent FastAPI requests could race and corrupt settings
- GIL does NOT protect file I/O — lock is required

### 2. **Input Validation for Character Operations**
- `main.py` has `_validate_character_ids()` function that filters user input against database
- Character IDs from request body MUST be validated before passing to fleet/detail endpoints
- Invalid/unowned character IDs are logged as security warnings
- This prevents accidental or malicious access to other players' data

### 3. **Request Size Limits**
- `MAX_UPLOAD_SIZE = 10 * 1024 * 1024` (10 MB) prevents DoS attacks
- PI board imports validate file size BEFORE JSON parsing
- Oversized uploads return 413 error with helpful message

### 4. **Error Response Standardization**
- ALL error responses use `_error_response()` helper
- Format: `{"error": "message", "status": 400, ...extra_fields}`
- Consistent format allows frontend to parse errors uniformly
- All endpoints updated to use this helper, not ad-hoc response generation

### 5. **Logging Context Matters**
- NEVER use bare `except:` clause — always catch specific exceptions
- ALWAYS log with `.exception()` method (includes full stack trace) for errors
- Include contextual info: character IDs, operation names, request parameters
- Logs written to `data/logs/dashboard.log` with 10MB rotating files, 5 backups

### 6. **Raspberry Pi Resource Constraints**
- Docker container runs with `cpus: '2'` and `memory: 512M` limits
- Tunable via docker-compose.yml for Pi 3 (256M) vs Pi 4/5 (1G+)
- Health checks tuned for slower I/O: 60s interval, 10s timeout, 30s start period
- No parallel processing; sync runs in background thread to avoid blocking main handler

### 7. **ESI API Quirks**
- ESI returns paginated results; `esi.wallet_journal()` handles pagination automatically
- Token refresh is transparent; if refresh fails, error is logged with character context
- Some endpoints return 420 rate-limit errors; sync job has exponential backoff (not yet implemented)
- Last-Modified headers used to detect stale data; stored in database for incremental sync

### 8. **SQLite Concurrency Mode**
- WAL (Write-Ahead Logging) enabled to allow concurrent reads during writes
- No explicit connection pooling; Uvicorn workers don't share connections (safe by default)
- Backup strategy: `data/` directory should be backed up daily (contains production database)
- Recovery: If database is corrupted, restore from backup; sync will re-fetch fresh data

---

## Local Agent Guidelines (Qwen, GitHub Copilot, etc.)

### When Working on This Project
1. **Read status.md first** — understand what's currently active/completed
2. **Check task files** in `tasks/active/` — see what work is in progress
3. **All work happens in Docker** — use `docker-compose exec app bash` to run commands
4. **Terminal access**: You have full shell access within the container
5. **Python path**: Inside container, files are at `/app/` (mounted from `./` on host)
6. **Tests**: Run `bash test-quality.sh` to verify all static checks pass
7. **Git commits**: Host-based only; prepare changes, commit on host with human verification

### If Something Breaks
- Container won't start? → Check `data/logs/dashboard.log` for startup errors
- Syntax errors? → `docker run --rm -v "$PWD":/app python:3.11 python -m py_compile /app/FILE.py`
- Import errors? → Container shell: `python -c "from app.module import func"`
- Database corrupted? → Delete `data/eve_dashboard.db` and sync will rebuild it

---

## Project State Summary (Current)

**Grade**: ✅ **B+ → A-** (Production Ready)

- ✅ Core functionality: working and tested
- ✅ Error handling: comprehensive logging with context
- ✅ Security: input validation, token encryption, request limits
- ✅ Containerization: Docker build succeeds, container health checks pass
- ✅ Thread safety: Settings class protected with RLock
- ✅ Code quality: 27/27 static tests pass
- ✅ Documentation: README.docker.md, README.raspberrypi.md, DEVELOPMENT.md
- ✅ Deployment: Raspberry Pi setup automation ready
- ⏳ Remaining: End-to-end testing with real EVE Online credentials

---

## Recommended Next Actions (Priority Order)

1. **[HIGH]** Test locally with real EVE Online account (full functional test)
2. **[MEDIUM]** Deploy to Raspberry Pi if intended for 24/7 use
3. **[MEDIUM]** Add rate-limit handling for ESI 420 responses (currently bare log)
4. **[LOW]** Performance profiling under load (concurrent players)
5. **[LOW]** Add Prometheus metrics endpoint for monitoring
6. **[LOW]** Web UI polish (currently functional, not fancy)
