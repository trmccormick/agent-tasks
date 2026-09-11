# Backlog: Production Smoke Test Setup Limitations

**Date Created**: 2026-09-11  
**Priority**: Low (backlog — doesn't block current work)  
**Status**: ⏳ **BACKLOG**

---

## Problem

`sh up.prod.local.sh` (production smoke test) cannot fully validate the IIIF viewer and related HTTPS-dependent features because:

1. **No HTTPS by default** — smoke test uses `docker-compose.local.yml` which runs on plain HTTP
2. **IIIF viewer requires HTTPS** — the viewer component fails to load/function without SSL
3. **Limited test coverage** — cannot verify production-like behavior for HTTPS-dependent features
4. **Workaround missing** — unlike Stack Car (which uses Traefik + `localhost.direct`), the smoke test has no built-in HTTPS proxy

---

## Context

- **Stack Car** (`up.sc.local.sh`): Works fine — uses Traefik proxy + `localhost.direct` domain for HTTPS
- **Smoke Test** (`up.prod.local.sh`): Pulls pre-built GHCR images for quick validation, but lacks HTTPS setup
- **Original Intent**: Smoke test should mirror production config on Mac before VM deployment, but HTTPS limitation prevents full validation

---

## Potential Solutions

### Option 1: Add Traefik to Production Smoke Test
- Reuse the Traefik setup from Stack Car for `docker-compose.local.yml`
- Would require updates to `docker-compose.local.yml`
- Trade-off: Adds complexity to smoke test; might not be worth it for occasional testing

### Option 2: Document HTTPS Limitation
- Add note to README about what smoke test can/cannot validate
- Direct users to Stack Car for full feature testing
- Minimal change; acknowledges the constraint

### Option 3: Create HTTPS-Aware Smoke Test Variant
- Add new compose file (`docker-compose.local-https.yml`)
- Include Traefik + certificate setup
- More work, but provides true production-like testing

---

## Recommendation

**Option 2 (documentation)** for now — lowest effort, highest clarity. Consider **Option 1** (add Traefik) only if smoke test validation becomes critical path for deployment.

---

## Related Files

- `up.prod.local.sh` — Current smoke test script
- `docker-compose.local.yml` — Smoke test compose file (no HTTPS)
- `docker-compose.yml` / Stack Car setup — Has Traefik for reference

---

## Acceptance Criteria (if pursued)

- [ ] Production smoke test validates IIIF viewer without manual workarounds
- [ ] HTTPS is available at `localhost.direct` or similar in smoke test
- [ ] Documentation clearly explains what is/isn't validated in each test mode
- [ ] No regression in Stack Car setup

