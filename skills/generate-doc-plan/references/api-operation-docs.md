# API Operation Documentation Reference

Rules for naming, structuring, and writing API operation documentation files.

## File Naming Conventions

### Standard CRUD Operations

| Pattern | When to Use | Example |
|---------|-------------|---------|
| `{resourcename}-get.md` | GET single resource | `user-get.md` |
| `{resourcename}-list.md` | LIST top-level collection | `user-list.md` |
| `{resourcename}-update.md` | PATCH or PUT resource | `user-update.md` |
| `{resourcename}-delete.md` | DELETE resource | `user-delete.md` |

### Navigation Property Operations

When CSDL defines the resource as a return type of a navigation property:

| Pattern | When to Use | Example |
|---------|-------------|---------|
| `{parent}-list-{navprop}.md` | LIST via nav property | `group-list-members.md` |
| `{parent}-post-{navprop}.md` | POST via nav property | `group-post-members.md` |
| `{parent}-delete-{navprop}.md` | DELETE via nav property | `group-delete-members.md` |

### Actions and Functions

| Pattern | When to Use | Example |
|---------|-------------|---------|
| `{bindingtype}-{actionname}.md` | Bound action | `message-send.md` |
| `{bindingtype}-{functionname}.md` | Bound function | `user-getmembergroups.md` |

> Note: The binding parameter is the **first parameter** of a bound method, regardless of whether `bindingParameter` is explicitly specified in the CSDL.

### Subnamespace Prefix

For APIs in subnamespaces (e.g., `microsoft.graph.security`), prefix all file names:

| Pattern | Example |
|---------|---------|
| `{subnamespace}-{resource}-{operation}.md` | `security-alert-get.md` |
| `{subnamespace}-{resource}-list-{navprop}.md` | `identitygovernance-lifecycleworkflowscontainer-list-workflows.md` |
| `{subnamespace}-{resource}-{action}.md` | `security-alert-dismiss.md` |

### All File Names Must Be Lowercase

✅ `customauthenticationextension-get.md`
❌ `customAuthenticationExtension-get.md`

---

## Polymorphic Entity Type Handling

### Detection

A polymorphic entity type exists when:
1. An EntityType is referenced as `BaseType` by **two or more** other EntityTypes
2. All public derived types (without `ags:IsHidden="true"`) share the **same endpoints**
3. API.md confirms operations use the same URIs for all derived types

**Steps to detect:**
1. Find an EntityType referenced as `BaseType` by 2+ types
2. Exclude hidden types (`ags:IsHidden="true"`)
3. Verify shared endpoint paths in API.md

### Abstract vs. Concrete Base Types

| Type | Behavior |
|------|----------|
| **Abstract** (`Abstract="true"`) | Cannot be instantiated directly; only derived types in collections |
| **Concrete** | Can be instantiated alongside derived types; may have its own actions/functions |

### File Naming for Polymorphic Collections

**Always use the base type name**, not derived type names, in operation file names.

✅ `customauthenticationextension-get.md`, `identitycontainer-list-customauthenticationextensions.md`
❌ `ontokenissuancestartcustomextension-get.md`

Derived types get **resource docs only** — not separate operation files.

### @odata.type Rules

| Operation | Rule |
|-----------|------|
| **GET** (single/collection) | Included automatically in response |
| **POST / PUT** | **Required** in request body — list all valid `@odata.type` values |
| **PATCH** | **Required** in request body — note before the property table |
| **LIST response** | Each item includes `@odata.type` to differentiate derived types |

### Request Body Properties (POST/PATCH/PUT)

| Scenario | Rule |
|----------|------|
| Property on base type | One row. Note: "Inherited from [parentType]" |
| Same property, same type across derived types | One row. Note: "Applies to: **type1**, **type2**" |
| Same property, different type per derived type | Separate row per type. Note: "For **derivedType** only" |
| Required for some, optional for others | State per type: "Required for **type1**. Optional for **type2**" |
| Unique to one derived type | One row. Note: "**For derivedType only**" |

**POST intro paragraph:**
> In the request body, supply a JSON representation of the [baseType] object. You must specify the `@odata.type` property to indicate the specific derived type.

**PATCH intro paragraph:**
> [!INCLUDE [table-intro](../../includes/update-property-table-intro.md)]
> You must specify the `@odata.type` property to indicate the specific derived type.

### Response Examples

**LIST:** Show heterogeneous collection with 2–3 different derived types, each with `@odata.type`.

**GET/POST/PATCH:** Show one derived type per example. Include multiple examples when demonstrating type-specific properties.

### Caveat Block for Documentation Plans

When a derived type belongs to a polymorphic collection, include:

> ⚠️ **Note:** `{derivedTypeName}` is part of a polymorphic collection managed by the base type `{baseTypeName}`.
> **Recommended files:** `{basetype}-list.md`, `{basetype}-get.md`, `{parenttype}-post-{collection}.md`, `{basetype}-update.md`, `{basetype}-delete.md`
> **Verification needed:** Confirm in API.md that all derived types share the same endpoints.

### Non-Polymorphic Derived Types

When derived types have **different endpoints**, each requires separate operation docs following standard naming conventions.

### Concrete Base Types with Own Operations

Document base-type-only actions/functions as separate operation files using the base type name (e.g., `engagementconversationmessage-vote.md`), in addition to shared CRUD operations.

---

## Operation Description Guidelines

Write 1–3 paragraphs covering:
- What the operation does and when to use it
- Key behaviors or limitations (status codes, returned objects, error conditions, required headers)
- Prerequisites and special permissions (e.g., Microsoft Entra ID admin roles)

### Standard HTTP Status Codes

| Method | Success Code |
|--------|-------------|
| GET | `200 OK` + resource object |
| POST | `201 Created` + resource object |
| PATCH | `200 OK` + updated resource object |
| DELETE | `204 No Content` (no body) |
| Action | Varies, often `204 No Content` |
| Function | `200 OK` + return type object |

Always use the full format: `200 OK`, `201 Created`, `204 No Content`, etc.

### For Polymorphic Operations

1. If base is abstract: state it cannot be instantiated directly. If concrete: note collection may contain base and derived instances.
2. List all public derived types with links and brief descriptions.
3. State instances are differentiated by `@odata.type`.
4. Keep descriptions focused on behavior; save type-specific details for Examples.

---

## Permissions Documentation

- Permissions are handled through a separate process
- Only document admin roles beyond standard permissions (e.g., "Microsoft Entra role: Security Administrator")
- Exclude Global Administrator unless it's the only role

---

## Common Mistakes to Avoid

- ❌ TODO markers in final documentation
- ❌ Missing or incomplete permissions
- ❌ Examples that don't match CSDL schema
- ❌ Incorrect HTTP status codes
- ❌ Missing required headers
- ❌ Forgetting beta disclaimer for beta APIs
- ❌ Broken internal links to resource documentation
- ❌ Using derived type names for polymorphic operation files
