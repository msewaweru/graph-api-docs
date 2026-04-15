---
name: generate-doc-plan
description: Analyzes CSDL schema changes (from a local workloads repo or a remote Azure DevOps schema PR) and API.md proposals to generate a structured documentation plan (documentation-plan.md) for Microsoft Graph API reference documentation. Use when preparing to author or review Graph API docs.
---

# Generate Documentation Plan

Analyze CSDL schema changes and produce a comprehensive `documentation-plan.md` that specifies all required Microsoft Graph API documentation work.

This skill supports **two input modes**:

| Mode | When to use | What you need |
|------|------------|---------------|
| **Local repo** | You have the workloads repo cloned and are on a branch with CSDL changes | A terminal open in the `AD-AggregatorService-Workloads` repo |
| **Remote PR** | You want to work entirely from the docs repo | An Azure DevOps schema PR URL |

## Progress Checklist

Track your progress through each step:

```
Doc Plan Generation Progress:
- [ ] Step 1: Determine input mode and collect inputs
- [ ] Step 2: Analyze CSDL changes
- [ ] Step 3: Gather API.md context
- [ ] Step 4: Detect special patterns (polymorphism, deprecation, inheritance)
- [ ] Step 5: Generate documentation plan
- [ ] Step 6: Present summary to user
```

---

## Step 1: Determine Input Mode and Collect Inputs

Ask the user how they want to provide the CSDL schema changes:
- **Option A:** "I have the workloads repo open locally" → go to **Step 1A**
- **Option B:** "I have a schema PR link" → go to **Step 1B**

If the user's message already contains an Azure DevOps PR URL, skip straight to **Step 1B**. If the current working directory is the workloads repo (contains `Workloads/` folder), default to **Step 1A**.

---

### Step 1A: Local Repo Mode

#### 1A.1 Confirm Working Branch

Run `git branch --show-current` and verify the user is **not** on `main`, `master`, or `release`. If they are, **stop** and ask them to switch to a working branch that contains CSDL changes.

#### 1A.2 Confirm CSDL Changes Exist

Run `git diff --name-only HEAD~1..HEAD` (or compare against the base branch) to verify that `schema-Prod-*.csdl` files have been modified under `Workloads/*/override/`.

If no CSDL changes are found, broaden the diff range:
```
git diff --name-only main...HEAD -- "*/schema-Prod-*.csdl"
```

#### 1A.3 Detect Workload Name

Extract the workload name from the changed file paths. The pattern is `Workloads/{WorkloadName}/override/schema-Prod-*.csdl`. If multiple workloads are changed, confirm with the user which one to focus on, or generate plans for each.

#### 1A.4 Check for API.md Proposals

Look in the `Reviews/` folder for API.md files. These follow the pattern:
```
Reviews/{Organization}/{prId}-{workItemId}-{title}/API.md
```

Ask the user if they know which API.md proposal applies, or search `Reviews/` for recently modified files.

**→ Continue to Step 2A**

---

### Step 1B: Remote PR Mode

#### 1B.1 Get the Schema PR Link

Ask the user for the **Azure DevOps schema PR URL** (if not already provided). This is the pull request in the workloads repository (AD-AggregatorService-Workloads) that contains the CSDL schema changes.

The URL follows one of these patterns:
```
https://dev.azure.com/{org}/{project}/_git/{repo}/pullrequest/{prId}
https://dev.azure.com/{org}/{project}/_apis/git/repositories/{repo}/pullRequests/{prId}
```

Parse the URL to extract:
- **Organization** (e.g., `msazure`)
- **Project** (e.g., `One`)
- **Repository name or ID** (e.g., `AD-AggregatorService-Workloads`)
- **PR ID** (numeric)

#### 1B.2 Fetch PR Details

Use the ADO tools to retrieve PR metadata:
1. Call `ado-repo_get_pull_request_by_id` with the repository ID and PR ID to get the PR title, description, source branch, target branch, and status.
2. Call `ado-repo_list_pull_request_threads` to check for any reviewer comments or context about the changes.

From the PR metadata, extract:
- **Source branch name** — this is the working branch with CSDL changes
- **Target branch** — typically `main` or `master`
- **PR description** — often contains links to API.md and context about the changes

#### 1B.3 Identify Changed Files

Use `ado-search_code` or browse the PR diff to find which files changed. Look specifically for:
- `schema-Prod-*.csdl` files under `Workloads/*/override/`
- `API.md` files under `Reviews/`

#### 1B.4 Detect Workload Name

Extract the workload name from the changed file paths. The pattern is `Workloads/{WorkloadName}/override/schema-Prod-*.csdl`. If multiple workloads are changed, confirm with the user which one to focus on, or generate plans for each.

**→ Continue to Step 2B**

---

## Step 2: Analyze CSDL Changes

### Step 2A: Local Diff (from Step 1A)

Run a detailed diff on each modified `schema-Prod-*.csdl` file:

```
git diff main...HEAD -- "Workloads/{Workload}/override/schema-Prod-beta.csdl"
git diff main...HEAD -- "Workloads/{Workload}/override/schema-Prod-v1.0.csdl"
```

**→ Continue to "Parse CSDL Changes" below**

### Step 2B: Remote Diff (from Step 1B)

For each modified `schema-Prod-*.csdl` file found in the PR:

1. **Get the source branch version** — Use `ado-repo_list_directory` with the source branch to locate the CSDL files, then fetch their content using the ADO repository file content APIs.
2. **Get the target branch version** — Fetch the same file from the target branch (typically `main`) for comparison.
3. **Diff the two versions** — Compare the source and target versions to identify what changed.

Alternatively, if the PR diff is available through PR threads or comments, use that directly.

**→ Continue to "Parse CSDL Changes" below**

### Parse CSDL Changes

Parse the diff output to identify changes in these artifact types:

| Artifact | What to Look For |
|----------|-----------------|
| **EntityType** | New types, removed types, `IsHidden` changes, `BaseType` changes, `Abstract` attribute |
| **ComplexType** | New types, removed types, property additions/removals |
| **Property** | Added/removed/modified properties, type changes, nullability changes, `ags:Default` annotation |
| **NavigationProperty** | New relationships, containment changes, `IsHidden` changes |
| **EnumType** | New enums, new members, value changes, members after `unknownFutureValue` |
| **Action** | New actions, binding parameter, return type, parameters |
| **Function** | New functions, binding parameter, return type, parameters |

For each change, record:
- **Namespace** — e.g., `microsoft.graph` or `microsoft.graph.security`
- **API version** — `beta` or `v1.0` (derived from the file name)
- **Change type** — Added, Modified, Deprecated, Unhidden, Removed
- **Details** — Property types, nullability, default values, base types, key properties

### CSDL Annotations to Extract

Look for `Core.Description` annotations and XML comments that provide descriptions:

```xml
<Annotation Term="Core.Description">
  <String>Description text here.</String>
</Annotation>
```

Also check for `ags:Default="true"` on properties (indicates returned by default vs. only on `$select`).

---

## Step 3: Gather API.md Context

The API.md proposal can come from the same source as the CSDL changes (local or remote), or the user may provide a direct link. Ask the user if they have an API.md link, or locate it automatically based on the input mode.

### Step 3A: Local API.md (from Step 1A)

Search the `Reviews/` folder in the local workloads repo for API.md files:
```
Reviews/{Organization}/{prId}-{workItemId}-{title}/API.md
```

Ask the user if they know which API.md proposal applies, or search `Reviews/` for recently modified files:
```
find Reviews/ -name "API.md" -newer $(git log -1 --format=%ci main) 2>/dev/null
```

### Step 3B: Remote API.md (from Step 1B)

Fetch API.md files from the schema PR's source branch. These are typically at `Reviews/{Organization}/{prId}-{workItemId}-{title}/API.md`. Use `ado-repo_list_directory` on the source branch to locate them, then fetch their content using the ADO repository file content APIs.

### Step 3C: Direct Link

If the user provides a direct URL to the API.md file (Azure DevOps file link), fetch it directly. The URL typically follows:
```
https://dev.azure.com/{org}/{project}/_git/{repo}?path=/Reviews/{path}/API.md&version=GB{branch}
```

Parse the URL and use `ado-repo_list_directory` or the ADO file content APIs to retrieve the file.

### Extract from API.md

From the API.md proposals, extract:

- **Scenarios and use cases** — What problems the API solves
- **Supported operations** — GET, POST, PATCH, PUT, DELETE with endpoint paths
- **Query parameter support** — `$select`, `$filter` (with operators), `$expand`, `$count`, `$orderby`, `$top`, `$skip`
- **Example requests and responses** — Realistic sample data
- **Permissions requirements** — Required permissions and admin roles
- **Limitations and special behaviors** — Rate limits, eventual consistency requirements, preview-only features
- **HTTP status codes** — Expected success and error responses

Cross-reference API.md operations with the CSDL changes to ensure completeness.

---

## Step 4: Detect Special Patterns

### 4.1 Polymorphic Entity Types

**Detection:** Find any EntityType that serves as `BaseType` for two or more other EntityTypes where all public (non-hidden) derived types share the same endpoints.

**Steps:**
1. Find EntityTypes referenced as `BaseType` by multiple other types
2. Exclude hidden types (`ags:IsHidden="true"`)
3. Verify in API.md that all public derived types share endpoint paths

**When detected**, apply these rules from [references/api-operation-docs.md](references/api-operation-docs.md):
- Use **base type name** in all operation file names
- Derived types get resource docs but **not** separate operation files
- Document `@odata.type` requirements for POST/PATCH operations
- Show heterogeneous collections in LIST response examples
- Add a ⚠️ caveat block in the documentation plan

### 4.2 Inheritance Relationships

- Track base types and all their derived types
- Note abstract vs. concrete base types
- Base type changes affect all derived types (properties, visibility, deprecation)
- If a derived type is added/unhidden, update the base type's documentation too

### 4.3 Deprecations

When deprecation patterns are detected:
- Extract deprecation date, removal date, reason, and migration path from CSDL annotations/comments
- Flag whether a migration guide is needed (multiple affected resources or significant code changes)
- Track all resources and properties affected by the deprecation
- Note any breaking changes with ⚠️ markers

---

## Step 5: Generate Documentation Plan

Create `documentation-plan.md` with four sections following the structure defined in [references/role-and-scope.md](references/role-and-scope.md).

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

Build a comprehensive table with columns:

| Artifact Type | Object Name | Change Type | Details | Additional Context |
|--------------|-------------|-------------|---------|-------------------|

Include the change type legend and critical markers as defined in [references/role-and-scope.md](references/role-and-scope.md).

**Key rules:**
- Document property-level changes, not just type-level
- Include data types, nullability, and default values for new properties
- For deprecations, always include deprecation date and removal date
- For enum changes, note position relative to `unknownFutureValue`
- For inheritance changes, list all inherited/derived types affected

### Section 3: Documentation Plan

Use checkbox format organized by documentation type. Follow the templates in [references/role-and-scope.md](references/role-and-scope.md):

1. **Entity Type Resource Documentation** — Per entity: state (New/Updated/Deprecated), file path, supported operations matrix, resource documentation actions, per-operation action items
2. **Complex Type Resource Documentation** — Per complex type: state, file path, parent references, property documentation actions
3. **Enum Type Documentation** — Per enum: state, file path, member table updates, usage tracking across resources
4. **API Operation Documentation** — Per operation file: action items for request/response examples, query parameters, permissions
5. **Migration Guides** — Only if deprecations require dedicated guidance
6. **Concept Documentation** — Only if introducing a new service or major feature area

**File naming rules** (see [references/api-operation-docs.md](references/api-operation-docs.md) and [references/resource-type-docs.md](references/resource-type-docs.md)):
- Resource files: `{resourcename}.md` or `{subnamespace}-{resourcename}.md`
- API operations: `{resourcename}-{operation}.md`, `{parentresource}-list-{navprop}.md`, `{parentresource}-post-{navprop}.md`
- For subnamespaces: prefix with `{subnamespace}-`
- For polymorphic collections: use **base type name** in all operation file names
- All filenames must be **lowercase**

**Polymorphic collection caveat block** (include when applicable):

> ⚠️ **Note:** `{derivedTypeName}` is part of a polymorphic collection managed by the base type `{baseTypeName}`.
> **Recommended files:** `{basetype}-list.md`, `{basetype}-get.md`, `{parenttype}-post-{collection}.md`, `{basetype}-update.md`, `{basetype}-delete.md`
> **Verification needed:** Confirm in API.md that all derived types share the same endpoints. If endpoints differ, separate files may be required.

### Section 4: Validation Checklist

Include the standard checklist covering:

**Technical Accuracy:**
- All property names match CSDL exactly (case-sensitive)
- All property types and nullability match CSDL
- Enum values match CSDL exactly (numeric values correct)
- Deprecation dates are accurate
- HTTP status codes are correct for each operation

**Completeness:**
- All new properties documented with full descriptions
- All deprecated items have clear migration guidance
- Examples include new properties with realistic values
- Breaking changes clearly highlighted
- All `IsHidden=false` elements are documented
- Inherited properties documented for derived types

**Standards Compliance:**
- Internal links between related docs work correctly
- File naming conventions followed (subnamespace prefixes, lowercase)
- Consistent terminology throughout
- Proper Markdown formatting
- Follows Microsoft Graph documentation patterns

---

## Step 6: Present Summary to User

After generating the plan:

1. **Save** the file using a workload-scoped path so multiple plans can coexist:
   ```
   temp-docstubs/{workload-name}/documentation-plan.md
   ```
   Where `{workload-name}` is the lowercase workload name detected in Step 1 (e.g., `temp-docstubs/defender-for-identity/documentation-plan.md`). Create the subdirectory if needed.
2. **Print a summary** showing:
   - Total number of changes detected
   - Number of new/updated/deprecated artifacts
   - Number of documentation files to create or update
   - Any special patterns detected (polymorphism, deprecations, breaking changes)
   - Warnings or items needing manual verification
3. **Ask the user** to review the plan and confirm before proceeding to documentation authoring

---

## Reference Files

For detailed rules on specific documentation types, consult:

- [references/role-and-scope.md](references/role-and-scope.md) — Documentation plan template, CSDL analysis workflow, validation checklist
- [references/api-operation-docs.md](references/api-operation-docs.md) — Operation file naming, polymorphic handling, description guidelines
- [references/resource-type-docs.md](references/resource-type-docs.md) — Resource file naming, property documentation, relationship rules
