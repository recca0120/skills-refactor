# Duplicate Detection Patterns

Guide for identifying duplicate content across Skills.

---

## Types of Duplicates

### 1. Exact Duplicates

Identical text blocks appearing in multiple SKILL.md files.

**Criteria**: Block ≥5 lines, 100% identical content

**Examples**: Reference lists, workflow diagrams, boilerplate sections

```markdown
# In skill-a/SKILL.md
## Reference Resources
- **Workflow guide**: `shared/workflow.md`
- **API reference**: `shared/api.md`
- **Examples**: `shared/examples.md`

# In skill-b/SKILL.md (identical)
## Reference Resources
- **Workflow guide**: `shared/workflow.md`
- **API reference**: `shared/api.md`
- **Examples**: `shared/examples.md`
```

**Action**: Extract to `shared/[name].md`

---

### 2. Near Duplicates

Similar blocks with minor differences (≥80% similarity).

**Criteria**: Block ≥5 lines, same structure, different values

**Examples**: Workflow position diagrams, step instructions, config templates

```markdown
# In skill-a/SKILL.md (stage 1 highlighted)
┌─────────┐   ┌─────────┐   ┌─────────┐
│ Design  │ → │Implement│ → │ Review  │
│  (🔨)   │   │         │   │         │
└─────────┘   └─────────┘   └─────────┘

# In skill-b/SKILL.md (stage 2 highlighted)
┌─────────┐   ┌─────────┐   ┌─────────┐
│ Design  │ → │Implement│ → │ Review  │
│         │   │  (🔨)   │   │         │
└─────────┘   └─────────┘   └─────────┘
```

**Action**: Create parameterized template or reference with context

---

### 3. Structural Duplicates

Same patterns/templates with completely different content.

**Criteria**: Same markdown structure (headers, tables), different actual values

**Examples**: Test plan templates, output specifications, checklists

```markdown
# In skill-a/SKILL.md
| # | Test Name | Type | Purpose |
|---|-----------|------|---------|
| 1 | Login test | Unit | Verify login |

# In skill-b/SKILL.md (same structure)
| # | Test Name | Type | Purpose |
|---|-----------|------|---------|
| 1 | Export test | Unit | Verify export |
```

**Action**: Extract template to shared file

---

## Reporting Format

```markdown
### Duplicate #N: [descriptive name]

- **Type**: exact | near (X%) | structural
- **Locations**:
  - `skill-a/SKILL.md:10-25` (16 lines)
  - `skill-b/SKILL.md:15-30` (16 lines)
- **Action**: [refactoring recommendation]
- **Lines saved**: [estimated reduction]
```
