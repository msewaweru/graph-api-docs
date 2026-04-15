# Deprecation and Retirement Review Criteria

Review criteria for deprecated and retired API documentation.

---

## Deprecation Patterns

### Resource Deprecation

- [ ] "(deprecated)" added to H1 title: `# resourceName resource type (deprecated)`
- [ ] Deprecation banner (CAUTION alert) added after namespace declaration
- [ ] Banner includes: what's deprecated, milestone/sunset date, alternative/workaround, blog post link
- [ ] `toc.title` with "(deprecated)" added to YAML front matter
- [ ] `ROBOTS: NOINDEX` added to YAML front matter
- [ ] All child methods marked deprecated with banners
- [ ] References in other resources marked "(deprecated)"
- [ ] Child type definitions NOT marked deprecated (unless explicitly stated)

### Method Deprecation

- [ ] "(deprecated)" added to H1 title
- [ ] Deprecation banner added with alternative/workaround
- [ ] Parent resource Methods table: "(deprecated)" in link text, moved to end of table
- [ ] Description column includes alternative

### Property Deprecation

- [ ] "(deprecated)" added to property name in Properties table
- [ ] Description updated with alternative
- [ ] Moved to end of table (grouped alphabetically with other deprecated items)
- [ ] "(deprecated)" added in Request body tables (POST/PATCH/PUT) if applicable
- [ ] Response examples updated (removed if no longer returned)
- [ ] "(deprecated)" **NOT** added to JSON representation (causes validation errors)

### Relationship Deprecation

- [ ] "(deprecated)" added to relationship name in Relationships table
- [ ] Description updated, moved to end
- [ ] Corresponding methods with navigation property as segment also deprecated

### Parameter Deprecation

- [ ] "(deprecated)" added to parameter name
- [ ] Description specifies whether to ignore or use alternative
- [ ] Moved to end of table

### Enumeration Deprecation

- See `enum-rules.md` for detailed enum deprecation rules
- [ ] Section title or H1 updated with "(deprecated)"
- [ ] Properties using enum updated with alternatives
- [ ] Deprecated members moved to end with "(deprecated)" suffix

## Deprecation Banner Format

Placed immediately after namespace declaration (before beta disclaimer if beta):

```markdown
> [!CAUTION]
> The {feature name} API is deprecated and will stop returning data on {date}. Use the [{alternative}]({link}) instead.
```

Use include files in `api-reference/includes/` for consistency across topics.

## Version-Specific Rules

| Version | Banner placement | Link format |
|---|---|---|
| **Beta** | After namespace, before beta disclaimer | `?view=graph-rest-beta&preserve-view=true` |
| **v1.0** | After namespace | No query strings |
| **Both** | Apply to both; may have different dates/alternatives | Version-appropriate formatting |

## Mixed Scenarios

When combining deprecation with new APIs/promotions:

- [ ] Non-deprecation tasks processed first (ensures alternatives exist)
- [ ] Deprecation references newly created alternatives
- [ ] Changelog includes both deprecation and addition entries

---

## Retirement (File Deletion)

Retirement = API removed from service. Files are **deleted**, not annotated.

### Deletion Validation

- [ ] All resource files explicitly marked as retired are deleted
- [ ] All API operation files linked to retired resources are deleted
- [ ] Permission include files for retired operations are deleted
- [ ] RBAC include files for retired operations deleted (if not in use by other APIs)
- [ ] Complex types only deleted if explicitly called out as retired
- [ ] Enums only deleted if explicitly called out as retired

### Reference Cleanup

- [ ] Retired resources removed from Methods, Relationships, and Properties tables in parent/related resources
- [ ] TOC entries removed from `toc.mapping.json`
- [ ] Concept topics updated or deleted as appropriate

### Redirects

Required for: entity type resource files (with Methods tables) and API operation files.
Not required for: complex types, enums, permission include files.

- [ ] Redirects added to latest `.openpublishing.redirection.yyyy-mm.json` in `redirects/`
- [ ] `source_path`: file path relative to repo root
- [ ] `redirect_url`: relative URL to alternative on learn.microsoft.com
- [ ] `redirect_document_id`: `false`

```json
{
  "source_path": "api-reference/{version}/resources/{retired-resource}.md",
  "redirect_url": "/graph/api/resources/{alternative-resource}?view=graph-rest-{version}",
  "redirect_document_id": false
}
```

### Changelog and What's New

- [ ] Changelog entries with `ChangeType: "Deletion"`
- [ ] What's New notes API retirement with link to alternative
