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

- **Stack Car** (`up.sc.local.sh`): Local development convenience — Traefik provides HTTPS for developer ergonomics
- **Smoke Test** (`up.prod.local.sh`): Quick validation of container images + config; runs on local Mac without infra layer
- **Production & hykudev**: nginx reverse proxy (outside containers) handles HTTPS; containers run HTTP internally
- **IIIF Viewer**: Requires HTTPS from browser's perspective, but doesn't care if it's provided by nginx (infrastructure) or container-level Traefik

---

## Root Cause

Smoke test attempts to mirror production container behavior in isolation, but **production's HTTPS is an infrastructure concern** (nginx reverse proxy), not a container concern. Replicating nginx on local Mac for a "smoke test" defeats the purpose of a quick, lightweight validation.

---

## Potential Solutions

### Option 1: Set Up Local nginx for Smoke Test
- Add local nginx reverse proxy in front of smoke test containers (mirrors production architecture)
- Trade-off: Smoke test becomes complex; no longer "smoke" — becomes full local production replica
- Not recommended: Defeats the purpose of quick, lightweight testing

### Option 2: Document HTTPS Limitation & Redirect IIIF Testing to hykudev
- Smoke test validates container images + config (HTTP-only)
- Full IIIF/HTTPS validation happens on hykudev or production (where nginx provides HTTPS)
- Clear division: smoke test = image validation; hykudev/prod = feature validation
- **Recommended**: Lowest friction, aligns with actual architecture

### Option 3: Use mkcert for Local Self-Signed HTTPS
- Add mkcert-generated certs to local Mac for smoke test
- Provides HTTPS to smoke test without nginx complexity
- Trade-off: Still not production-like (production uses nginx + real certs); adds local tooling requirement

---

## Recommendation

**Option 2 (document HTTPS limitation and testing tiers)** — This aligns with actual architecture:
- Smoke test is for validating container images + config in isolation (HTTP-only is fine for this)
- IIIF/HTTPS feature validation belongs on hykudev or production, where nginx reverse proxy is present
- Clear separation of concerns: smoke test = quick container validation; hykudev = full dev feature validation; production = live validation

**Action**: Update README.md to document testing tiers and what each covers.

---

## Related Files

- `up.prod.local.sh` — Current smoke test script
- `docker-compose.local.yml` — Smoke test compose file (no HTTPS)
- `docker-compose.yml` / Stack Car setup — Has Traefik for dev convenience

---

## Acceptance Criteria (When Addressed)

- [ ] README or TESTING.md documents three testing tiers: smoke test (containers), hykudev (dev features), production (live)
- [ ] Smoke test section clearly states: validates images + config only; HTTP-only; no infra layer
- [ ] IIIF/HTTPS testing section: direct to hykudev or production (where nginx is present)
- [ ] No changes needed to `up.prod.local.sh` or `docker-compose.local.yml`

