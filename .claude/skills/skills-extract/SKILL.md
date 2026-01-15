---
name: extracting-skills
description: "Extracts duplicate content from Skills into shared modules. Use when user asks to refactor skills, extract common content, or merge duplicates."
---

# Skills Extractor

## When to Use

This is **Step 2** of the refactoring workflow:

1. analyze → Must run first to identify duplicates
2. **extract** ← You are here
3. validate → Run after extraction completes

**Prerequisite**: Analysis report with identified duplicates.

## Workflow

### Step 1: Review Duplicates

From the analysis report, identify:
- What content is duplicated
- Where it appears (file:line)
- Which strategy to use

### Step 2: Choose Strategy

| Duplicate Type | Strategy | When to Use |
|----------------|----------|-------------|
| Exact (100%) | Extract | Identical blocks across files |
| Near (≥80%) | Parameterize | Same structure, minor differences |
| Structural | Template | Same pattern, different values |

### Step 3: Execute

**Extract** (for exact duplicates):
```
1. Create shared/[name].md with the content
2. Replace original with: > See `shared/[name].md`
```

**Parameterize** (for near duplicates):
```
1. Create shared/[name].md with {{placeholders}}
2. Each skill references + provides specific values
```

### Step 4: Verify

- [ ] All references point to existing files
- [ ] YAML frontmatter intact in all SKILL.md
- [ ] No content accidentally removed

## Example

> See `references/example.md` for a complete before/after refactoring example.

## Output

```markdown
# Extraction Complete

## Changes
- Created: shared/[name].md ([n] lines)
- Modified: [skill]/SKILL.md (-[n] lines)

## Summary
- Duplicates eliminated: [n] locations → 1 source
```

## Next Step

Run **skills-validate** to verify refactoring
