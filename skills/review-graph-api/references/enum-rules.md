# Enumeration Documentation Rules

Validation rules for enum documentation — creation, updates, promotion, and deprecation.

---

## Three Documentation Options

| Option | Location | When to use |
|---|---|---|
| **Option 1: Global enum file** | `enums.md` or `enums-{subnamespace}.md` | Self-explanatory member names; no descriptions needed |
| **Option 2: Parent resource** | H3 section in resource file after Properties table | Need descriptions; single feature; ≤10 members |
| **Option 3: Separate topic** | Dedicated `{enum-name}.md` file | Many members; multiple unrelated consumers; detailed descriptions needed |

### Auto-Selection Heuristics

1. Doc stub defines enum in `enums.md` or `enums-{subnamespace}.md` → **Option 1**
2. Documentation Plan says "with descriptions" and ≤10 members → **Option 2**
3. Documentation Plan says "with descriptions" and >10 members → **Option 3**
4. Multiple unrelated resources reference the same enum → **Option 3**
5. When uncertain → default to **Option 1** and flag for author review

## Option 1 Rules (Global Enum File)

- H3 section titled `### {enumType} values`
- Table with **Member** column only (no Description or Value columns)
- Members listed in Documentation Plan order
- Evolvable enums: include `unknownFutureValue` in member list
- Property descriptions in parent resource must list all values inline
- JSON representation: property type = `"String"` (not enum type name)
- Note: `enums.md` files are NOT customer-facing (API Doctor validation only)

## Option 2 Rules (Parent Resource)

- H3 section `### {enumType} values` placed after Properties table
- Table with **Member** (required) and **Description** (optional) columns
- Members in ascending order by numeric value (values not exposed)
- Evolvable enums: `unknownFutureValue` description = "Evolvable enumeration sentinel value. Do not use."
- Properties table links to the H3 section
- JSON representation: property type = `"String"`
- Inline listing in property description NOT needed (linked H3 section serves this purpose)

## Option 3 Rules (Separate Topic)

- File naming: `{enum-name}.md` or `{subnamespace}-{enum-name}.md`
- Title: `# {enumType} enum type`
- `doc_type: enumPageType`
- Members H2 section with **Member** and **Description** columns
- Parent resource Properties table links to enum topic
- Inline listing in property description NOT needed
- For subnamespaces: namespace attribute in page annotation at bottom

## Evolvable Enumerations

All evolvable enums must document `unknownFutureValue` sentinel member.

### Scenario 1: `unknownFutureValue` is the last member

- List all values with `unknownFutureValue` last
- No special header required
- Inline format: `"The possible values are: \`value1\`, \`value2\`, \`unknownFutureValue\`."`

### Scenario 2: Members follow `unknownFutureValue`

- Must include `Prefer: include-unknown-enum-members` header note
- Inline format: `"The possible values are: \`value1\`, \`unknownFutureValue\`, \`value3\`. Use the \`Prefer: include-unknown-enum-members\` request header to get the following value or values in this evolvable enum: \`value3\`."`
- For Options 2 & 3: add introductory text before the table explaining the header requirement

## Flagged Enumerations

- Append: "This flagged enumeration allows multiple members to be selected simultaneously."

## Cross-Surface Consistency

When reviewing enum changes, verify consistency across ALL surfaces:

1. **Enum definition** (whichever Option location applies)
2. **Inline property descriptions** in all resources that reference the enum
3. **API topic references** (request body tables, response descriptions)

### Update Checklist

- [ ] New members added to enum definition
- [ ] Order from Documentation Plan maintained
- [ ] All consuming properties updated
- [ ] Inline member lists in property descriptions match definition
- [ ] For evolvable enums: `unknownFutureValue` always documented
- [ ] For new members after `unknownFutureValue`: Prefer header notes updated

## Deprecation

### Entire Enumeration

- In enums.md/parent resource: add "(deprecated)" to section title
- In separate topic: add "(deprecated)" to H1 and add deprecation banner
- Update all properties using the enum with alternative/workaround

### Individual Members

- Add "(deprecated)" to member name in table
- Specify alternative in description
- Move deprecated members to end of table (grouped alphabetically)
- Update property descriptions noting which values are deprecated
