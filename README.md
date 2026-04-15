# graph-api-docs

Generate documentation plans and review Microsoft Graph API reference documentation. This plugin bundles two skills that cover the documentation lifecycle — from CSDL analysis through PR review.

## Skills

| Skill | What it does |
|-------|--------------|
| [`generate-doc-plan`](skills/generate-doc-plan/README.md) | Analyze CSDL schema changes and API.md proposals to produce a structured `documentation-plan.md`. |
| [`review-graph-api`](skills/review-graph-api/README.md) | Review PR documentation against the Documentation Plan and Microsoft Graph authoring guidelines. |

## Recommended workflow

The two skills are designed to run in sequence, with the Documentation Plan bridging them:

```text
┌──────────────────┐   documentation-plan.md   ┌──────────────────┐
│ generate-doc-plan│ ────────────────────────→ │ review-graph-api │
│                  │                            │                  │
│ Workloads repo   │                            │ Docs repo        │
│ (CSDL schemas)   │                            │ (PR with docs)   │
└──────────────────┘                            └──────────────────┘
```

### 1. Generate the documentation plan

Open the workloads repository on the branch with CSDL changes, then invoke the skill:

```text
Generate a documentation plan for the CSDL changes on this branch
```

The skill analyzes `schema-Prod-*.csdl` diffs, cross-references `Reviews/` API.md proposals, and outputs a structured `documentation-plan.md` with four sections:

1. **Overview** — Branch name, workload, API version, files modified
2. **Changes Summary** — Table of all CSDL changes by artifact type
3. **Documentation Plan** — Checklist of all required doc work (entity types, complex types, enums, operations)
4. **Validation Checklist** — Technical accuracy, completeness, and standards compliance checks

### 2. Review the documentation PR

Switch to the docs repository, place the `documentation-plan.md` in `temp-docstubs/`, and invoke the review skill:

```text
Review PR #1234 against the documentation plan
```

The skill runs a multi-phase validation:

- **Phase A** — Traceability map (plan → files → source)
- **Phase B** — Structural integrity (templates, sections, headings)
- **Phase C** — Completeness (scope coverage)
- **Phase D** — Technical accuracy (descriptions, properties, examples)
- **Phase E** — Cross-file consistency (enums, polymorphic types, versions)

Output is a severity-ranked review report with PR comments.

## Installation

Install from the GitHub repository:

```bash
copilot plugin install <owner>/graph-api-docs
```

Or load locally during development:

```bash
copilot --plugin-dir /path/to/graph-api-docs
```

## Invocation examples

| What you want to do | Example prompt | Skill |
|---|---|---|
| Generate a doc plan | "Generate a documentation plan for the CSDL changes on this branch" | `generate-doc-plan` |
| Review a PR | "Review PR #1234 for Microsoft Graph docs compliance" | `review-graph-api` |
| Review with plan | "Review PR #1234 against the documentation plan in temp-docstubs" | `review-graph-api` |
| Review workspace files | "Review the changed files in this workspace" | `review-graph-api` |

### Slash commands

```text
/graph-api-docs:generate-doc-plan
/graph-api-docs:review-graph-api
```
