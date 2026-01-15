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

### Step 3: Auto-Generate Refactoring Plan

Generate a dry-run preview before making changes:

```markdown
## Refactoring Plan (Dry-Run)

### Action 1: Create shared/[auto-generated-name].md
\`\`\`markdown
[content to be extracted]
\`\`\`

### Action 2: Update [skill-a]/SKILL.md
\`\`\`diff
- [original lines 10-20]
+ > See `shared/[name].md` for [description]
\`\`\`

### Action 3: Update [skill-b]/SKILL.md
\`\`\`diff
- [original lines 15-25]
+ > See `shared/[name].md` for [description]
\`\`\`
```

> Naming guidelines: `references/example.md`

### Step 4: Execute with Confirmation

After user confirms the plan:

1. **Create** the shared file with extracted content
2. **Update** each SKILL.md with reference
3. **Verify** all references are valid

### Step 5: Verify

- [ ] All references point to existing files
- [ ] YAML frontmatter intact in all SKILL.md
- [ ] No content accidentally removed

## Example

> See `references/example.md` for a complete before/after refactoring example.

## Output

```markdown
# Extraction Complete

## Dry-Run Preview
[Show plan before execution]

## Changes Made
- Created: shared/[name].md ([n] lines)
- Modified: [skill-a]/SKILL.md (-[n] lines)
- Modified: [skill-b]/SKILL.md (-[n] lines)

## Summary
- Duplicates eliminated: [n] locations → 1 source
- Lines saved: [total lines reduced]
```

## Next Step

Run **skills-validate** to verify refactoring
