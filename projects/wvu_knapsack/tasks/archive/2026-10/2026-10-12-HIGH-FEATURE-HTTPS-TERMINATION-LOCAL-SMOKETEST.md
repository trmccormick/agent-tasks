---
status: backlog
resume: false
priority: HIGH
type: feature
system_domain: OTHER
mvp_alignment: OTHER
local_worker_safe: true
---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching)

- [x] Agent Dispatch Interface section is complete and accurate
- [ ] All Step 0-N instructions are clear and actionable
- [x] Architecture Gotchas are specific
- [x] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

---

## 🔴 Agent Dispatch Interface (Required)

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: <agent-tasks>/projects/wvulibraries_knapsack/tasks/backlog/[FILENAME].md
Step 0: Move this file to active/ and update status.md with progress. Create a synthesis report before coding.

Synthesis Report Required: Yes — write SYNTHESIS-HTTPS-LOCAL-SMOKETEST.md summarizing your approach, trade-offs, and any upstream references consulted.
```

---

## Problem Statement

`docker-compose.local.yml` is the local production smoke test for arm64 Macs but runs entirely over HTTP. It does not provide HTTPS/TLS termination. This means:

- IIIF viewer features fail silently or error because browsers block media from non-secure contexts
- `Secure` cookie flags are stripped by Rails in HTTP mode — cookies never persist
- Mixed-content policies prevent accurate feature validation against production behavior
- Feature testing on this stack is invalid for any HTTPS-dependent work

**Stack Car (`sc up -d`) works correctly because Traefik provides wildcard HTTPS**, but it is not a production-smoke test — it is a development proxy. The local smoke test should be able to validate production-like behavior including HTTPS.

## Objective

Add self-signed TLS (or equivalent) termination to `docker-compose.local.yml` so that the web service is accessible via **both** `https://localhost` and `http://localhost:3000`. Any IIIF viewer, media embed, or Secure cookie features will work correctly during smoke testing.

## Options for Implementation

### Option A — Add a Traefik sidecar to docker-compose.local.yml (Preferred)

Add a small Traefik service that listens on port 443 with a self-signed certificate and proxies to the existing `web` service on port 3000. This mirrors how Stack Car works but as part of the smoke test compose stack.

**Pros**:
- Matches the Stack Car architecture exactly (same tool, same behavior)
- Self-signed certs can be auto-generated or pre-generated once and committed (gitignored is fine)
- Traefik already handles routing; no custom TLS config needed per-service
- Future-proof: easy to add HTTPS for other services if needed

**Cons**:
- Browser will warn about self-signed cert (user must accept once)
- Requires generating/providing a self-signed cert pair

### Option B — Nginx reverse proxy with self-signed cert

Add an nginx service that terminates TLS and proxies to the `web` container.

**Pros**:
- Simpler than Traefik for a single-service setup
- Well-understood Docker pattern

**Cons**:
- Manual SSL configuration in nginx conf
- Less flexible than Traefik for multi-service setups

### Option C — Generate self-signed cert and enable Rails HTTPS directly

Enable `config.force_ssl` on the Rails side and generate a local self-signed cert. No proxy needed.

**Pros**:
- Zero new services — simplest docker-compose changes
- Only adds ~2 lines to the web service

**Cons**:
- More manual config management (cert generation, renewal)
- Does not match production stack pattern (VM uses reverse proxy)
- Less likely to catch HTTPS-related issues that occur in production

### Option D — Use mkcert for valid local certs

Add a `mkcert` container or entrypoint that generates locally-trusted certs. Combined with one of the options above.

**Pros**:
- No browser security warnings
- Certs are trusted by the system CA

**Cons**:
- Requires `mkcert` binary on host (not installed by default)
- Additional setup step for each developer

## Recommended Approach: Option A + mkcert where available

1. Add a Traefik sidecar service to `docker-compose.local.yml`
2. Generate a self-signed cert pair (or use mkcert if available)
3. Configure Traefik to terminate HTTPS on port 443 and proxy HTTP → 8080 → web:3000
4. Ensure the Rails app knows it's behind TLS (`X-Forwarded-Proto: https`)
5. Update the compose file header with the new purpose

## Files That Will Be Modified

1. **`docker-compose.local.yml`** — Add Traefik service, update web/worker/services to use proper forwarding headers
2. **`.env.production` (or a new `.env.traefik`)** — Cert paths if needed
3. **Potentially `config/initializers/`** — Ensure Rails trusts `X-Forwarded-*` headers (check if already configured)

## Architecture Gotchas

1. **Rails must trust forwarded headers**: Rails needs `config.action_dispatch.x_forwarded_for_headers = %w[Host X-Real-IP]` or the `reverse_proxy` initializer in Hyku/Hyrax. Verify this is already set before adding more config.

2. **Cookie secure flag**: If any initializer sets `secure: true` on cookies, it will break over HTTP. Ensure the Rails app uses `request.ssl?` checks that work behind the proxy.

3. **Self-signed cert trust**: On macOS, the cert needs to be added to the Keychain and trusted ("Always Trust"). Document this step for developers.

4. **Existing port 80 conflict**: If anything already uses host port 80 (Docker Desktop may use it), Traefik needs a different host port mapping like `443:443` only, or `8443:443`.

5. **No need for full wildcard**: Since this is local testing, a cert for `localhost` is sufficient — no wildcard needed.

## Implementation Steps

### Step 0 — Preparation
1. Run synthesis report: `SYNTHESIS-HTTPS-LOCAL-SMOKETEST.md` documenting approach choice
2. Check if Rails already trusts forwarded proxy headers (grep `x_forwarded_for`, `trust_proxy`, `forwarded_proto`)
3. Verify no local service already binds to port 80/443: `lsof -i :80 -i :443`

### Step 1 — Add Traefik Service
Add a new Traefik service to `docker-compose.local.yml`:
- Use the official `traefik:v2.10` image (or latest stable)
- Bind `443:443` host port for HTTPS
- Mount certs and traefik.toml config
- Configure entrypoint `https` with TLS cert resolver using self-signed files

### Step 2 — Generate/Provide Certs
Generate a self-signed cert pair for `localhost`:
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout traefik-selfsigned.key \
  -out traefik-selfsigned.crt \
  -subj "/CN=localhost" \
  -addext "subjectAltName=DNS:localhost,IP:127.0.0.1"
```
Place certs in a new `traefik/` directory under the project root (add to gitignore).

### Step 3 — Configure Traefik
Create a minimal `traefik.toml`:
```toml
[entryPoints]
  [entryPoints.http]
    address = ":80"
  [entryPoints.https]
    address = ":443"
    [entryPoints.https.forwardedHeaders]
      trustedIPs = ["127.0.0.1/32", "10.0.0.0/8"]

[providers.docker]
  exposedByDefault = false
  network = "docker-compose.local.yml_internal"

[tls]
  [tls.certificates]
    [[tls.certificates.certificates]]
      certFile = "/etc/traefik/certs/localhost.crt"
      keyFile = "/etc/traefik/certs/localhost.key"
```

### Step 4 — Wire Everything Together
- Bind mount certs into Traefik container
- Add `traefik.http.routers.local.rule = "Host(`localhost`)`"` label to the web service
- Configure TLS cert resolver on the router
- Ensure Rails receives `X-Forwarded-Proto: https`

### Step 5 — Update Rails Config (if needed)
Verify or add headers middleware in an initializer:
```ruby
# config/initializers/force_ssl_trust_proxy.rb
Rails.application.config.trusted_proxies = %w[10.0.0.0/8 172.16.0.0/12 192.168.0.0/16]
```

### Step 6 — Verify
- `docker compose -f docker-compose.local.yml up -d`
- Visit `https://localhost` — accept self-signed cert, confirm site loads
- Check that IIIF viewer media loads without mixed-content errors
- Confirm cookies persist with Secure flag

## Acceptance Criteria

1. ✅ `docker compose -f docker-compose.local.yml up -d` starts without errors (all services healthy)
2. ✅ `https://localhost` loads the application successfully (after accepting self-signed cert)
3. ✅ `http://localhost:3000` still works for backward compatibility
4. ✅ IIIF viewer resources load without mixed-content errors at https://localhost
5. ✅ Secure cookies persist correctly (cookies with `Secure; HttpOnly` flags are sent)
6. ✅ All existing Docker services (zoo, solr, fcrepo, db, redis, fits, worker, web, initialize_app) remain functional
7. ✅ Compose file header documents the HTTPS capability and cert trust requirement
8. ✅ Cert files are added to `.gitignore`

## Dependencies

- Self-signed cert generation (one-time step for developer setup)
- Browser must accept self-signed cert for `localhost` on first visit
- No other task is blocked by this — it's a standalone improvement to the test infrastructure

## Status
**Backlog** — Ready for planning session dispatch. Not yet moved to active/.
