# Resource Type Documentation Reference

Rules for naming, structuring, and writing resource type documentation files (EntityType, ComplexType, EnumType).

## File Naming Conventions

| Pattern | When to Use | Example |
|---------|-------------|---------|
| `{resourcename}.md` | Standard resource | `user.md`, `message.md` |
| `{subnamespace}-{resourcename}.md` | Subnamespace resource | `security-alert.md` |

All file names must be **lowercase**.

---

## Resource Description

Write 2–4 paragraphs covering:

- What the resource represents and its purpose in Microsoft Graph
- Key capabilities or characteristics
- How it relates to other resources (inheritance, parent/child)
- Common scenarios where it's used
- Whether it's an open type
- For EntityType: supported operations (CRUD, actions, functions)
- For derived types: inheritance hierarchy and base type reference
- Whether it supports `$select`

**Good example:**
> Represents a work or school user account in Microsoft Entra ID. This resource also serves as the entry point for accessing user-related data such as messages, calendar events, and OneDrive files.

---

## Supported Methods Section

- List all supported operations with HTTP methods
- Follow file naming conventions from [api-operation-docs.md](api-operation-docs.md)
- Order: CRUD operations first, then actions/functions
- Concise descriptions for each method

---

## Properties Section

Document **ALL** properties from the CSDL, including inherited properties.

### Required Information Per Property

| Field | Source | Notes |
|-------|--------|-------|
| Name | CSDL `Name` attribute | Case-sensitive, exact match |
| Type | CSDL `Type` attribute | Use OData types: `String`, `Int32`, `Boolean`, `DateTimeOffset`, `Edm.Duration` |
| Description | CSDL annotations / API.md | Purpose and usage |
| Nullable | CSDL `Nullable` attribute | Default is true if not specified |
| Read-only / Immutable / Updatable | API.md / CSDL annotations | Document access pattern |
| Key property | CSDL `<Key>` element | Primary or alternate key |
| Default value | CSDL `DefaultValue` attribute | If applicable |
| Special behaviors | CSDL annotations / API.md | Length limits, syntax rules, required privileges |

### Property Return Behavior

Check for `ags:Default="true"` annotation on each property:

| Annotation | Documentation |
|-----------|--------------|
| `ags:Default="true"` | "Returned by default." — Included automatically without `$select` |
| No annotation | "Returned only on `$select`." — Must be explicitly requested |

### Enum Property References

Properties of enum type should reference the enum type documentation. Include supported values or link to the enum definition.

### Property Description Best Practices

✅ Good:
- "The unique identifier for the user. Read-only."
- "The user's primary email address. Formatted as `user@domain.com`."
- "`true` if the account is enabled; otherwise, `false`."

❌ Poor:
- "The ID" (too vague)
- "Email" (incomplete)
- "Account status" (unclear)

### Query Parameter Support Per Property

- `$filter` — document supported operators per property (e.g., `eq`, `ne`, `startsWith`, `ge`, `le`)
- `$orderby` — list properties that support sorting
- Note `ConsistencyLevel=eventual` requirements where applicable

---

## Relationships Section

Relationships correspond to **navigation properties** in CSDL where `IsHidden` is `false` (or not specified).

Document per relationship:
- Exact name (case-sensitive)
- Description explaining purpose
- Whether it's a collection relationship
- Target resource type
- Supported query parameters (`$expand`, `$filter`, `$orderby`)
- Special behaviors or constraints

If no relationships exist: "This resource has no relationships."

**Hidden navigation properties** (`IsHidden="true"`) are **not** candidates for public documentation.

---

## JSON Representation

Show the data structure with **types, not example values**:

```json
{
  "id": "String",
  "displayName": "String",
  "createdDateTime": "String (timestamp)",
  "isEnabled": "Boolean",
  "addresses": [{"@odata.type": "microsoft.graph.physicalAddress"}]
}
```

Must match the Properties section exactly.

---

## Query Capabilities

Document OData query parameter support:

| Parameter | Scope | Notes |
|-----------|-------|-------|
| `$select` | Resource-wide | List properties not returned by default |
| `$filter` | Per-property | Document operators per property type |
| `$orderby` | Per-property | List sortable properties |
| `$expand` | Per-relationship | List expandable relationships |
| `$top` / `$skip` | Collection | Default and maximum page sizes |
| `$count` | Collection | Note `ConsistencyLevel=eventual` if required |

---

## Enum Type Documentation

Enum types are a special resource type. Key rules:

- **Numeric values are critical** and must match the CSDL exactly
- **Preferred representation** in docs is the member name (not the numeric value)
- Enum docs may be standalone files or inline in the resource that uses them
- **Flagged enums** (allow multiple members) must be clearly indicated
- When an enum changes, update **all** resources/properties that reference it

---

## Polymorphic Base Type Resource Docs

When documenting a base type that has derived types:

- **Properties:** Only the base type's own properties + inherited from parent. Do NOT include derived-type-specific properties.
- **Relationships:** Only relationships defined on the base type.
- **Methods table:** Link to shared operation files using base type naming. For concrete base types, also include base-type-only operations.
- **JSON representation:** Base type structure only.

---

## Common Mistakes to Avoid

- ❌ Missing properties that exist in CSDL (`IsHidden="false"` or not specified)
- ❌ Incorrect property types
- ❌ Undefined relationships to related resources
- ❌ Missing available methods
- ❌ Inconsistent naming (case sensitivity matters)
- ❌ Forgetting to link complex type and enum properties
- ❌ Including derived-type properties in base type resource docs
- ❌ Using example values instead of types in JSON representation
