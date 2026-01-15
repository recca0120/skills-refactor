---
name: analyzing-documents
description: "Analyzes documents for insights and patterns. Use when user asks to analyze documents, find patterns, or generate reports."
---

# Document Analyzer

Analyzes documents and generates insights.

## Workflow Diagram

```
┌──────────┐   ┌──────────┐   ┌──────────┐
│  Input   │ → │ Analyze  │ → │  Report  │
│  (📄)    │   │  (🔍)    │   │  (📊)    │
└──────────┘   └──────────┘   └──────────┘
```

## Reference Resources

- **API Documentation**: `docs/api.md`
- **User Guide**: `docs/user-guide.md`
- **Examples**: `docs/examples.md`

## Steps

1. Read the input document
2. Perform analysis
3. Generate insights
4. Create report

## Output Format

| Field | Type | Description |
|-------|------|-------------|
| insights | array | Key findings |
| patterns | array | Detected patterns |
| summary | string | Executive summary |
