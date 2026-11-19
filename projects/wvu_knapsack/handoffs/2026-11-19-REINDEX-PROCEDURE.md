# Reindex Procedure for Hyku 7.2.0 Search Results Fix

**Based on LaRita Robinson's suggestion**: Search results issue likely requires Solr reindex

**Recommended Procedure**:

## Quick Verification First (2 min)

Before reindexing, confirm whether data needs it:

```bash
# On hykudev, check if UUID collection exists (previous bug)
curl -s "http://localhost:8983/solr/admin/collections?action=LIST&wt=json" | jq '.collections'

# Count docs in both collections
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0" | jq '.response.numFound'
curl -s "http://localhost:8983/solr/uuid/select?q=*:*&rows=0" | jq '.response.numFound' 2>/dev/null || echo "UUID collection not found"

# If hydra-production empty but UUID has docs → Collection mismatch confirmed
# If hydra-production has docs but search still empty → Schema issue
```

## Reindex Command

If verification shows data issues, run reindex:

```bash
# From hykudev docker container
docker compose exec -T web bundle exec rake solr:index

# Or specific tenant:
docker compose exec -T web bundle exec rake solr:index['testing']

# Monitor progress in logs
docker compose logs web -f | grep -i index
```

## Verify Reindex Success

```bash
# Check doc count in hydra-production after reindex
curl -s "http://localhost:8983/solr/hydra-production/select?q=*:*&rows=0" | jq '.response.numFound'

# Should match local doc count
sc exec bundle exec rails runner \
  "puts Work.count"
```

## If Reindex Needed in Production Upgrade

Add to production upgrade checklist:

```bash
# After pulling latest code and rebuilding:
sh down.sh
sh up.sh
# Wait for initialization_app to complete

# Then explicitly reindex:
docker compose exec -T web bundle exec rake solr:index
docker compose exec -T web bundle exec rake solr:index['production']

# Verify search works
# Browse to: https://demo-hykudev.lib.wvu.edu/catalog?q=*
```

## Why Reindex Might Be Needed

1. **7.1.3 → 7.2.0 schema change**: Field names/types changed (e.g., _sim, _tesim fields)
2. **Collection mismatch**: Data indexed to wrong collection after upgrade
3. **Valkyrie indexer change**: New 7.2.0 uses different indexing path than 7.1.3

---

**Action**: Run quick verification first, then reindex if needed. Report results to synthesis.
