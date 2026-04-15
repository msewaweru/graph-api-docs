# GA Promotion Review Criteria

Review criteria specific to promoting APIs from beta to v1.0 (General Availability).

---

## Key Assumption

When promoting from beta to v1.0, **nothing changes** by default — content should be identical except where version is explicitly mentioned.

**Exception:** If the Documentation Plan identifies deviations (renames, type changes, structural differences), documentation should be authored from scratch, not copied from beta.

## Copy-from-Beta Validation

For files copied from beta, verify these updates were applied:

### All Files

- [ ] Beta disclaimer **removed**
- [ ] Version references updated from `/beta` to `/v1.0`
- [ ] `ms.date` updated in YAML front matter

### API Method Files

- [ ] National cloud include statement **deleted** (the `[!INCLUDE [national-cloud-support]...]` line)
- [ ] SDK snippet links **removed** from Example Request section
- [ ] `# [HTTP](#tab/http)` tab **removed**
- [ ] Tab boundary markers (`---`) **removed**
- [ ] Language-specific SDK snippet includes **removed**
- [ ] Only HTTP example remains in Request section
- [ ] Example URLs use `graph.microsoft.com/v1.0/` (not `/beta/`)
- [ ] Properties not returned in v1.0 removed from examples

### Resource Files

- [ ] Methods table updated for promoted methods
- [ ] Properties table updated for promoted properties
- [ ] JSON representation updated for promoted properties
- [ ] Examples updated to include new properties (where applicable)

## Beta-Only Content Removal

If the Documentation Plan includes a "Beta-only content to remove" table:

- [ ] All listed properties **absent** from v1.0 resource files
- [ ] All beta-only inherited properties removed from Properties tables, JSON representation, and example payloads
- [ ] No beta-only relationships remain in Relationships tables

### Safety Net (with or without Documentation Plan)

For v1.0 resources that inherit from a base type:
- Cross-reference inherited properties against the **existing v1.0 base type** resource file in `api-reference/v1.0/resources/`
- Flag any inherited property in the derived type's v1.0 doc that does NOT appear in the v1.0 base type's Properties table — these are likely beta-only properties incorrectly carried over

## Partial Promotions

When only some properties/methods/relationships are being promoted (v1.0 resource already exists):

- [ ] Existing v1.0 content **not overwritten** (only promoted items added)
- [ ] Promoted properties merged into existing Properties table (alphabetical order maintained)
- [ ] Promoted methods added to existing Methods table
- [ ] Promoted relationships added to existing Relationships table
- [ ] JSON representation updated for promoted properties only
- [ ] Existing v1.0 examples updated to include promoted properties where applicable

## Enumeration Promotion

- [ ] Enum definition files copied from beta to v1.0
- [ ] Beta disclaimer removed from promoted enum files
- [ ] Version references updated

## TOC Updates

- [ ] `toc.mapping.json` updated in `api-reference/v1.0/toc/`
- [ ] "(preview)" removed from `toc.title` in resource YAML front matter
- [ ] "(preview)" removed from Methods table link text
- [ ] Concept `toc.yml` updated if applicable

## Permission and RBAC Files

- [ ] Permission include files copied from `beta/includes/permissions/` to `v1.0/includes/permissions/`
- [ ] RBAC include files copied from `beta/includes/rbac-for-apis/` to `v1.0/includes/rbac-for-apis/` (if applicable)

## Changelog and What's New

Always required for promotions:

- [ ] Changelog entries present with `Version: "v1.0"`
- [ ] What's New entries for all promoted schema artifacts (resources, properties, enums, methods)
- [ ] What's New links use v1.0 paths (no `?view=graph-rest-beta` query strings)
- [ ] What's New descriptions follow GA promotion patterns:
  - Resource: `Added the [resourceName](/graph/api/resources/{filename}) resource type to the v1.0 endpoint.`
  - Property: `Added the **propertyName** property to the [resourceName](/graph/api/resources/{filename}) resource type in v1.0.`
  - Method: `Added the [methodName](/graph/api/{filename}) method to the [resourceName](/graph/api/resources/{filename}) resource type in v1.0.`
