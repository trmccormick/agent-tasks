---
status: backlog
priority: MEDIUM
type: BUGFIX
system_domain: Lookup Service
mvp_alignment: CRAFT_LOOKUP_INTEGRITY
local_worker_safe: true
created: 2026-08-13
last_updated: 2026-08-13
---

## CraftLookupService ENOTDIR Error Handling Gap

### Problem
`CraftLookupService#find_craft` does not properly handle `Errno::ENOTDIR` errors that can occur when `Dir.glob` encounters a path component that is a file rather than a directory.

The spec at `spec/services/lookup/craft_lookup_service_spec.rb:186` mocks this scenario:
```ruby
allow(Dir).to receive(:glob).and_raise(Errno::ENOTDIR.new("Not a directory"))
service = described_class.new
expect(service.find_craft('any_craft')).to be_nil
```

The service needs to rescue `Errno::ENOTDIR` (alongside existing `Errno::ENOENT` and `File.directory?` checks) and return `nil` gracefully.

### Target File
- `galaxy_game/app/services/lookup/craft_lookup_service.rb` — add ENOTDIR rescue in `find_craft`

### Acceptance Criteria
- [ ] `find_craft` rescues `Errno::ENOTDIR` and returns `nil`
- [ ] Existing spec at line 186 passes without mocking (or mock is removed if service handles it natively)
- [ ] No regression in other error handling paths (ENOENT, File.directory? checks)
