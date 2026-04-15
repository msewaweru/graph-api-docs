# Resource Topic Checklist

Validation checklist for resource reference files (`api-reference/*/resources/*.md`).

---

## Front Matter

- [ ] `title` field present (leave unchanged)
- [ ] `description` field present and descriptive
- [ ] `doc_type: resourcePageType` (or `enumPageType` for enum topics)
- [ ] `ms.date` field present
- [ ] `ms.subservice` populated (no TODO)
- [ ] `author` populated (no TODO)
- [ ] No TODO placeholders remain

## Required Sections (in order)

1. **H1 Title** — resource name in lowerCamelCase (e.g., `# user resource type`)
2. **Namespace declaration** — `Namespace: microsoft.graph*` immediately after H1
3. **Beta disclaimer** (beta only) — `[!INCLUDE [beta-disclaimer](../../includes/beta-disclaimer.md)]`
4. **Description** — begins with present-tense verb; links to related resources
5. **`## Methods`** — table of supported operations
6. **`## Properties`** — alphabetically ordered property table
7. **`## Relationships`** — alphabetically ordered relationship table
8. **`## JSON representation`** — property names and types matching the Properties table

## Resource Naming Consistency

The resource name must match exactly in all four locations:

| Location | Example |
|---|---|
| YAML `title` | `user resource type` |
| H1 heading | `# user resource type` |
| HTML comment `@odata.type` | `"@odata.type": "#microsoft.graph.user"` |
| JSON `@odata.type` | `"@odata.type": "#microsoft.graph.user"` |

## Description

- Begins with present-tense verbs: "Represents", "Contains", "Defines"
- Active voice preferred
- Don't use resource name to describe its purpose
- Single opening sentence; additional context in separate paragraphs
- Link to base type in derived type topics
- Link to derived types in base type topics
- Link to consuming entity/complex types in complex type topics

### Polymorphic Base Types

If the resource is a base type with derived types sharing endpoints:
- **Abstract base:** State it cannot be instantiated directly
- **Concrete base:** Note the collection may contain both base and derived instances
- List all public derived types with links
- Note instances are differentiated by `@odata.type`

### Polymorphic Derived Types

- Description references and links to base type
- Properties include derived-type-specific + inherited
- Methods section: polymorphic note only (no Methods table) — directs readers to base type

## Methods Table

- Use succinct method names (avoid repeating resource name)
- CRUD order: List, Create, Get, Update, Delete
- Actions/functions: use name without binding parameter
- For `204 No Content` update operations: return type = "None"
- Root resources with no methods: section present with "None." as content
- Every method in the table should have a corresponding API file in `api/`

## Properties Table

- [ ] **Alphabetically ordered** by property name
- [ ] Each row: Property | Type | Description
- [ ] Descriptions use noun phrases ending with periods
- [ ] Empty table → replace with "None."
- [ ] Property references styled with **bold**
- [ ] Resource references styled with **bold** or linked
- [ ] Enum values in inline code (backticks)
- [ ] Boolean capitalized as "Boolean" (not "boolean")
- [ ] Filterable properties document `$filter` support
- [ ] Types use correct namespace qualification for subnamespace types
- [ ] Shared enum properties list only values applicable to this API

## Relationships Table

- [ ] **Alphabetically ordered** by relationship name
- [ ] Present for all resource types (entity types AND complex types)
- [ ] If no relationships: "None."
- [ ] Types use correct namespace qualification for subnamespace types

## JSON Representation

- [ ] Property list matches the Properties table exactly
- [ ] Show **types only** in JSON values (not actual or fictitious values)
- [ ] HTML comment block with `@odata.type` present
- [ ] JSON `@odata.type` matches resource name
- [ ] Enum properties shown as `"String"` type (not enum type name)

## Linking Rules

- Links to API files: `../api/{filename}.md`
- Links to other resource files: `../resources/{filename}.md`
- Subnamespace files: prepend subnamespace to filename (e.g., `security-alert.md`)

## Version-Specific

**Beta files:**
- Beta disclaimer present after namespace declaration
- URLs reference `/beta`

**v1.0 files:**
- No beta disclaimer
- URLs reference `/v1.0`
- No beta-only properties carried over during promotion

## Inheritance Validation

For resources that inherit from a base type:
- Inherited properties present in Properties table
- For v1.0 promoted files: cross-reference inherited properties against v1.0 base type — flag any property not in the v1.0 base type's Properties table (likely beta-only)
