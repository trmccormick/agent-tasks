Here are the copy-pasteable GitHub Issue templates broken out for each specific point in the launch roadmap. They are completely modularized so you can drop them directly into your GitHub issues queue as soon as your review is complete.
Issue 1: Add PostgreSQL Index to ARK Identifier Column

    Repository: wvulibraries/wvu_knapsack

    Labels: backend, database, performance

Markdown

### Description
To prevent lookups from becoming a database bottleneck as the repository scales past tens of thousands of legacy items imported from MFCS, we need to add an explicit database index to the column storing our custom ARK identifier strings.

### Acceptance Criteria
- [ ] An ActiveRecord migration is added to the knapsack layer.
- [ ] The migration applies a native B-tree index on the `ark_id` column of our target work table (e.g., `generic_works`).
- [ ] Lookups against the indexed column are verified via the Rails console as highly performant.

### Technical Notes
Create the migration within the knapsack engine path (`db/migrate/`):
```ruby
class AddIndexToArkIdentifiers < ActiveRecord::Migration[7.0]
  def change
    unless column_exists?(:generic_works, :ark_id)
      add_column :generic_works, :ark_id, :string
    end
    add_index :generic_works, :ark_id, unique: true
  end
end

Issue 2: Implement ARK Web Resolver Controller (ArksController)

    Repository: wvulibraries/wvu_knapsack

    Labels: backend, routing, core-feature

Markdown

### Description
Implement a native Rails routing catcher and traffic-cop controller to handle incoming institutional Archival Resource Keys matching our Assigned NAAN (`13030`). The endpoint must conditionally route users based on whether they are requesting metadata or a live object view.

### Acceptance Criteria
- [ ] Route glob defined in `config/routes.rb` traps incoming `/ark:/13030/:ark_suffix` requests.
- [ ] If `params.has_key?(:info)` is **false**, execution triggers a `301 Moved Permanently` HTTP redirect to the work's primary catalog landing display page.
- [ ] If `params.has_key?(:info)` is **true**, execution intercepts the request and outputs a clean machine-readable JSON object containing our institutional Commitment Statement text and basic metadata attributes.
- [ ] Requests for an invalid or non-existent ARK return a clean `404 Not Found` response.

### Technical Notes
Route pattern:
```ruby
get '/ark:/13030/:ark_suffix', to: 'arks#resolve', as: :ark_resolver

Commitment Statement text to bundle in JSON:

    "West Virginia University Libraries is committed to maintaining the persistent discoverability and long-term preservation of this digital asset identifier link."


---

### Issue 3: Collection-Level Side Facet Restoration
* **Repository:** `wvulibraries/wvu_knapsack`
* **Labels:** `metadata`, `solr`, `configuration`

```markdown
### Description
Fix the search behavior where side facets/filters completely vanish when a user is browsing within a specific collection profile (violating consistency seen in our reference instances).

### Acceptance Criteria
- [ ] Locate the active M3 configuration template (`config/metadata_profiles/m3_profile.yaml`).
- [ ] Target specific properties requested by the archivist: **Date, People, Location, Subjects, and Format**.
- [ ] Set `facetable: true` attributes on these fields.
- [ ] Ensure they map to index inside Solr with appropriate string/symbol suffixes (such as the standard Blacklight `_sim` facet suffix).
- [ ] Execute a full repository index migration loop (`rake hyrax:remigrate`) to test active rendering.

Issue 4: Filter and Restructure "Browse By" Fields

    Repository: wvulibraries/wvu_knapsack

    Labels: ui, configuration

Markdown

### Description
The archivist requested cleaning up the public "Browse By" tab. We need to remove the redundant "Type" selector (since legacy imports classify as standard works) and explicitly ensure the menu focuses on Date, People, Location, Subjects, and Format.

### Acceptance Criteria
- [ ] "Type" field is hidden or removed from the public browse array parameters.
- [ ] High-priority fields (**Date, People, Location, Subjects, and Format**) are configured to populate the primary browse selectors.
- [ ] If the YAML configuration layer doesn't dynamically clear the view, implement a targeted view partial block override inside `app/views/catalog/`.

Issue 5: Suppress Homepage Sections & Expand Featured Collections Grid

    Repository: wvulibraries/wvu_knapsack

    Labels: ui, theming

Markdown

### Description
Clean up visual clutter on the home landing interface by suppressing unused content segments, dropping restrictive layout boundaries, and expanding the featured collection layouts.

### Acceptance Criteria
- [ ] Hardcoded "Recent Works" block is suppressed/removed from the home layout view.
- [ ] Hardcoded "Featured Works" block is suppressed/removed from the home layout view.
- [ ] The visual bounding border layout surrounding the repository's main description block is completely stripped via SCSS overrides.
- [ ] The "Featured Collections" section layout is restructured via responsive CSS Grid to break past its standard 6-item linear layout maximum constraint.

### Technical Notes
Target home templates inside `app/views/hyku/homepage/` and local SCSS overrides in `app/assets/stylesheets/wvu_knapsack/`.

Issue 6: Implement Summary Description Truncation and Field Condensation

    Repository: wvulibraries/wvu_knapsack

    Labels: ui, frontend-ux

Markdown

### Description
Reduce text clutter on catalog search result lists and collection index views. Results elements should be condensed down to strictly surface Title, Description, and Date Created, while long Description texts should be safely clamped to prevent wall-of-text layout breaks.

### Acceptance Criteria
- [ ] Extraneous field elements (e.g., *Subject, Rights Notes, Bibliographic Citation*) are stripped out of search summary item lists (`app/views/catalog/_index_default.html.erb`).
- [ ] Description paragraph strings use modern CSS line clamping (`-webkit-line-clamp: 3; display: -webkit-box;`) to truncate long data securely.
- [ ] This safely avoids raw backend Ruby string-truncation which can clip mid-HTML-tag and break layouts.
- [ ] A minimal client-side JavaScript utility handles appending a interactive "Show More / Show Less" toggle link on clamped summaries.