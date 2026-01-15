# Shared Reference Template

Use this template when creating new shared reference files.

---

## File Header

```markdown
# [Descriptive Title]

Brief description of what this shared content contains.

---
```

## Content Sections

Organize content with clear headers:

```markdown
## Section Name

Content here...

### Subsection (if needed)

More detailed content...
```

## Usage Instructions

At the end of the shared file, include usage instructions:

```markdown
---

## How to Use

Reference this file from your SKILL.md:

\`\`\`markdown
> See \`shared/[this-file].md\` for [description].
\`\`\`
```

---

## Examples

### Example 1: Reference List

```markdown
# Common Reference Resources

Shared resources used across multiple skills.

---

## Documentation

- **API Reference**: `docs/api.md`
- **User Guide**: `docs/user-guide.md`
- **FAQ**: `docs/faq.md`

## Templates

- **Test Plan**: `templates/test-plan.md`
- **Output Format**: `templates/output-format.md`

---

## How to Use

Reference from SKILL.md:
> See `shared/reference-resources.md` for available documentation.
```

### Example 2: Workflow Diagram

```markdown
# Workflow Position Diagram

Visual representation of the skill workflow stages.

---

## Stages

```
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│  Design  │ → │Implement │ → │  Review  │ → │ Deploy   │
└──────────┘   └──────────┘   └──────────┘   └──────────┘
```

## Stage Descriptions

| Stage | Description |
|-------|-------------|
| Design | Define requirements and architecture |
| Implement | Write code following TDD |
| Review | Code review and testing |
| Deploy | Release to production |

---

## How to Use

In your SKILL.md, indicate current stage:
> Current workflow stage: **[Stage Name]**
> See `shared/workflow-diagram.md` for full workflow.
```

### Example 3: Parameterized Template

```markdown
# Test Plan Template

Standard format for test plans across skills.

---

## Template

| # | Test Name | Type | Purpose |
|---|-----------|------|---------|
| 1 | {{test_1_name}} | {{test_1_type}} | {{test_1_purpose}} |
| 2 | {{test_2_name}} | {{test_2_type}} | {{test_2_purpose}} |
| 3 | {{test_3_name}} | {{test_3_type}} | {{test_3_purpose}} |

## Test Types

- **Unit**: Tests individual functions
- **Integration**: Tests component interactions
- **E2E**: Tests complete user flows

---

## How to Use

Copy the template structure and fill in your specific tests:
> Test plan follows format from `shared/templates/test-plan.md`
```
