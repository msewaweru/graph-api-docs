# generate-doc-plan

Analyzes CSDL schema changes in an AD-AggregatorService-Workloads branch and generates a structured `documentation-plan.md` for Microsoft Graph API reference documentation. The plan covers all required resource docs, API operation docs, enum updates, migration guides, and concept docs — organized with checkboxes for tracking progress.

## Prerequisites

- You must be on a **working branch** (not `main`, `master`, or `release`) that contains changes to `schema-Prod-*.csdl` files under `Workloads/*/override/`.
- For best results, have **API.md proposals** available in the `Reviews/` folder. These provide scenarios, operations, query parameters, and example requests/responses that enrich the plan.
- The workspace should be the **AD-AggregatorService-Workloads** repository (or a clone of it).

## How to Invoke

Example prompts:

- `Generate a documentation plan for the CSDL changes in this branch.`
- `Analyze the schema changes and create a doc plan.`
- `What documentation do we need for the Identity workload changes?`
- `Create a documentation-plan.md for the beta API changes.`

## Expected Output

A `documentation-plan.md` file with four sections:

1. **Overview** — Branch name, workload, API version(s), modified files, generation date.
2. **Changes Summary** — Table of all CSDL changes (EntityType, ComplexType, Property, NavigationProperty, EnumType, Action, Function) with change types, details, and critical markers.
3. **Documentation Plan** — Checkbox-format list of all required documentation work: resource docs, operation docs, enum docs, migration guides, and concept docs. Includes file names, action items, and operation matrices.
4. **Validation Checklist** — Technical accuracy, completeness, and standards compliance checks.

## Tips

- **Run in the workloads repo** — The skill expects `Workloads/` and `Reviews/` folder structures.
- **Verify your branch first** — Run `git branch --show-current` to confirm you're not on a default branch.
- **Provide API.md links** — If you know which API.md proposal applies, mention it. The skill will search `Reviews/` otherwise.
- **Multiple workloads** — If your branch touches multiple workloads, the skill will ask which to focus on or generate plans for each.
- **Polymorphic types** — The skill automatically detects polymorphic entity types and adjusts file naming and documentation requirements accordingly.
- **Deprecations** — Deprecated artifacts are flagged with migration guide recommendations when the change is significant.
