---
status: active
priority: MEDIUM
type: feature
system_domain: AI_MANAGER
local_worker_safe: true
---

# TASK: Complete Ollama Vision Integration for Alt-Text Generation in ollama_testing Branch

**Status**: ACTIVE
**Priority**: MEDIUM
**Type**: feature
**Created**: 2026-06-17
**Last Updated**: 2026-06-17
**Branch**: ollama_testing
**Assigned To**: (pending)

---

## Summary
Complete the Ollama vision model integration for automatic alt-text generation in Bulkrax importer. This experimental feature uses moondream model via Ollama to generate archival-quality alt descriptions for uploaded images and PDFs.

**SCOPE**: This is a WVU-specific experimental feature for local accessiblity improvements. LLM integration will NOT be incorporated into Hyku or Samvera core products (organizational decision). However, AI working group is discussing NEW PRODUCTS for AI generation → Hyku data import (still in discussion), and this work may align with future directions. This experimentation remains independent local work for now.

---

## Context

**What exists**:
- Ollama service running on Docker (internal, port 11434)
- VisionService class with Ollama POST /api/generate integration
- AiMetadataBehavior concern with alt-text generation logic
- FileSet decorator: `app/models/file_set_decorator.rb`

**What needs work**:
1. **Rewrite AiMetadataBehavior for Valkyrie**
   - Current code uses ActiveFedora API (file?, image?, pdf? methods)
   - Must update to Valkyrie Hyrax::FileSet API
   - Must use Valkyrie query service for file metadata

2. **Integration with Bulkrax**
   - After import: trigger vision service for each file set
   - Store generated alt-text on file set's flexible schema
   - Validate alt-text meets 125-char archival standard

3. **Testing and Backfill**
   - Test on single image/PDF upload
   - Run console script to backfill existing file sets
   - Verify quality of generated descriptions

---

## Problem Statement

**Issue 1: ActiveFedora → Valkyrie Migration**
The `AiMetadataBehavior` concern uses deprecated ActiveFedora methods:
```ruby
def image?
  # Currently calls AF API — needs Valkyrie rewrite
  file_set.original_file.mime_type.match?(/^image/)
end

def pdf?
  file_set.original_file.mime_type == 'application/pdf'
end
```

**Should use Valkyrie**:
```ruby
def image?
  file_metadata = Hyrax.custom_queries.find_original_file(file_set: self)
  file_metadata.mime_type.match?(/^image/)
end
```

**Issue 2: Callback Timing**
- After-create callback may not fire consistently on Hyrax::FileSet
- May need alternative hook (after-save? in FileSet model? Bulkrax job?)

**Issue 3: Alt-Text Attribute**
- Need to verify Hyrax::FileSet flexible schema has `alt_text` property
- May need to add to schema if not present

---

## Solution Approach

### Phase 1: Valkyrie Rewrite
1. Read `app/models/concerns/ai_metadata_behavior.rb` fully
2. Identify all AF-specific methods:
   - `file_set.original_file` → use `Hyrax.custom_queries.find_original_file(file_set: self)`
   - `file_set.file_sets` → use Valkyrie query service
   - Check for `after_create` callback availability
3. Rewrite each method to use Valkyrie API:
   ```ruby
   def image?
     file_metadata = Hyrax.custom_queries.find_original_file(file_set: self)
     file_metadata&.mime_type&.match?(/^image/) || false
   end
   ```

### Phase 2: Schema Verification
1. Check if `Hyrax::FileSet` has `alt_text` property
2. If not, determine where to store alt-text:
   - Add to flexible schema? (likely)
   - Check `hyrax-webapp/config/flexible_schemas.yml`
3. Update code to read/write alt_text via Valkyrie attributes

### Phase 3: Callback Integration
1. Verify `after_create` fires on Hyrax::FileSet in Valkyrie mode
2. If not, explore alternatives:
   - After-save hook
   - Job queued by Hyrax after file upload
   - Bulkrax post-import job
3. Ensure vision service is called async (GoodJob background job)

### Phase 4: Testing
1. Upload single image/PDF via UI
2. Check Solr/Fedora for generated alt-text
3. Verify alt-text meets 125-char standard (no "Image of" prefix)
4. Test error handling (vision service timeout, invalid mime type)

### Phase 5: Backfill
1. Write Rails console script to find all FileSet objects without alt-text
2. Call vision service on each
3. Log results and failures

---

## Files to Modify

**Core Files**:
- `app/models/concerns/ai_metadata_behavior.rb` — Valkyrie rewrite (primary)
- `app/models/file_set_decorator.rb` — Already uses decorator pattern
- `app/services/vision_service.rb` — May need path adjustments

**Reference Files**:
- `hyrax-webapp/config/flexible_schemas.yml` or equivalent
- `config/initializers/hyrax.rb` — Schema configuration
- `lib/tasks/` — For backfill rake task

---

## VisionService Reference

```ruby
# Already functional for Valkyrie
def self.call_with_bytes(bytes:, filename:)
  response = Net::HTTP.post_form(uri, {
    model: 'moondream',
    prompt: 'Provide a concise, 125-character archival description of this image. Do not use phrases like "Image of" or "Photo of".',
    stream: false,
    images: [Base64.strict_encode64(bytes)]
  })
  JSON.parse(response.body)['response']
end

# Needs Valkyrie path fix in .call():
def self.call(file_set)
  file_metadata = Hyrax.custom_queries.find_original_file(file_set: file_set)
  file = Valkyrie::StorageAdapter.find_by(id: file_metadata.file_identifier)
  bytes = file.read
  call_with_bytes(bytes: bytes, filename: file_metadata.filename)
end
```

---

## Architectural Decisions

- **Async**: Vision service runs in background job (GoodJob) to avoid request timeout
- **Flexible Schema**: Alt-text stored on FileSet as first-class attribute (Valkyrie)
- **Error Handling**: Vision service failures logged but don't block upload
- **Prompt**: 125-char archival description, no "Image of" phrases

---

## Testing & Validation

1. ⏳ AiMetadataBehavior rewritten for Valkyrie
2. ⏳ Alt-text attribute confirmed in schema
3. ⏳ After-create callback fires (or alternative hook working)
4. ⏳ Single image upload generates alt-text
5. ⏳ Single PDF upload generates alt-text (if supported)
6. ⏳ Generated alt-text meets 125-char standard
7. ⏳ Vision service errors handled gracefully
8. ⏳ Backfill script runs without errors
9. ⏳ Existing FileSet objects populated with alt-text

---

## Deployment Notes

- Changes remain on ollama_testing branch until production-ready
- No database migrations needed (flexible schema handles attributes)
- Ollama service must be running before activating
- Background job queue (GoodJob) must be active
- Consider rate-limiting vision service calls (Ollama may be slow)

---

## Known Issues / Context

**Valkyrie Flexible Mode**:
- File metadata accessed via `Hyrax.custom_queries.find_original_file(file_set: self)`
- FileSet attributes are schema-driven, not model-defined
- After-create callbacks may behave differently than AF

**Vision Service**:
- Ollama model: moondream (small, fast, accurate for descriptions)
- Endpoint: POST /api/generate (docker-internal, not exposed)
- Timeout: May need tuning for large images/PDFs

**Bulkrax Integration**:
- If alt-text generation plugs into importer workflow, needs careful timing
- Should probably run as post-import job to avoid blocking batch import

---

## Completion Checklist

- ⏳ AiMetadataBehavior rewritten for Valkyrie
- ⏳ All AF API methods replaced with Valkyrie equivalents
- ⏳ Alt-text attribute location confirmed/added to schema
- ⏳ Callback integration verified working
- ⏳ Single file upload test passes
- ⏳ Alt-text meets archival standard (125 chars, no "Image of")
- ⏳ Error handling tested (vision service timeout, invalid type)
- ⏳ Backfill script created and tested
- ⏳ Documentation updated
- ⏳ Ready for merge or hold for later

---

## Dependencies & Integration

- Hyrax::FileSet (Valkyrie)
- Hyrax custom query service
- Valkyrie::StorageAdapter
- Ollama vision service (moondream model)
- GoodJob background job queue
- Bulkrax importer (eventual integration)
- Rails 7.2 / Hyrax 5.x flexible schema

---

## Agent Assignment

**Assigned To**: [Qwen3.5-27B local (primary)]
**Why This Agent**: Strong at Rails/ActiveRecord rewriting, background job logic, and Valkyrie API patterns
**Supervision Level**: watched carefully (experimental AI feature, first Valkyrie integration)

> If local Qwen attempts fail:
> - Document exact error (Valkyrie API mismatch, callback not firing, etc.)
> - Escalate to Claude Haiku 0.33x for API research
> - Do NOT merge to production without human review of vision service integration
