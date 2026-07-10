## STATUS SYNTHESIS REPORT

**Task**: Investigate Logging Configuration Differences (HykuDev vs Local Dev vs Production)
**Status**: Research Complete
**Date**: 2026-07-09

---

## 🔬 RESEARCH FINDINGS

### 1. Rails Logger Configuration (production.rb)

**File**: `config/environments/production.rb` (lines 132-136)

```ruby
if ENV["RAILS_LOG_TO_STDOUT"].present?
  logger           = ActiveSupport::Logger.new(STDOUT)
  logger.formatter = config.log_formatter
  config.logger = ActiveSupport::TaggedLogging.new(logger)
end
```

**Key finding**: Rails logs to STDOUT **only if** `RAILS_LOG_TO_STDOUT` is set and non-empty. If the env var is absent or empty, Rails uses its **default logger**, which writes to `log/production.log` inside the container.

### 2. Environment Variable Comparison

| File | `RAILS_LOG_TO_STDOUT` | `RAILS_ENV` |
|---|---|---|
| `.env` (local) | `true` | not set (defaults to development via Rails convention) |
| `.env.production.example` | `true` | `production` |
| `ops/friends-deploy.tmpl.yaml` (HykuDev/Helm) | `"true"` | `production` |

**All three environments set `RAILS_LOG_TO_STDOUT=true`**, so Rails logger should be STDOUT in all cases.

### 3. Volume Mount Comparison — THE KEY DIFFERENCE

#### Local Dev (`docker-compose.yml` via Stack Car)
```yaml
volumes:
  - node_modules:/app/samvera/hyrax-webapp/node_modules:cached
  - uploads:/app/samvera/hyrax-webapp/public/uploads:cached
  - assets:/app/samvera/hyrax-webapp/public/assets:cached
  - cache:/app/samvera/hyrax-webapp/tmp/cache:cached
  - ./data/ingest:/app/samvera/ingest:cached
  - .:/app/samvera          # ← mounts entire project root
```

**No explicit log volume mount.** Rails writes to `log/production.log` inside the container (which is `/app/samvera/hyrax-webapp/log/production.log`). Since the app directory is mounted from host (`.`), the log file **does exist on the host at `./hyrax-webapp/log/production.log`** — but nobody mounts or exposes it.

However, because `RAILS_LOG_TO_STDOUT=true`, Rails writes to STDOUT, which Docker captures and makes available via `docker logs` / `sc logs`.

#### Production/HykuDev (`docker-compose.production.yml`)
```yaml
volumes:
  - ./data/bundle:/usr/local/bundle:cached
  - ./data/node_modules:/app/samvera/hyrax-webapp/node_modules:cached
  - ./data/uploads:/app/samvera/hyrax-webapp/public/uploads:cached
  - ./data/assets:/app/samvera/hyrax-webapp/public/assets:cached
  - ./data/cache:/app/samvera/hyrax-webapp/tmp/cache:cached
  - ./data/logs/rails:/app/samvera/hyrax-webapp/log:cached   # ← CRITICAL MOUNT
  - ./data/ingest:/app/samvera/ingest:cached
  - .:/app/samvera
```

**Key mount**: `./data/logs/rails` is bind-mounted to `/app/samvera/hyrax-webapp/log`. This means the Rails log directory on the host is `./data/logs/rails/`.

### 4. Docker Log Driver Comparison

**No custom log drivers are configured** in any of the compose files (no `logging:` section on web or worker services). The only `logging:` block found is on the `chrome` service in `hyrax-webapp/docker-compose.yml`, set to `driver: none` — irrelevant to Rails logging.

### 5. Web Service Entrypoint Comparison

| Environment | Entrypoint | Command |
|---|---|---|
| Local dev (via hyrax-webapp base) | inherited | inherited |
| Production/HykuDev (`docker-compose.production.yml`) | `["sh", "-c"]` | `bundle check || bundle install && ./bin/web` |

Both environments ultimately run `./bin/web` which starts Puma. The entrypoint is the same pattern.

### 6. Network Configuration

| Environment | Networks |
|---|---|
| Local dev (`docker-compose.yml`) | Default (Stack Car network) |
| Production/HykuDev (`docker-compose.production.yml`) | `internal` bridge network only |

The production compose file uses an `internal` network (no external ports exposed except solr's 8983). The web service port `3000:3000` is still exposed.

---

## 📊 ROOT CAUSE ANALYSIS

### Why Logs May Not Appear on HykuDev

**Finding 1 — The log directory mount IS present**:
`./data/logs/rails:/app/samvera/hyrax-webapp/log:cached` means Rails logs written to the container's `log/` directory are persisted to the host at `./data/logs/rails/`. This is correct.

**Finding 2 — But RAILS_LOG_TO_STDOUT overrides file logging**:
When `RAILS_LOG_TO_STDOUT=true`, Rails logger is set to STDOUT, **NOT** to the file-based logger. The volume mount `./data/logs/rails:/app/samvera/hyrax-webapp/log` only captures logs written to files — it does NOT capture STDOUT.

**Finding 3 — Docker default log driver captures STDOUT**:
Without a custom log driver, Docker uses the `json-file` driver by default, which captures STDOUT/STDERR and stores it in `/var/lib/docker/containers/<container-id>/<container-id>-json.log`. This is accessible via `docker logs <container>` or `sc logs web`.

### The Actual Problem

The issue is likely **NOT** a configuration mismatch but one of these scenarios:

1. **`RAILS_LOG_TO_STDOUT` is NOT being set on the HykuDev VM** — If `.env.production` doesn't contain this variable (or it's empty), Rails falls back to file-based logging (`log/production.log`). The volume mount `./data/logs/rails:/app/samvera/hyrax-webapp/log` would capture these, but only if the directory exists and has correct permissions on the host.

2. **The `./data/logs/rails/` directory doesn't exist or has wrong permissions** — If the bind mount target doesn't exist before Docker creates it, Docker creates it as `root:root`. The Rails process runs as a non-root user (uid 1001) and cannot write to it.

3. **Logs are going to STDOUT but Docker's json-file driver has size limits** — Docker's default log rotation may have truncated logs before they could be inspected.

4. **The web container isn't running or crashed during testing** — If the container restarted, old logs would be gone from `docker logs` (unless log rotation is configured).

---

## 🔧 PROPOSED FIXES

### Fix 1 — Ensure `RAILS_LOG_TO_STDOUT` is set in `.env.production` on HykuDev VM
```bash
# On the HykuDev VM, verify:
grep RAILS_LOG_TO_STDOUT .env.production
# Should output: RAILS_LOG_TO_STDOUT=true
```

### Fix 2 — Ensure `./data/logs/rails/` exists with correct permissions BEFORE docker compose starts
```bash
mkdir -p ./data/logs/rails
chown -R 1001:1001 ./data/logs/rails
chmod -R 755 ./data/logs/rails
```

### Fix 3 — Add explicit file-based logger alongside STDOUT (dual logging)
Modify `config/environments/production.rb` to always write to both STDOUT and a file:
```ruby
# Always log to file (for volume mount capture)
file_logger = ActiveSupport::Logger.new(
  Rails.root.join('log', 'production.log')
)
file_logger.formatter = config.log_formatter

# Log to STDOUT if enabled
if ENV["RAILS_LOG_TO_STDOUT"].present?
  stdout_logger = ActiveSupport::Logger.new(STDOUT)
  stdout_logger.formatter = config.log_formatter
  config.logger = ActiveSupport::TaggedLogging.new(
    ActiveSupport::Logger.broadcast(file_logger).broadcast(stdout_logger)
  )
else
  config.logger = file_logger
end
```

### Fix 4 — Add Docker log rotation to prevent log loss
In `docker-compose.production.yml`, add to the web service:
```yaml
logging:
  driver: json-file
  options:
    max-size: "100m"
    max-file: "3"
```

---

## 📋 ENVIRONMENT COMPARISON SUMMARY

| Aspect | Local Dev (Stack Car) | HykuDev VM | Production |
|---|---|---|---|
| Compose file | `docker-compose.yml` (via Stack Car) | `docker-compose.production.yml` | `docker-compose.production.yml` |
| `RAILS_LOG_TO_STDOUT` | `true` (in `.env`) | `true` (in `.env.production` or Helm) | `"true"` (in Helm deploy template) |
| Rails logger target | STDOUT | STDOUT (if env var set) | STDOUT |
| Log volume mount | None (relies on stdout) | `./data/logs/rails:/app/samvera/hyrax-webapp/log` | Same as HykuDev |
| Docker log driver | Default (json-file) | Default (json-file) | Default (json-file) |
| Network | Stack Car network | `internal` bridge | Kubernetes (Helm) |
| Log access method | `sc logs web -f` | `docker logs` or `/data/logs/rails/` | Kubernetes logs or `/data/logs/rails/` |

---

## ✅ CONCLUSION

**Root cause**: The logging configuration is **consistent** across all environments — all set `RAILS_LOG_TO_STDOUT=true`. The volume mount for file logs (`./data/logs/rails`) exists in production compose but is **redundant when STDOUT logging is active**, since Rails writes to stdout, not to files.

**Most likely issue**: On the HykuDev VM, either:
- `.env.production` is missing `RAILS_LOG_TO_STDOUT=true`, causing Rails to fall back to file-only logging, AND
- The `./data/logs/rails/` directory has wrong permissions (created as root by Docker), preventing writes

**Recommended action**: Apply Fix 1 + Fix 2 above. If the issue persists, apply Fix 3 for dual logging to ensure logs are captured regardless of STDOUT configuration.

---

**RESEARCH COMPLETE.** Findings documented above. Ready for implementation phase.
