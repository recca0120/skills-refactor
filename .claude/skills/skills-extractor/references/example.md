# Refactoring Example

Complete before/after example of extracting duplicate content.

---

## Scenario

Two skills have identical "Reference Resources" sections.

## Before

**skill-a/SKILL.md**
```markdown
---
name: processing-documents
description: "Processes documents..."
---

# Document Processor

## Reference Resources

- **API Documentation**: `docs/api.md`
- **User Guide**: `docs/user-guide.md`
- **Examples**: `docs/examples.md`

## Steps
1. Read document
2. Process content
```

**skill-b/SKILL.md**
```markdown
---
name: analyzing-documents
description: "Analyzes documents..."
---

# Document Analyzer

## Reference Resources

- **API Documentation**: `docs/api.md`
- **User Guide**: `docs/user-guide.md`
- **Examples**: `docs/examples.md`

## Steps
1. Read document
2. Analyze content
```

---

## After

**shared/reference-resources.md** (new file)
```markdown
# Reference Resources

- **API Documentation**: `docs/api.md`
- **User Guide**: `docs/user-guide.md`
- **Examples**: `docs/examples.md`
```

**skill-a/SKILL.md**
```markdown
---
name: processing-documents
description: "Processes documents..."
---

# Document Processor

## Reference Resources

> See `shared/reference-resources.md`

## Steps
1. Read document
2. Process content
```

**skill-b/SKILL.md**
```markdown
---
name: analyzing-documents
description: "Analyzes documents..."
---

# Document Analyzer

## Reference Resources

> See `shared/reference-resources.md`

## Steps
1. Read document
2. Analyze content
```

---

## Result

| Metric | Before | After |
|--------|--------|-------|
| Total lines | 32 | 26 |
| Duplicate locations | 2 | 0 |
| Shared files | 0 | 1 |

---

## Auto-naming Guidelines

When creating shared files, use these naming conventions:

### Content-based Naming

| Content Type | Generated Name |
|--------------|----------------|
| Workflow diagram (box characters) | `workflow-diagram.md` |
| Reference list (links/resources) | `reference-resources.md` |
| Output format (Field/Type table) | `output-format.md` |
| Step instructions (numbered) | `step-instructions.md` |
| Code snippet | `code-[language].md` |

### Section-based Naming

Use markdown headings when present:

```markdown
## Workflow Diagram    →  shared/workflow-diagram.md
## Reference Resources →  shared/reference-resources.md
## Output Format       →  shared/output-format.md
```

### Naming Rules

1. Convert to `lowercase-with-hyphens`
2. Keep names descriptive but concise
3. Add numeric suffix if name exists: `workflow-diagram-2.md`

### Reference Format

**For exact duplicates:**
```markdown
> See `shared/[name].md`
```

**For near duplicates with context:**
```markdown
> **Current stage: [Stage Name]**
> See `shared/workflow-stages.md` for the full workflow diagram.
```
