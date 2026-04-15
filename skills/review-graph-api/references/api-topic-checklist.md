# API Method Topic Checklist

Validation checklist for API method reference files (`api-reference/*/api/*.md`).

---

## Front Matter

- [ ] `title` field present (leave unchanged)
- [ ] `description` field present and descriptive
- [ ] `doc_type: apiPageType`
- [ ] `ms.date` field present
- [ ] `ms.subservice` populated (no TODO)
- [ ] `author` populated (no TODO)
- [ ] No TODO placeholders remain

## Required Sections (in order)

1. **H1 Title** — begins with imperative verb (Get, Create, Update, List, Delete)
2. **Namespace declaration** — `Namespace: microsoft.graph*` immediately after H1
3. **Beta disclaimer** (beta only) — `[!INCLUDE [beta-disclaimer](../../includes/beta-disclaimer.md)]`
4. **Description** — begins with imperative verb, concise, action-oriented; links to related resources
5. **`## Permissions`** — standard boilerplate text + permissions include
6. **`## HTTP request`** — preceded by `<!-- { "blockType": "ignored" } -->`
7. **Path parameters** (if applicable) — `## Path parameters` table
8. **Function parameters** (if applicable) — `## Function parameters` table; each description states required/optional
9. **Optional query parameters** (if applicable) — `## Optional query parameters`; OData params in code font (`$filter`, `$select`)
10. **`## Request headers`** — includes Authorization; add Content-Type for request body operations
11. **`## Request body`** (if applicable) — for actions/functions, refer to body items as "parameters" not "properties"
12. **`## Response`** — links to returned resource type; for `204 No Content`, return type = "None"
13. **`## Examples`** — one or more examples

## Title and Description

- Title begins with imperative verb: Get, Create, Update, List, Delete, etc.
- Description is concise and action-oriented
- Link to parent resource in description (e.g., "Get a [user](../resources/user.md)")

## Permissions Section

- Standard boilerplate text present
- Permissions include file referenced
- RBAC include (optional) — present if specified in documentation plan

## HTTP Request Section

- Preceded by HTML comment: `<!-- { "blockType": "ignored" } -->`
- Uses **relative URLs**: `GET /users/{id}` (not full `https://...` URLs)
- Placeholders use `{type-id}` format for multiple IDs
- HTTP method matches the operation (GET, POST, PATCH, PUT, DELETE)

## Function Parameters

- Each parameter description states whether **required** or **optional**
- Parameter names in Markdown code font (backticks)
- Types use correct namespace qualification for subnamespace types

## Request Body

- Actions/functions: refer to body items as **"parameters"**, not "properties"
- Only include properties relevant to the operation (exclude read-only ID properties)
- For POST/PATCH targeting polymorphic collections: include `@odata.type` guidance

## Response Section

- Links to returned resource type
- For `204 No Content` responses: return type = "None" (not a resource type)
- Optional H3 errors section for specific error codes

## Examples

- [ ] Each example has **Request** and **Response** blocks
- [ ] Request URLs use **full URLs**: `https://graph.microsoft.com/{version}/...`
- [ ] Domain is `graph.microsoft.com` (not test environments)
- [ ] URL version matches file location (`v1.0` or `beta`)
- [ ] Uses **pseudo-values** (plausible IDs, names), never data type names ("String")
- [ ] HTML comment block precedes each JSON block with `blockType` and `name` attributes
- [ ] `name` attribute values are **unique within the file**
- [ ] For multiple examples: H3 format "Example 1: Description"
- [ ] No `204 No Content` responses include "shortened for readability" notes
- [ ] HTTP method in example matches the operation
- [ ] JSON properties exist and are correctly cased
- [ ] Response aligns with schema (no undocumented properties)

## Code Snippets

- Only HTTP request examples included (no SDK snippets)
- No `# [HTTP](#tab/http)` tab declarations
- No tab boundary markers (`---`)
- No language-specific SDK snippet includes

## Linking Rules

- Links to resource files: `../resources/{filename}.md`
- Links to other API files: `../api/{filename}.md`
- Beta links in What's New: append `?view=graph-rest-beta&preserve-view=true`
- v1.0 links: no query string parameters

## Version-Specific

**Beta files:**
- Beta disclaimer present after namespace
- URLs reference `/beta` endpoint

**v1.0 files:**
- No beta disclaimer
- URLs reference `/v1.0` endpoint
- No national cloud include statement
- No SDK snippet includes
