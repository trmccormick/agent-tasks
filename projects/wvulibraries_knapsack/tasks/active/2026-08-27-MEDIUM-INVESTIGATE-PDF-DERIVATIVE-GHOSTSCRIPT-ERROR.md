# TASK: Investigate ValkyrieCreateDerivativesJob PDF Conversion Failure

**Status**: Ready for investigation  
**Priority**: MEDIUM (not blocking facet-limiting feature)  
**Estimated**: 1-2 hours  

## Problem Statement

ValkyrieCreateDerivativesJob failing when converting PDF files to JPG thumbnails during imports.

**Error Details**:
- Job ID: `7faf3b23-3174-47fc-ba4f-c6d4a24bd5d8`
- Error: `MiniMagick::Error` during PDF→JPG conversion
- Root cause: Ghostscript error code 1 (Unrecoverable error)
- Details:
  - `GPL Ghostscript 10.00.0: Unrecoverable error, exit code 1`
  - `ExecuteGhostscriptCommand/75`
  - Missing file or permission issue
  - ImageMagick "convert" command deprecated in IMv7

**Discovery**: Found during main branch import testing (2026-08-26)  
**Related to**: Pre-existing infrastructure issue (not facet-limiting commits)  
**Scope**: PDF files only; other formats work fine

## Root Cause Possibilities

1. **Ghostscript misconfiguration** in Docker container
2. **ImageMagick/Ghostscript version mismatch** (convert deprecated in IMv7)
3. **Missing Ghostscript binary** or libraries in container
4. **Temp file permissions** preventing PDF processing
5. **Missing PDF delegate** in ImageMagick configuration

## Investigation Goals

1. Check Ghostscript installation & version in container
2. Verify convert command availability
3. Check if PDF conversion works in isolation
4. Identify root cause (version, missing binary, permissions)
5. Recommend fix (upgrade packages, rebuild container, configure delegates)

## Acceptance Criteria

✅ Determined root cause of Ghostscript failure  
✅ Verified if issue is container-specific or code-related  
✅ Recommended fix path  
✅ Document solution  

## Steps

### 1. Check Ghostscript in Container

```bash
docker exec wvu_knapsack-web-1 gs -v
docker exec wvu_knapsack-web-1 which gs
docker exec wvu_knapsack-web-1 ghostscript -v
```

### 2. Check ImageMagick Version & Convert Command

```bash
docker exec wvu_knapsack-web-1 convert -version
docker exec wvu_knapsack-web-1 identify -version
docker exec wvu_knapsack-web-1 which convert
docker exec wvu_knapsack-web-1 which magick
```

### 3. Test PDF Conversion Manually

```bash
# Get a PDF from a failed work
docker exec wvu_knapsack-web-1 bash -c "
  find /tmp -name '*.pdf' -mtime -1 2>/dev/null | head -1
"

# Try manual conversion
docker exec wvu_knapsack-web-1 bash -c "
  convert /path/to/test.pdf[0] /tmp/test-output.jpg
"
```

### 4. Check Dockerfile & ImageMagick Delegates

```bash
cat Dockerfile | grep -A 5 -B 5 "imagemagick\|ghostscript\|gs"
cat Dockerfile | grep -A 5 -B 5 "magick"

# In container, check delegates
docker exec wvu_knapsack-web-1 convert -list delegate | grep -i pdf
```

### 5. Review Hyrax Configuration

```bash
# Check if PDF support is enabled in Hyrax
grep -r "pdf" config/initializers/ | grep -i derivative
grep -r "pdf" config/hyrax.rb 2>/dev/null || echo "file not found"

# Check Hydra::Derivatives config
grep -r "Hydra::Derivatives" config/ | head -5
```

### 6. Rails Console Diagnostic

```bash
docker exec wvu_knapsack-web-1 rails console production <<'EOF'
# Check registered file set derivatives
puts Hyrax.config.file_set_file_service
puts Hyrax::FileSetPresenter.new(FileSet.first, nil).derivative_definitions
EOF
```

## Notes

- **Not urgent**: Facet-limiting feature doesn't depend on this
- **Pre-existing**: Likely present before recent commits
- **Scope**: PDF files only; JPG/PNG/GIF files work fine
- **Precedent**: Similar Ghostscript issues common in Docker containers with imagemagick

## Solution Options

1. **Upgrade ImageMagick/Ghostscript**: Rebuild container with newer versions
2. **Fix delegates**: Configure ImageMagick PDF delegate to use correct convert syntax
3. **Use magick instead of convert**: Update MiniMagick to use "magick" instead of deprecated "convert"
4. **Check Dockerfile**: Ensure all required packages installed in build
5. **Skip PDF derivatives**: Disable PDF thumbnail generation if not critical

## Related Issues

- Job ID: `7faf3b23-3174-47fc-ba4f-c6d4a24bd5d8` (Sidekiq dashboard link provided)
- Branch: main (pre-existing, not branch-specific)
- Frequency: Only when PDF files are imported
