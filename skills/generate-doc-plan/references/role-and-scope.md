# Role and Scope Reference

Quick-reference rules for CSDL analysis and documentation plan generation.

## Publication Scope

- **Public cloud APIs only** — Beta and v1.0
- CSDL files: `schema-Prod-beta.csdl` and `schema-Prod-v1.0.csdl` in `Workloads/{Workload}/override/`
- Do not document internal, sovereign cloud, or pre-release-only APIs

## Information Sources (Priority Order)

1. **CSDL schema files** — Source of truth for API definitions
2. **API.md proposals** — Scenarios and design decisions (`Reviews/{Org}/{prId}-{workItemId}-{title}/API.md`)
3. **Permissions JSON files** — Required permission details
4. **Existing Graph docs** — For patterns and consistency
5. **Graph API Guidelines** — Design patterns and best practices

## Key Documentation Principles

- **Completeness** — All publicly exposed schemas must be documented
- **Active voice** — Use simple, direct language
- **Consistency** — Match existing Microsoft Graph documentation patterns
- **Developer focus** — Write for developers of varying experience levels; show real-world use cases
- **Working examples** — Provide correct code examples; highlight common scenarios and pitfalls

---

## CSDL Analysis Workflow

### Step 1: Validate Working Branch

```bash
git branch --show-current
```

⚠️ **STOP** if on `master`, `main`, `release`, or default branch.

### Step 2: Identify CSDL Changes

```bash
git diff main...HEAD -- "*/schema-Prod-*.csdl"
```

Parse for: EntityType, ComplexType, Property, NavigationProperty, EnumType, Action, Function changes.

Record per change:
- Namespace (`microsoft.graph`, `microsoft.graph.security`, etc.)
- Version (beta / v1.0)
- Change type (Added / Modified / Deprecated / Unhidden / Removed)

**Inheritance rules:**
- Base type changes affect all derived types
- Abstract type changes require updates for all concrete derived types
- If a derived type is added/unhidden, the base type doc also needs updating

### Step 3: Gather API.md Context

Extract: scenarios, supported operations, query parameters, example requests/responses, permissions, limitations.

### Step 4: Identify Documentation Needs

Map changes to required documentation files (resource docs, operation docs, enum docs, migration guides, concept docs).

### Step 5: Output Documentation Plan

Save as `documentation-plan.md`.

---

## Documentation Plan Template

### Section 1: Overview

```markdown
# Branch Changes Summary: {branch-name}

## Overview
{2-3 sentence description of what changed and why}

**Workload**: {Workload.Name}
**File(s) Modified**: `{Full path to modified CSDL file(s)}`
**API Version**: Beta | v1.0 | Both
**API Proposal(s)**: [`Reviews/{path}/API.md`](Reviews/{path}/API.md)
**Onboarding Branch**: `{working-branch-name}`
**Document Generated**: {YYYY-MM-DD}
---
```

### Section 2: Changes Summary

| Artifact Type | Object Name | Change Type | Details | Additional Context |
|--------------|-------------|-------------|---------|-------------------|
| **EntityType** | `typeName` | Added | New entity type | Base type: `baseType`<br>Key: `keyProp`<br>Properties: N |
| **EntityType** | `typeName` | Unhidden | IsHidden: true → false | Abstract: Yes/No<br>Derived types: `type1`, `type2` |
| **EntityType** | `typeName` | Deprecated | Entire type | **Deprecated:** YYYY-MM-DD<br>**Removed:** YYYY-MM-DD<br>**Migration:** Use `newType` |
| **ComplexType** | `typeName` | Added | New complex type | Properties: `prop1` (Type), `prop2` (Type) |
| **Property** | `type.propName` | Added | New property | Type: `Edm.Type`<br>Nullable: true/false |
| **Property** | `type.propName` | Modified | Type change | From `oldType` to `newType` ⚠️ **Breaking** |
| **NavigationProperty** | `entity.navProp` | Added | New navigation | Type: `Collection(target)` or `target`<br>Containment: true/false |
| **EnumType** | `enumName` | Added | New enum with N members | Members: `m1(0)`, `m2(1)`, `unknownFutureValue(N)`<br>Used by: `type.prop` |
| **EnumType** | `enumName` | Modified | Added member(s) | Added: `newMember(val)` after `unknownFutureValue`<br>Used by: `type.prop` |
| **Action** | `actionName` | Added | New action | Binding: `type`<br>Returns: `returnType`<br>Params: `p1`, `p2` |
| **Function** | `funcName` | Added | New function | Binding: `type`<br>Returns: `returnType`<br>Params: `p1`, `p2` |

#### Change Type Legend

- **Added** — New artifact or member introduced
- **Modified** — Existing artifact changed (type, nullability, value)
- **Deprecated** — Marked for removal with sunset timeline
- **Unhidden** — Made public (IsHidden: true → false)
- **Removed** — Deleted from schema

#### Critical Markers

- ⚠️ **Breaking** — May break existing client code
- **Deprecated** — Must include: deprecation date, removal date; optionally: reason, migration path

#### Documentation Guidelines for Changes Table

- Document **property-level** changes, not just type-level
- Include data types, nullability, default values for new properties
- For deprecations: always include deprecation date and removal date
- For enum changes: note position relative to `unknownFutureValue`; document which properties use the enum
- For inheritance changes: list all inherited/derived types; document impact

### Section 3: Documentation Plan

Use checkbox format. Organize by documentation type:

#### 1. Entity Type Resource Documentation

Per entity type:

```markdown
**EntityType**: {Name}
**State**: New | Updated | Deprecated
**File**: `api-reference/{version}/resources/{namespace}-{name}.md`
```

**State mapping:**
- **New** = Added or Unhidden → create new doc file
- **Updated** = Modified → update existing doc file
- **Deprecated** = Deprecated or Removed → add deprecation notices

Include:
- **Supported operations matrix** — table with Operation | Supported | File name | Query Parameters
- **Resource documentation actions** — checkboxes for description, properties table, JSON representation, relationships, inherited properties
- **Property return behavior** — check `ags:Default="true"` annotation: with it = "Returned by default"; without = "Returned only on $select"
- **Per-operation action items** — checkboxes for request/response examples, query parameters, permissions

#### 2. Complex Type Resource Documentation

Per complex type:

```markdown
**ComplexType**: {Name}
**State**: New | Updated | Deprecated
**File**: `api-reference/{version}/resources/{namespace}-{name}.md`
```

Include:
- **Referenced by** — list parent entities/types that use this complex type
- **Action items** — description, properties table, JSON representation, inheritance notes, parent links

#### 3. Enum Type Documentation

Per enum:

```markdown
**EnumType**: {Name}
**State**: New | Updated | Deprecated
**File**: `api-reference/{version}/resources/{name}.md` or inline
```

Include:
- Member table updates
- Usage tracking (which resources/properties reference this enum)
- Deprecation notices for removed members

#### 4. Migration Guides

**Create when:** deprecation affects multiple resources, requires significant code changes, no existing guidance.
**Skip when:** simple renames, single-resource deprecations, migration covered in resource/API docs.

#### 5. Concept Documentation

- **Service-level overview** — for new services (`concepts/{feature}-overview.md`)
- **API overview** — for new TOC nodes (`api/resources/{namespace}-{feature}-api-overview.md`)
- **Update existing** — for changes to existing features

### Section 4: Validation Checklist

```markdown
### Documentation Validation Checklist

**Technical Accuracy:**
- [ ] All property names match CSDL exactly (case-sensitive)
- [ ] All property types and nullability match CSDL
- [ ] Enum values match CSDL exactly (numeric values correct)
- [ ] Deprecation dates are accurate
- [ ] HTTP status codes are correct for each operation

**Completeness:**
- [ ] All new properties documented with full descriptions
- [ ] All deprecated items have clear migration guidance
- [ ] Examples include new properties with realistic values
- [ ] Breaking changes clearly highlighted
- [ ] All IsHidden=false elements are documented
- [ ] Inherited properties documented for derived types

**Standards Compliance:**
- [ ] Internal links between related docs work correctly
- [ ] File naming conventions followed (subnamespace prefixes, lowercase)
- [ ] Consistent terminology throughout
- [ ] Proper Markdown formatting
- [ ] Follows Microsoft Graph documentation patterns
```

---

## JSON Representation vs. Examples

**Resource docs — JSON Representation** — Show data structure with types:
```json
{
  "id": "String",
  "displayName": "String",
  "createdDateTime": "String (timestamp)"
}
```

**Operation docs — Examples** — Show realistic values:
```json
{
  "id": "12345",
  "displayName": "John Doe",
  "createdDateTime": "2024-01-15T09:30:00Z"
}
```

---

## Folder Structure Conventions

| Folder | Content |
|--------|---------|
| `resources/` | EntityType, ComplexType, and enum documentation |
| `api/` | API operation documentation (GET, POST, PATCH, PUT, DELETE, actions, functions) |
| `concepts/` | Conceptual/overview documentation, migration guides, tutorials |
