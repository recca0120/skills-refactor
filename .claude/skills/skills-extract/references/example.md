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
