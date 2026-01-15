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

From the analysis report, identify what content is duplicated and where.

### Step 2: Choose Strategy

| Duplicate Type | Strategy |
|----------------|----------|
| Exact (100%) | Extract to shared file |
| Near (≥80%) | Parameterize or extract |
| Structural | Create template |

### Step 3: Generate Dry-Run Plan

Show preview before making changes:
- What shared file will be created
- What lines will be replaced in each SKILL.md

> Example: `references/example.md`

### Step 4: Execute with Confirmation

After user confirms:
1. Create shared file
2. Update each SKILL.md with reference
3. Verify all references are valid

## Output

```markdown
# Extraction Complete

## Changes Made
- Created: shared/[name].md ([n] lines)
- Modified: [skill]/SKILL.md (-[n] lines)

## Summary
- Duplicates eliminated: [n] → 1
- Lines saved: [total]
```

## Next Step

Run **skills-validate** to verify refactoring
