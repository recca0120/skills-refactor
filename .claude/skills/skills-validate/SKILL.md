---
name: validating-skills
description: "Validates Skills after refactoring to ensure they work correctly. Use when user asks to validate skills, verify refactoring, or check skills still work."
---

# Skills Validator

## When to Use

This is **Step 3** of the refactoring workflow:

1. analyze → Identifies issues
2. extract → Performs refactoring
3. **validate** ← You are here

**Prerequisite**: Refactoring completed by skills-extract.

## Workflow

### Step 1: Reference Check

Verify all references exist and YAML frontmatter is intact.

> Checklist: `references/checklist.md`

### Step 2: Content Check

Compare before/after to ensure triggers, actions, and output format unchanged.

### Step 3: Generate Test Prompts

Create test prompts from each skill's description to verify activation.

## Output

```markdown
# Validation Report

## Reference Check
- [x] skill-a: All references valid
- [ ] skill-b: Missing `shared/example.md`

## Test Prompts
- "[trigger phrase]" → Should activate [skill-name]

## Issues Found
- [List any problems]
```

## Next Step

If issues found → Fix and re-run validation
If all pass → Refactoring complete
