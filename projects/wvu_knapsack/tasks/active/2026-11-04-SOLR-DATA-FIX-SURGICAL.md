# SOLR Data Fix — Surgical Procedure (Nov 4)

**Goal**: Match production version (7.1.3) → Verify bug persists → Fix Solr data → Test. Preserves all config, users, tenants.

**Current State**:
- hykudev at 7.1.0 (reverted Oct 29 snapshot)
- Production at 7.1.3 (source of truth)
- Demo tenant data (4,727 docs) in wrong collection: `c7998894-fea0-4f54-bfb7-3d4a0ae40b25` (UUID)
- Rails queries: `hydra-production` (empty)
- Result: Demo tenant search shows empty results

**Critical First Step**: **Upgrade hykudev to 7.1.3 to match production BEFORE any Solr fix**

**Expected Outcome**:
- Demo tenant search works (results display)
- Admin tenant unchanged (empty as expected)
- Can then proceed with version upgrades (7.1.3 or 7.2.x)

---

## Step 1: Upgrade hykudev to 7.1.3 (Match Production)

SSH to hykudev:
```bash
ssh tam0013@hykudev.lib.wvu.edu
cd /home/git_pulls/wvu_knapsack

# Check out main branch (production = 7.1.3)
git checkout main
git pull origin main

# Fix submodule and Gemfile.lock issue (CRITICAL)
rm -f hyrax-webapp/Gemfile.lock
git submodule update

# Verify versions
git log --oneline -1
cd hyrax-webapp
git log --oneline -1
cd ..
```

Rebuild containers:
```bash
docker compose -f docker-compose.production.yml build --no-cache web worker

# Start containers
docker compose -f docker-compose.production.yml down
docker compose -f docker-compose.production.yml up -d

# Wait for initialize_app to complete
docker compose -f docker-compose.production.yml logs -f initialize_app
# Watch for "done" message, then Ctrl+C
```

---

## Step 2: Verify 7.1.3 Upgrade Success & Bug Still Exists

**In browser** (should still show empty results at 7.1.3):
- Navigate to: `https://admin-hykudev.lib.wvu.edu/catalog`
- Empty search (no filters)
- **Expected**: Facets load BUT results are EMPTY (bug persists at 7.1.3)
- Document this observation

**Verify collections state hasn't changed**:
```bash
docker compose -f docker-compose.production.yml exec -it web bash

# Inside container
curl -s 'http://hydra:m0Nif7rNp3ZpkiKN52NA@solr:8983/solr/admin/collections?action=LIST&wt=json'
# Should still show: c7998894-fea0-4f54-bfb7-3d4a0ae40b25 and hydra-production

curl -s 'http://hydra:m0Nif7rNp3ZpkiKN52NA@solr:8983/solr/hydra-production/select?q=*:*&rows=0&wt=json' | grep numFound
# Should show: "numFound":0

curl -s 'http://hydra:m0Nif7rNp3ZpkiKN52NA@solr:8983/solr/c7998894-fea0-4f54-bfb7-3d4a0ae40b25/select?q=*:*&rows=0&wt=json' | grep numFound
# Should show: "numFound":4727
```

---

## Step 3: Delete UUID Collection (Pre-existing, no longer needed)
```bash
ssh tam0013@hykudev.lib.wvu.edu
cd /home/git_pulls/wvu_knapsack
docker compose -f docker-compose.production.yml exec -it web bash
```

Inside container, delete UUID collection:
```bash
# Delete the pre-existing UUID collection
curl -X POST \
  'http://hydra:m0Nif7rNp3ZpkiKN52NA@solr:8983/solr/admin/collections?action=DELETE&name=c7998894-fea0-4f54-bfb7-3d4a0ae40b25&wt=json'

# Verify deletion
curl -s 'http://hydra:m0Nif7rNp3ZpkiKN52NA@solr:8983/solr/admin/collections?action=LIST&wt=json'
# Should show only: "hydra-production"
```

---

## Step 2: Reindex Demo Tenant Data

Inside container, delete UUID collection:
```bash
# Delete the pre-existing UUID collection
curl -X POST \
  'http://hydra:m0Nif7rNp3ZpkiKN52NA@solr:8983/solr/admin/collections?action=DELETE&name=c7998894-fea0-4f54-bfb7-3d4a0ae40b25&wt=json'

# Verify deletion
curl -s 'http://hydra:m0Nif7rNp3ZpkiKN52NA@solr:8983/solr/admin/collections?action=LIST&wt=json'
# Should show only: "hydra-production"
```

---

## Step 4: Reindex Demo Tenant Data
```bash
exit

cd /home/git_pulls/wvu_knapsack

# Reindex (this will index demo tenant data to correct collection)
docker compose -f docker-compose.production.yml exec web \
  bundle exec rake index_works

# This will take a few minutes. Monitor progress:
# - Check logs: docker compose -f docker-compose.production.yml logs -f web
# - Or check Solr: docker compose -f docker-compose.production.yml exec web bash
#   then: curl -s 'http://hydra:m0Nif7rNp3ZpkiKN52NA@solr:8983/solr/hydra-production/select?q=*:*&rows=0&wt=json' | grep numFound
```

---

## Step 5: Verify Search Works

### Test in Browser:
- Navigate to: `https://admin-hykudev.lib.wvu.edu/catalog`
- Empty search (no filters)
- **Expected**: Facets load AND results display (should show works/collections)

### Test via Curl (inside container):
```bash
docker compose -f docker-compose.production.yml exec -it web bash

# Check how many docs indexed
curl -s 'http://hydra:m0Nif7rNp3ZpkiKN52NA@solr:8983/solr/hydra-production/select?q=*:*&rows=0&wt=json' | grep numFound
# Should show > 0

# Sample one document to verify structure
curl -s 'http://hydra:m0Nif7rNp3ZpkiKN52NA@solr:8983/solr/hydra-production/select?q=*:*&rows=1&wt=json' | head -50
```

---

## Step 6: Take Snapshot (Insurance Policy)

Once search works, contact DevOps:
- "Solr data issue fixed on 7.1.3. Please take a snapshot of hykudev for rollback insurance before 7.2.x testing."

---

## Next: Proceed to 7.2.x Testing (With Clean Data & Correct Version)

```bash
cd /home/git_pulls/wvu_knapsack
git checkout feature/hyku-v7.2.0-upgrade
rm -f hyrax-webapp/Gemfile.lock
git submodule update
# Standard deployment
```

---

## Acceptance Criteria

- [ ] Upgrade to 7.1.3 complete (matches production version)
- [ ] Bug verified to persist at 7.1.3 (empty results, facets load)
- [ ] Solr collections state verified (UUID has 4,727 docs, hydra-production has 0)
- [ ] UUID collection deleted successfully
- [ ] `rake index_works` completes successfully
- [ ] Demo tenant search shows results in browser
- [ ] Solr `hydra-production` collection has > 0 documents
- [ ] Fresh snapshot taken by DevOps
- [ ] Ready for 7.2.x testing with clean data at correct version
