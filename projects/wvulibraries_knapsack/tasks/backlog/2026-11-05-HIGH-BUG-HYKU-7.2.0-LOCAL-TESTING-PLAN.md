---
status: backlog
resume: false
priority: HIGH
type: bug-fix
system_domain: hyku
mvp_alignment: hyku-7.2.0-upgrade-blocker
local_worker_safe: true
---

## 🟡 DRAFT STATUS NOTE (for tasks in tasks/drafts/)

**This section applies ONLY if status: draft above.**

This task is in draft status and awaiting review approval. It has NOT yet been approved for backlog.

**Current State**: In `tasks/drafts/` — revision 2 (incorporates the 2026-11-05 independent review, disposition REVISE: submodule handling, seeding, command fixes). Needs a fresh independent review.  
**Next Step**: Review agent assessment → Human approval → Move to `tasks/backlog/` by human  
**Do NOT dispatch to implementation agents** until this task is in `tasks/backlog/` with `status: backlog`

See PLANNING_AGENT_SESSION_START.md § "Task Creation Workflow" for full procedure.

## 🟡 DRAFT REVIEW CHECKLIST (Human — before approving move from drafts/ to backlog/)

**APPLIES ONLY IF status: draft above. Use this checklist BEFORE moving task to tasks/backlog/.**

After review agent has returned assessment:

- [x] Acceptance Criteria are clear and measurable (no ambiguity)
- [x] Task scope is well-bounded (not too broad, not overlapping with active tasks)
- [x] Dependencies listed and verified against status.md (all prerequisites exist)
- [x] No contradictions in the task specification
- [x] Architecture aligns with current codebase patterns (review agent verified)
- [x] Review agent disposition is GO (not REVISE or STOP)

**Once ALL boxes checked:** Approve by moving task from `tasks/drafts/` to `tasks/backlog/` (flat; no subfolder) and changing `status: draft → status: backlog`.

---

## 🔴 CRITICAL: Task Readiness Checklist (Human — before dispatching to implementation)

**APPLIES ONLY IF status: backlog. STOP. Do not send this task to an agent until ALL boxes are checked.**

- [ ] Agent Dispatch Interface section below is complete and accurate (no placeholders)
- [ ] All Step 0-N instructions are clear and actionable (not vague)
- [ ] Synthesis report template is provided (copy/paste ready, not as example)
- [ ] No placeholder text remains in Implementation Steps
- [ ] All file paths are verified to exist
- [ ] Architecture Gotchas are specific (not generic)
- [ ] Acceptance Criteria are measurable
- [ ] Dependencies and Blocked/Blocks relationships are clear

**Task is NOT READY until all checkboxes are completed.**

---

## 🔴 Agent Dispatch Interface (Required — copy this EXACTLY to send to agent)

**This section is MANDATORY and NON-NEGOTIABLE. Do not edit, abbreviate, paraphrase, or summarize.**
Agents receive this exact text as the startup contract. Every word matters.

**One allowed substitution:** before dispatch, replace `<agent-tasks>` with the absolute path of the agent-tasks clone on the machine that will run the task (run `pwd` from the repo root). Change nothing else.

```
You are **Implementation Agent**.

Project: wvulibraries_knapsack
Task: <agent-tasks>/projects/wvulibraries_knapsack/tasks/backlog/2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md

STEP 0 — MOVE TASK FILE BEFORE ANYTHING ELSE (no exceptions):
  git mv projects/wvulibraries_knapsack/tasks/backlog/2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md \
         projects/wvulibraries_knapsack/tasks/active/2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md
  Then open the moved file and change: status: backlog → status: active
  Paste the output of both commands in chat before proceeding.
  Do NOT read the task file content, run any commands, or start synthesis until this is done.

If the task YAML has `resume: true`, read the Progress / Resume block first, continue from "Next step", and set `resume: false` when you move the file to `active/`. Do not restart the task.

LIFECYCLE: backlog → active → completed
  - Tracked file: git mv (never cp or plain mv)
  - New/untracked file: mv then git add the final path
  - Never leave stale copies in the source folder
  - Verify with: find agent-tasks/projects/wvulibraries_knapsack/tasks -name "2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md"
    Only ONE result should exist. Paste this output before committing.

READ FIRST (after Step 0), in this order:
  1. <agent-tasks>/rules/GUARDRAILS.md
  2. <agent-tasks>/projects/wvulibraries_knapsack/README.md
  3. <agent-tasks>/projects/wvulibraries_knapsack/SESSION_GUIDANCE.md (if the project has one)
  4. The task file. It holds what is specific to this task; the files above hold the standing rules.

CRITICAL: Save synthesis report as MD file to summaries folder BEFORE starting any work.
  Summaries path: <agent-tasks>/projects/wvulibraries_knapsack/summaries/
  Filename pattern: YYYY-MM-DD-[TYPE]-[SHORT-DESCRIPTION].md
  Chat is for questions only — never paste synthesis into chat (formatting breaks).

Closeout: follow SESSION_CLOSEOUT.md only.
```

**IMPORTANT: Do not modify or abbreviate the text above.**
Copy it exactly as-is when dispatching this task to an agent.
This is the startup contract — every element is required.

Everything else (details, gotchas, acceptance criteria, implementation steps) is in the sections below.
The dispatch interface above is ONLY the bootstrap instructions.

---

# TASK: Local Testing of Hyku 7.2.0 Search Bug — Stack Car Investigation
**Status**: BACKLOG  
**Priority**: HIGH  
**Type**: bug-fix  
**Created**: 2026-11-05  
**Last Updated**: 2026-11-05 (revision 2)  

---

## Local Worker Triage Report (Optional — for backlog review only)
*Filled in by local model (Qwen via GitHub Copilot custom agent config) during backlog review*
*This section is NOT sent to agents — it's for human task management only*

- **Template Conformance**: PENDING — revision 2 not yet assessed
- **MVP Alignment**: VALID — directly addresses hyku-7.2.0-upgrade blocker
- **Action Line**: PENDING independent review of revision 2

---

## Agent Assignment (Human-filled, not seen by agents)

**Assigned To**: Qwen local via Copilot (primary)
**Why This Agent**: Requires terminal access to run docker commands, Solr queries, Rails runner on Stack Car
**Local attempts before cloud**: N/A — first dispatch for this task
**Supervision Level**: Watched carefully — all agents on first dispatch of a task

> **Primary executor is always local Qwen via the GitHub Copilot custom agent config.**
> Cloud/paid agents are fallback only.

---

## Prerequisites — READ FIRST (Sequential Order)

1. **Workflow**: `/Users/tam0013/Documents/git/agent-tasks/README.md` (EXECUTOR Role section)
2. **Project Guide**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/README.md`
3. **Project Status**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/status.md`
4. **Previous Testing Strategy**: `/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/handoffs/2026-11-05-HYKU-UPGRADE-TESTING-STRATEGY.md` (context source — NOT the task to execute)
5. **This Task File**: Everything below

---

## Context

Hyku 7.2.0 upgrade symptom: the catalog search page shows "0 results" even though records appear to exist in Solr.

**Documented in the 2026-11-05 testing-strategy handoff (treat as reported, not re-verified):**
- Valkyrie indexing appears to write to a different Solr collection than the one Blacklight's catalog search reads.
- The mismatch may also exist at v7.1.3 but be masked by defaults.
- An earlier Gemfile.lock drift problem (`blacklight_advanced_search` v8.x removing constants Hyrax v7.1.3 references) was resolved by pinning. It is a SEPARATE problem — do not conflate it with the Solr collection question.

**Hypothesis (to be tested, NOT assumed):** the cause is code-level — the way `SolrEndpoint` / `Account` / `CreateSolrCollectionJob` resolve the collection ("core") name causes the indexing path and the search path to disagree. The task must also be able to conclude "not a code-level mismatch" if the evidence says so.

**Note on earlier framing:** earlier status.md / README entries described the hykudev issue as a Solr *data* issue (clean/reimport), not a code bug. The 2026-11-05 handoff proposed a code-level cause instead. This task tests that proposal; neither framing is assumed true. Record which one the evidence supports.

**Facts about the pinned Hyku source** (checked during review against `samvera/hyku@279fe062`, the submodule pin on `feature/hyku-v7.2.0-upgrade`, which is also the head of Hyku's `7.2-stable` branch; `main` pins `2c49cfbbd72466c3b8c382b43a49e0b56503b832`, which is Hyku's `v7.1.3` tag). These file/line references are PROVISIONAL until re-verified against the SHAs you record in Step 1:
- `hyrax-webapp/` is a **git submodule** (`.gitmodules`: `samvera/hyku`, branch `7.2-stable`). Its files are not tracked by the parent repo's own diffs.
- `SolrEndpoint#switch!` sets `Valkyrie::IndexingAdapter.adapters[:solr_index].connection`, `Blacklight.connection_config` and `Hyrax::SolrService` from `connection_options`; `connection_options` sets `base_options['core'] ||= switchable_options['collection']`. `url` is the Solr base URL; the collection is the separate `collection` option.
- `CreateSolrCollectionJob#perform` branches on `Hyku.single_tenant?`. In single-tenant mode the core is pre-created by the container entrypoint from the `SOLR_COLLECTION` env var and the job only points the account's SolrEndpoint at it. Multi-tenant mode names collections from `account.tenant.parameterize`.
- `solr_query_parameters_extension.rb` does NOT exist at that SHA. Do not look for it.

**Relevant Architecture Docs:**
- `SESSION_CLOSEOUT.md` — closeout procedures
- `REVIEW_AGENT_GUIDE.md` — review criteria and dispositions

---

## Critical Information for This Task

### Environment
| Item | Value | Notes |
|------|-------|-------|
| App repo | `/Users/tam0013/Documents/git/wvu_knapsack` | Run all app commands from its root |
| Solr | http://localhost:8983/solr | Local Stack Car only |
| Rails app | http://localhost:3000 | Local Stack Car only |
| Scratch folder | `$HOME/hyku-720-testing` | ALL outputs go here; removed in Step 7 |
| Compose service | `web` | Confirm with `docker compose ps --services`; if it differs, use the real name everywhere |

### Architecture Gotchas (Critical to understand BEFORE starting)

⚠️ **GOTCHA 1: The mismatch may affect BOTH versions, not just 7.2.0**
- ❌ Wrong: Assume the 7.1.3 baseline is "correct" because search returns results
- ✅ Right: At each version record WHICH collection holds documents and WHICH collection the app's search path queries. A baseline that "works" may just have both pointing at the same default.
- Why: defaults can mask a resolution bug at 7.1.3.

⚠️ **GOTCHA 2: `docker compose down --volumes` wipes the database AND Solr**
- ❌ Wrong: Expect records to survive a version switch, or compare a seeded 7.1.3 run to an empty 7.2.0 run
- ✅ Right: Run `down --volumes` before each switch, then seed the SAME data the SAME way on each version (Step 3).
- Why: contamination from old collections ruins the comparison, but an unseeded run proves nothing.

⚠️ **GOTCHA 3: `hyrax-webapp` is a submodule — `git checkout` alone does NOT switch Hyku**
- ❌ Wrong: `git checkout <branch>` and assume 7.2.0 is running
- ✅ Right: `git checkout <ref> && git submodule update --init --recursive`, then record `git submodule status hyrax-webapp` and confirm the SHA differs between the two runs.
- Why: without the submodule update both "versions" are the same code.

⚠️ **GOTCHA 4: Do not assume `main` is v7.1.3**
- ✅ Right: Step 1 verifies what `main` pins before it is used as the baseline. If it is not a 7.1.x Hyku, STOP and ask.

⚠️ **GOTCHA 5: Scratch files live on the HOST**
- ❌ Wrong: Write to `/tmp` (cleared on reboot, collides with other work) or leave files in the home folder root
- ✅ Right: Write only under `$HOME/hyku-720-testing`; copy final evidence into `handoffs/` before cleanup.

⚠️ **GOTCHA 6: `rails runner` does not run `switch!` for you**
- ❌ Wrong: Read `Blacklight.connection_config` in a bare runner and treat it as what the web process uses
- ✅ Right: Call `account.switch!` first (the provided `capture.sh` does), and treat runner output as "what the app resolves for this account".

⚠️ **GOTCHA 7: No code changes — pure investigation**
- ✅ Only read, query, diff and record. Do not edit app, submodule, config or `.env` files. Report findings; the fix strategy is a separate task.

---

## Problem Statement

After the Hyku 7.2.0 upgrade, catalog search returns "0 results" while records appear to be present in Solr. Working hypothesis: indexing and search resolve different Solr collections. This task determines, with identical controlled runs at the 7.1.3 baseline and at 7.2.0, **which collection holds the documents, which collection the app's search path queries, and which file/line decides each.**

**Current behavior**: Search page shows "0 results found" despite records in Solr
**Expected behavior**: Search returns matching documents, as at the 7.1.3 baseline

---

## Success Criteria

| Phase | Test | Pass Condition |
|-------|------|----------------|
| 0 | Stack Car starts clean | `wait_stack.sh` reports up; `git status` clean at start |
| 1 | Static code analysis | Collection-resolution code path at both submodule SHAs captured; questions in Step 2 answered |
| 2 | Baseline (7.1.3) seeded and captured | Seed command recorded; `capture.sh 713` completed; at least one collection has docs |
| 3 | 7.2.0 seeded and captured | Submodule SHA differs from baseline; same seed; `capture.sh 720` completed |
| 4 | Comparison | Documents-collection vs search-path-collection stated for BOTH versions, from captured files |
| 4 | Root cause | Exact file(s) and line(s) deciding the collection named — or an explicit "not code-level" conclusion with evidence |
| 5 | Revert and report | Original ref + submodule SHA restored; report in `handoffs/`; scratch removed |

---

## Files Involved

### Primary Files — inspect read-only (inside the `hyrax-webapp` submodule; line numbers approximate at 279fe062)
| File | Purpose | Key Method/Section |
|---|---|---|
| `hyrax-webapp/app/models/solr_endpoint.rb` | Resolves URL/collection/core and switches live connections | `SwitchMethod#switch!`, `#connection_options` (`core ||= collection`), `store :options` (`url`, `collection`) |
| `hyrax-webapp/app/models/account.rb` | Owns the SolrEndpoint | `belongs_to :solr_endpoint`, `build_solr_endpoint`, call to `solr_endpoint.switch!` |
| `hyrax-webapp/app/jobs/create_solr_collection_job.rb` | Creates/assigns the collection | `#perform` (`Hyku.single_tenant?` branch), `ensure_single_tenant_endpoint`, `perform_for_normal_tenant` |

### Reference Files — read but do not edit
| File | Why You Need It |
|---|---|
| `.gitmodules` (parent repo) | Submodule URL/branch |
| `hyrax-webapp/Gemfile` and `Gemfile.lock` | Record the actual `hyrax` revision (the Gemfile uses `branch: 'main'`, which moves — the lock file is the real version) |
| `up.sc.local.sh`, `docker-compose*.yml`, `.env*` (parent repo) | How Stack Car sets `SOLR_COLLECTION`, tenancy mode and whether images are rebuilt |
| Parent-repo overrides under `app/`, `config/`, `lib/` mentioning solr | Knapsack-level overrides of the above |

### Migration (if needed)
- [x] No migration needed — this task is pure investigation only

---

## Implementation Steps

### Step 0 — Move task file to active/ and update status (MANDATORY FIRST STEP)

This must be done before reading the task content, before synthesis, before any other action.

```bash
cd /Users/tam0013/Documents/git/agent-tasks

git mv projects/wvulibraries_knapsack/tasks/backlog/2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md \
       projects/wvulibraries_knapsack/tasks/active/2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md

# Then open the moved file and change: status: backlog → status: active
# Paste the output of both commands in chat before proceeding.
```

### Step 1 — Phase 0: Safety setup, refs and helper scripts

```bash
export SCRATCH="$HOME/hyku-720-testing"
mkdir -p "$SCRATCH"
cd /Users/tam0013/Documents/git/wvu_knapsack

# Working tree must be clean — if not, STOP (do not stash or discard anything)
git status --porcelain

# Record the starting point so Step 6 can restore it exactly
git rev-parse --abbrev-ref HEAD > "$SCRATCH/start-ref.txt"
git submodule status hyrax-webapp > "$SCRATCH/start-submodule-status.txt"
cat "$SCRATCH/start-ref.txt" "$SCRATCH/start-submodule-status.txt"

# Define the two refs under test and record the submodule SHA each one pins
cat > "$SCRATCH/refs.env" <<'EOF'
export SCRATCH="$HOME/hyku-720-testing"
export SOLR="http://localhost:8983/solr"
export BASE_REF="main"
export TARGET_REF="feature/hyku-v7.2.0-upgrade"
EOF
. "$SCRATCH/refs.env"

git -C hyrax-webapp fetch --all --tags --quiet
BASE_SHA=$(git ls-tree "$BASE_REF" hyrax-webapp | awk '{print $3}')
TARGET_SHA=$(git ls-tree "$TARGET_REF" hyrax-webapp | awk '{print $3}')
echo "export BASE_SHA=$BASE_SHA"     >> "$SCRATCH/refs.env"
echo "export TARGET_SHA=$TARGET_SHA" >> "$SCRATCH/refs.env"
echo "BASE   $BASE_REF   -> $BASE_SHA   ($(git -C hyrax-webapp describe --tags "$BASE_SHA" 2>&1))"
echo "TARGET $TARGET_REF -> $TARGET_SHA ($(git -C hyrax-webapp describe --tags "$TARGET_SHA" 2>&1))"
```

**Decision point:** the expected values are `BASE_SHA=2c49cfbbd72466c3b8c382b43a49e0b56503b832` (Hyku tag v7.1.3) and `TARGET_SHA=279fe062968abf7a00dec10959fc6a9f0fc69680`. If either recorded SHA differs from its expected value, or the two are equal, STOP, show the output in chat, and wait for the human to confirm which SHA is the intended baseline/target. The `describe` output is informational only (it depends on tag naming). Do not continue on an assumption.

Create the three helper scripts (they make both versions run IDENTICAL queries):

```bash
cat > "$SCRATCH/wait_stack.sh" <<'SCRIPT_EOF'
#!/usr/bin/env bash
# Waits up to 10 minutes for Solr and the Rails app to answer.
SOLR="${SOLR:-http://localhost:8983/solr}"
for i in $(seq 1 60); do
  if curl -sf "$SOLR/admin/info/system?wt=json" >/dev/null \
     && curl -s -o /dev/null -w '%{http_code}' http://localhost:3000/ | grep -q '^[23]'; then
    echo "stack up after ~$((i*10))s"; exit 0
  fi
  sleep 10
done
echo "TIMEOUT: stack did not come up"; exit 1
SCRIPT_EOF

cat > "$SCRATCH/capture.sh" <<'SCRIPT_EOF'
#!/usr/bin/env bash
# Usage (from wvu_knapsack root): bash capture.sh <label>   label = 713 | 720
# READ-ONLY: queries and log reads only.
set -u
LABEL="$1"
SCRATCH="${SCRATCH:-$HOME/hyku-720-testing}"
SOLR="${SOLR:-http://localhost:8983/solr}"
CATALOG="${CATALOG:-http://localhost:3000/catalog?q=&search_field=all_fields&per_page=5}"
OUT="$SCRATCH/$LABEL"; mkdir -p "$OUT"

git rev-parse --abbrev-ref HEAD       > "$OUT/parent-ref.txt"
git submodule status hyrax-webapp     > "$OUT/submodule-status.txt"

# 1. Collection names (SolrCloud first, standalone fallback)
python3 - "$SOLR" > "$OUT/collections.txt" <<'PY'
import sys, json, urllib.request
base = sys.argv[1]
def get(u):
    with urllib.request.urlopen(u, timeout=20) as r:
        return json.load(r)
try:
    names = get(base + "/admin/collections?action=LIST&wt=json")["collections"]
except Exception:
    names = list(get(base + "/admin/cores?action=STATUS&wt=json")["status"].keys())
print("\n".join(sorted(names)))
PY

# 2. Doc count per collection (tab-separated: name<TAB>count)
python3 - "$SOLR" "$OUT/collections.txt" > "$OUT/counts.tsv" <<'PY'
import sys, json, urllib.request
base, path = sys.argv[1], sys.argv[2]
for name in [l.strip() for l in open(path) if l.strip()]:
    try:
        with urllib.request.urlopen(f"{base}/{name}/select?q=*:*&rows=0&wt=json", timeout=20) as r:
            n = json.load(r)["response"]["numFound"]
    except Exception as e:
        n = "ERROR:" + type(e).__name__
    print(f"{name}\t{n}")
PY

# 3. What the app itself resolves for the account, and what its index/search paths see
docker compose exec -T web bin/rails runner '
def probe(label); puts "#{label}=#{yield}"; rescue => e; puts "#{label}=ERR #{e.class}: #{e.message}"; end
pick = ->(h) { h.to_h.select { |k, _| %w[url core collection].include?(k.to_s) }.inspect }
acct = Account.first
ep = acct&.solr_endpoint
probe("single_tenant")                 { Hyku.single_tenant? }
probe("account")                       { acct.name }
probe("account_count")                { Account.count }
probe("switch")                        { acct.switch!; "ok" }
probe("endpoint_id")                   { ep.id }
probe("endpoint_url")                  { ep.url }
probe("endpoint_collection")           { ep.collection }
probe("endpoint_connection_options")   { pick.call(ep.connection_options) }
probe("blacklight_connection_config")  { pick.call(Blacklight.connection_config) }
probe("valkyrie_solr_index_url")       { Valkyrie::IndexingAdapter.adapters[:solr_index].connection.options[:url] }
probe("index_adapter_class")           { Hyrax.index_adapter.class.name }
probe("hyrax_solrservice_numFound")    { Hyrax::SolrService.get("*:*", rows: 0)["response"]["numFound"] }
probe("catalog_search_path_numFound")  { CatalogController.blacklight_config.repository.connection.get("select", params: { q: "*:*", rows: 0 })["response"]["numFound"] }
probe("metadata_store_record_count")   { Hyrax.query_service.find_all.count }
' > "$OUT/endpoint.txt" 2>&1

# 4. Web UI search
curl -s -o "$OUT/catalog.html" -w "http_status=%{http_code}\n" "$CATALOG" > "$OUT/catalog-status.txt"
grep -o -i 'No results found[^<]*\|of <strong>[0-9,]*</strong>' "$OUT/catalog.html" | head -5 > "$OUT/catalog-summary.txt"

# 5. Solr lines from the web log (after the search above)
docker compose logs --no-color --tail 300 web 2>&1 | grep -i 'solr\|select?' | tail -40 > "$OUT/web-solr-log.txt"

echo "capture $LABEL complete:"; ls -l "$OUT"
SCRIPT_EOF

cat > "$SCRATCH/compare.sh" <<'SCRIPT_EOF'
#!/usr/bin/env bash
SCRATCH="${SCRATCH:-$HOME/hyku-720-testing}"
for f in submodule-status.txt collections.txt counts.tsv endpoint.txt catalog-summary.txt; do
  echo "=== $f (713 vs 720) ==="
  diff -u "$SCRATCH/713/$f" "$SCRATCH/720/$f" || true
done
for v in 713 720; do
  echo; echo "=== $v: collections WITH documents ==="
  awk -F'\t' '$2 ~ /^[0-9]+$/ && $2 > 0' "$SCRATCH/$v/counts.tsv"
  echo "=== $v: collection the app resolves / searches ==="
  grep -E '^(single_tenant|endpoint_|blacklight_|valkyrie_|hyrax_solrservice|catalog_search_path|metadata_store)' "$SCRATCH/$v/endpoint.txt"
done
SCRIPT_EOF
ls -l "$SCRATCH"
```

Then read how Stack Car is started and record the answers in the report (read-only):

```bash
cat up.sc.local.sh
grep -rn -i 'SOLR_COLLECTION\|SINGLE_TENANT\|HYKU_MULTITENANT' up.sc.local.sh docker-compose*.yml .env* 2>/dev/null | tee "$SCRATCH/stack-env-grep.txt"
docker compose ps --services
```

### Step 2 — Phase 1: Static code analysis (no Docker needed)

```bash
export SCRATCH="$HOME/hyku-720-testing"; . "$SCRATCH/refs.env"
cd /Users/tam0013/Documents/git/wvu_knapsack
FILES="app/models/solr_endpoint.rb app/models/account.rb app/jobs/create_solr_collection_job.rb"

# Change between the two submodule SHAs (diff runs INSIDE the submodule)
git -C hyrax-webapp diff "$BASE_SHA" "$TARGET_SHA" --stat -- $FILES > "$SCRATCH/code-diff-stat.txt"
git -C hyrax-webapp diff "$BASE_SHA" "$TARGET_SHA" -- $FILES        > "$SCRATCH/code-diff.txt"
cat "$SCRATCH/code-diff-stat.txt"

# Every place the collection/core name or tenancy mode is decided, at BOTH SHAs
for sha in "$BASE_SHA" "$TARGET_SHA"; do
  git -C hyrax-webapp grep -n -e 'SOLR_COLLECTION' -e 'single_tenant' -e "'core'" -e '\.collection' "$sha" -- app config lib \
    > "$SCRATCH/grep-collection-$sha.txt"
done

# Knapsack-level overrides in the parent repo (for each ref)
for ref in "$BASE_REF" "$TARGET_REF"; do
  git grep -n -i 'solr' "$ref" -- app config lib > "$SCRATCH/grep-knapsack-solr-$(echo $ref | tr '/' '_').txt"
done

# Record the hyrax revision actually locked at each SHA
for sha in "$BASE_SHA" "$TARGET_SHA"; do
  echo "== $sha"; git -C hyrax-webapp show "$sha:Gemfile.lock" | grep -n -A3 'samvera/hyrax'
done > "$SCRATCH/hyrax-lock-revisions.txt"
cat "$SCRATCH/hyrax-lock-revisions.txt"
```

Read the three primary files at each SHA (`git -C hyrax-webapp show <sha>:<file>`) and answer in the report, citing file and line:
1. Which code decides the Solr collection/core name for the account's SolrEndpoint in single-tenant mode, and where does `SOLR_COLLECTION` enter?
2. Which collection does the **indexing** path (Valkyrie solr_index adapter, via `switch!`/`core`) write to?
3. Which collection does the **search** path (Blacklight / `CatalogController`) read from?
4. Which tenancy mode does Stack Car run in (from `stack-env-grep.txt` and the runner output later)?
5. Did any of this change between `BASE_SHA` and `TARGET_SHA`?

If a primary file does not exist at a SHA, STOP and report the exact path.

### Step 3 — Phase 2: Baseline (7.1.3)

```bash
export SCRATCH="$HOME/hyku-720-testing"; . "$SCRATCH/refs.env"
cd /Users/tam0013/Documents/git/wvu_knapsack

docker compose down --volumes
git checkout "$BASE_REF" && git submodule update --init --recursive
git submodule status hyrax-webapp | tee "$SCRATCH/baseline-submodule-status.txt"
# The SHA printed above MUST equal BASE_SHA — if not, STOP.

# Start Stack Car. If up.sc.local.sh does not rebuild images, run `docker compose build` first and note that in the report.
sh up.sc.local.sh > "$SCRATCH/startup-713.log" 2>&1 &
bash "$SCRATCH/wait_stack.sh"
docker compose ps
```

**Seed test data.** Use the project's DOCUMENTED seed/import method (look in `wvu_knapsack` README, `status.md`, and the testing-strategy handoff). Run the same command, with the same input, on both versions, and write the exact command into `$SCRATCH/seed-command.txt`. If no documented method exists, STOP and ask the human — do not invent one. The number of seeded records must be identical on both versions.

```bash
export SCRATCH="$HOME/hyku-720-testing"; . "$SCRATCH/refs.env"
cd /Users/tam0013/Documents/git/wvu_knapsack
# (run the documented seed command here, and record it in $SCRATCH/seed-command.txt)
bash "$SCRATCH/capture.sh" 713
cat "$SCRATCH/713/counts.tsv" "$SCRATCH/713/endpoint.txt" "$SCRATCH/713/catalog-summary.txt"
```

If every collection has 0 documents after seeding, STOP (seeding failed — not a finding).

### Step 4 — Phase 3: v7.2.0

```bash
export SCRATCH="$HOME/hyku-720-testing"; . "$SCRATCH/refs.env"
cd /Users/tam0013/Documents/git/wvu_knapsack

docker compose down --volumes
git checkout "$TARGET_REF" && git submodule update --init --recursive
git submodule status hyrax-webapp | tee "$SCRATCH/target-submodule-status.txt"
# The SHA printed above MUST equal TARGET_SHA and MUST differ from BASE_SHA — if not, STOP.

sh up.sc.local.sh > "$SCRATCH/startup-720.log" 2>&1 &
bash "$SCRATCH/wait_stack.sh"
docker compose ps
```

Seed using the exact command in `$SCRATCH/seed-command.txt`, then:

```bash
export SCRATCH="$HOME/hyku-720-testing"; . "$SCRATCH/refs.env"
cd /Users/tam0013/Documents/git/wvu_knapsack
bash "$SCRATCH/capture.sh" 720
cat "$SCRATCH/720/counts.tsv" "$SCRATCH/720/endpoint.txt" "$SCRATCH/720/catalog-summary.txt"
```

### Step 5 — Phase 4: Comparison and root cause

```bash
export SCRATCH="$HOME/hyku-720-testing"; . "$SCRATCH/refs.env"
bash "$SCRATCH/compare.sh" | tee "$SCRATCH/comparison.txt"
```

Then, for EACH version, state in the report (quoting file contents, not summaries):
- Collection(s) holding documents (`counts.tsv`).
- Collection/core the app resolves (`endpoint_collection`, `endpoint_connection_options`, `blacklight_connection_config`, `valkyrie_solr_index_url`).
- What the app's own search path sees (`catalog_search_path_numFound`, `hyrax_solrservice_numFound`) versus the records in the metadata store (`metadata_store_record_count`).
- Whether the web UI search found records (`catalog-summary.txt`).

Conclusion rules:
- `catalog-summary.txt` is a regex over Blacklight HTML and is a convenience signal only. The Solr counts and the endpoint probes are the authoritative evidence; keep `catalog.html` for manual inspection.
- The report must state which account was inspected (`account`) and how many accounts exist (`account_count`). If `account_count` is greater than 1, say so; the probes only describe `Account.first`.
- **Mismatch confirmed** only if documents exist in collection A and the search path's numFound/collection points at B, with the captured lines quoted.
- Name the file and line (from Step 2 evidence) that decides each side.
- If the evidence shows the collections agree (e.g. an empty index because indexing never ran, or a UI/permissions problem), say "mismatch NOT confirmed" and state what the evidence does show. Do not force the hypothesis.
- If any `probe` returned `ERR`, report it verbatim; do not guess its value.

### Step 6 — Phase 5: Revert to the starting state

```bash
export SCRATCH="$HOME/hyku-720-testing"; . "$SCRATCH/refs.env"
cd /Users/tam0013/Documents/git/wvu_knapsack

docker compose down --volumes
git checkout "$(cat "$SCRATCH/start-ref.txt")" && git submodule update --init --recursive
git submodule status hyrax-webapp | diff - "$SCRATCH/start-submodule-status.txt" && echo "restored to start state"
git status --porcelain
```

Restart Stack Car only if it was running when this task started.

### Step 7 — Report, evidence copy and cleanup

```bash
export SCRATCH="$HOME/hyku-720-testing"; . "$SCRATCH/refs.env"
REPORT=/Users/tam0013/Documents/git/agent-tasks/projects/wvulibraries_knapsack/handoffs/2026-11-05-HYKU-7.2.0-SEARCH-BUG-LOCAL-TESTING-RESULTS.md
{
  echo "# Hyku 7.2.0 search bug — local testing results"
  echo "Date: $(date +%F)   Refs: $BASE_REF ($BASE_SHA) vs $TARGET_REF ($TARGET_SHA)"
  echo; echo "## Seed command"; cat "$SCRATCH/seed-command.txt"
  echo; echo "## Stack environment"; cat "$SCRATCH/stack-env-grep.txt"
  echo; echo "## Code diff stat (submodule)"; cat "$SCRATCH/code-diff-stat.txt"
  echo; echo "## Hyrax locked revisions"; cat "$SCRATCH/hyrax-lock-revisions.txt"
  echo; echo "## Comparison (713 vs 720)"; cat "$SCRATCH/comparison.txt"
  for v in 713 720; do
    echo; echo "## Runtime resolution at $v"; cat "$SCRATCH/$v/endpoint.txt"
    echo; echo "## Counts at $v"; cat "$SCRATCH/$v/counts.tsv"
  done
  echo; echo "## Findings (agent-written: answers to Step 2 questions 1-5 and Step 5 conclusions, with file:line)"
  echo "FILL IN"
} > "$REPORT"
```

Replace the `FILL IN` line with the written findings. Then verify and clean up (only after the report exists and is non-empty):

```bash
test -s "$REPORT" && ! grep -q '^FILL IN' "$REPORT" && rm -rf "$SCRATCH" && echo "scratch removed"
```

Do NOT `git add` or commit the report until the Review Gate below is satisfied.

---

## Review Gate

You MAY NOT commit, push, or mark any task step as "complete" until BOTH conditions are met:

1. **Reviewer has issued a GO disposition** — GO/REVISE/STOP per REVIEW_AGENT_GUIDE.md
2. **Human authorization** — explicit human message authorizing commit AND separate authorization for push

**Before requesting review:** Paste your complete evidence in chat (the report plus the key captured files) and state: *"All implementation and verification complete. Requesting reviewer assessment."*

---

## Synthesis Report Template

Save this as an MD file before starting any work:

```markdown
## STATUS SYNTHESIS REPORT

**Task**: Local Testing of Hyku 7.2.0 Search Bug — Stack Car Investigation
**Status**: backlog → active (after Step 0) → completed
**Date**: YYYY-MM-DD

### What I'm About to Do
Investigate (read-only) why Hyku 7.2.0 search returns no results, by (1) reading the collection-resolution code at the 7.1.3 and 7.2.0 submodule SHAs, then (2) running identical seeded captures on both versions in Stack Car, then (3) comparing which collection holds documents against which collection the app's search path reads. No code is changed.

### Files I'll Reference
| File | Purpose | Status |
|---|---|---|
| hyrax-webapp/app/models/solr_endpoint.rb | Collection/core resolution and switch! | pending |
| hyrax-webapp/app/models/account.rb | Account to SolrEndpoint relationship | pending |
| hyrax-webapp/app/jobs/create_solr_collection_job.rb | Collection creation / single-tenant endpoint | pending |
| $HOME/hyku-720-testing/ (scratch) | Captured evidence and helper scripts | not started |

### Prerequisites Completed
- [ ] Task file moved to active/ with git mv (find output pasted in chat)
- [ ] YAML status updated to active
- [ ] Read README.md EXECUTOR section, project guide, status.md, testing-strategy handoff
- [ ] Working tree clean; start ref and submodule SHA recorded
- [ ] BASE_SHA verified as a 7.1.x Hyku and differs from TARGET_SHA

### Expected Outcomes
For each version: the collection holding documents, the collection the search path queries, and the file/line deciding each — or evidence that no mismatch exists.

### Critical Gotchas I Will Avoid
- Assuming git checkout switches the hyrax-webapp submodule (it does not; run submodule update)
- Comparing a seeded run to an unseeded run (seed identically after every down --volumes)
- Assuming main is 7.1.3 without checking the pinned SHA
- Writing outside $HOME/hyku-720-testing; editing any application file
```

---

## Planner Self-Validation (Draft Author Checklist)

*Planner self-validation — not sent to agents. For draft author only.*

- [x] YAML frontmatter complete (status: draft, resume, priority, type, system_domain, mvp_alignment, local_worker_safe)
- [x] DRAFT STATUS NOTE and DRAFT REVIEW CHECKLIST present (review items left unchecked for the reviewer/human)
- [x] Agent Dispatch Interface matches TASK_TEMPLATE.md block; only `<agent-tasks>` placeholder remains
- [x] Full task body present; no `[FILL IN HERE]` markers (the report's single `FILL IN` line is an intentional agent action in Step 7)
- [x] File is in tasks/drafts/ with status: draft
- [x] Scope is investigation-only; hypothesis separated from documented facts
- [x] Primary file paths checked against `samvera/hyku@279fe062` (submodule pin) during independent review; NOT executed locally
- [ ] Commands run end-to-end on Stack Car (to be confirmed by first dispatch under supervision)
- [x] Review Gate and Synthesis Report Template included

---

## Acceptance Criteria

- [ ] 1. `BASE_SHA` and `TARGET_SHA` recorded and differ, and each matches its expected value in Step 1 (or the human confirmed a different SHA in chat)
- [ ] 2. Step 2 questions 1-5 answered with file and line citations from both SHAs
- [ ] 3. The exact seed command is recorded and the same seeded record count is shown at both versions
- [ ] 4. `capture.sh` completed for 713 and 720 with all output files present (collections, counts, endpoint, catalog, log)
- [ ] 5. For BOTH versions the report states, quoting captured files: the collection(s) holding documents, the collection/core the app resolves, and the app search path's numFound
- [ ] 6. Mismatch status stated explicitly as CONFIRMED or NOT CONFIRMED with the deciding file(s)/line(s) named — or a stated reason none can be named
- [ ] 7. Report saved at `handoffs/2026-11-05-HYKU-7.2.0-SEARCH-BUG-LOCAL-TESTING-RESULTS.md`, with no `FILL IN` text left
- [ ] 8. Parent repo and submodule restored to the recorded starting ref/SHA (diff against `start-submodule-status.txt` clean), working tree clean, scratch folder removed
- [ ] 9. No application, submodule, config or `.env` file was modified

---

## Stop Conditions — escalate to user immediately if:

- The working tree is dirty at the start (do not stash or discard)
- `BASE_SHA` equals `TARGET_SHA`, or the baseline SHA is not a 7.1.x Hyku
- After `git submodule update`, the submodule SHA does not equal the expected SHA
- Stack Car cannot be started or does not become healthy after two clean attempts
- No documented seed method exists, or seeding leaves every collection at 0 documents
- The mismatch is not reproducible after two full clean runs (down --volumes, checkout, submodule update, seed)
- A primary file listed for inspection does not exist at the recorded SHA
- An architectural decision is required about which collection name is canonical
- The root cause lies outside the three primary files and would expand scope
- Any step would require writing or modifying application code (this task is investigation only)

---

## Commit Instructions

> ⚠️ **PRE-COMMIT CHECK**: Before running ANY of the commands below, confirm:
> - [ ] Reviewer has issued GO disposition (not just pending)
> - [ ] Human has explicitly authorized the command I am about to run (commit or push) in a message that names it
> - [ ] Both conditions documented in your completion report
>
> If either condition is unmet, DO NOT proceed. Wait for the missing authorization.

Run git commands on **host only** — never inside the Docker container:
```bash
git add [specific files only — never git add .]
git commit -m "[type]: [spec/file name] — [brief description of root cause and findings]"
git push
```

**Task file move on completion:**
```bash
# Tracked file (already committed): use git mv
git mv projects/wvulibraries_knapsack/tasks/active/2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md \
       projects/wvulibraries_knapsack/tasks/completed/2026-11/2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md

# New/untracked file (just created this session): move with filesystem, then add the final path
# mv ... then git add the final path

git commit -m "chore: move 2026-11-05-HIGH-BUG-HYKU-7.2.0-LOCAL-TESTING-PLAN.md to completed/"
```

---

## Documentation
- [ ] No doc changes needed
- [ ] Update `docs/[path]/[file].md` — [what to update]
- [ ] Flag doc gap: [description] — do not create the doc, add to backlog instead

---

## Dependencies
**Blocked by**: none  
**Blocks**: hyku-v7.2.0-upgrade fix strategy development  
**Related tasks**: 2026-11-05 HYKU-UPGRADE-TESTING-STRATEGY (handoff document)

---

## Progress / Resume

*Updated by the session whenever it stops before the task is finished (SESSION_CLOSEOUT.md section 2a).*

- **Done so far:**
- **Next step:**
- **Last verified fact (with command):**
- **Local-only commits (`git log --oneline origin/main..HEAD`):**
- **Owning session / window label:**

---

## Completion Report
*Filled in by the implementing agent after completion*

**Completed by**: [agent name]  
**Completion date**: YYYY-MM-DD  
**Final test result**: X examples, Y failures  
**Evidence basis:** [direct verification / review of pasted evidence / reported by agent / human assertion] — [one-line source or note when not direct verification]

### What was changed
- `[file]` — [description of change]

### Issues discovered
[Any problems found during implementation that weren't in the original task]

### Follow-up tasks needed
[Any new backlog items identified — do not create the files, just list them here]

### Lessons learned
[What worked, what didn't, what future tasks in this area should know]

---

## Handoff Summary
*Filled in at end of session — one scannable line for next agent*

HANDOFF SUMMARY: [files updated] | [structural changes] | [next action needed]
