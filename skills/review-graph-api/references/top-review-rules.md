# Top 13 Review Rules

These are the most commonly violated rules in Microsoft Graph API documentation. Check these first for every file.

## 1. Lowercase Filenames

All filenames must be lowercase. PR will be blocked otherwise.

- ✅ `security-alert-get.md`
- ❌ `Security-Alert-Get.md`

## 2. Namespace Declaration

`Namespace: microsoft.graph` (or subnamespace like `Namespace: microsoft.graph.security`) must appear immediately after the H1 title.

## 3. Alphabetical Ordering

- **Properties** in Properties tables must be in alphabetical order
- **Relationships** in Relationships tables must be in alphabetical order
- **H3 headings** in What's New must be in alphabetical order within each month section

## 4. Beta Disclaimer

- **Required** in all beta files, immediately after the namespace declaration:
  ```
  [!INCLUDE [beta-disclaimer](../../includes/beta-disclaimer.md)]
  ```
- **Must be absent** in all v1.0 files

## 5. No TODO Placeholders

Search all changed files for `TODO`. None should remain in committed documentation.

## 6. No Custom H2 Sections

Only predefined H2 headings are allowed for API and resource topics. Do not add custom H2 sections.

**API topics:** Permissions, HTTP request, Path parameters, Function parameters, Optional query parameters, Request headers, Request body, Response, Examples
**Resource topics:** Methods, Properties, Relationships, JSON representation

## 7. Pseudo-Values in Examples

Examples must use pseudo-values (plausible IDs, names, emails), never data type names.

- ✅ `"displayName": "Adele Vance"`
- ❌ `"displayName": "String"`

## 8. Full URLs in Examples

Example request URLs must use full URLs with the domain:

- ✅ `GET https://graph.microsoft.com/v1.0/users`
- ❌ `GET /users`

## 9. Relative URLs in HTTP Request Section

The HTTP request syntax section must use relative URLs with the HTTP method:

- ✅ `GET /users`
- ❌ `GET https://graph.microsoft.com/beta/users`

The HTTP request must be preceded by `<!-- { "blockType": "ignored" } -->`.

## 10. Resource Name Consistency

The resource name must match exactly (including casing) in all four locations:

1. YAML front matter `title` field
2. H1 page title
3. HTML comment block `@odata.type` in JSON representation
4. JSON `@odata.type` in JSON representation

## 11. JSON Representation Matches Properties Table

The properties listed in the JSON representation section must match the Properties table — same property names, same types. Show types only in JSON (not actual or fictitious values).

## 12. Validation Scripts Pass

- **Changelog:** Run `.\scripts\validate-changelog-json.ps1` when changelog files are modified
- **temp-docstubs:** Run `.\scripts\validate-temp-docstubs.ps1` when temp-docstubs folder was used

## 13. Production URL Versions Only

URLs must contain only `v1.0` or `beta`. Flag any non-production versions:

- ❌ `ppeprod`, `ppe`, `ppe-beta`, `ppe-v1.0`
- ❌ `staging`, `stagingbeta`, `stagingv1.0`
- ❌ Any other test environment versions
