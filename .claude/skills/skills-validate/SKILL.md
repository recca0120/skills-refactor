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

Verify all references created during extraction:

```
For each SKILL.md:
  - [ ] All `shared/*.md` references exist
  - [ ] All markdown links work
  - [ ] YAML frontmatter intact
```

### Step 2: Content Check

Compare before/after to ensure no accidental changes:

| Check | Pass Criteria |
|-------|---------------|
| Triggers | Same phrases still activate skill |
| Actions | Same behavior described |
| Output | Same format expected |

### Step 3: Generate Test Prompts

For each refactored skill, create test prompts from description:

```markdown
## Test: [skill-name]

| Prompt | Expected |
|--------|----------|
| "[trigger from description]" | Skill activates |
| "[alternative phrasing]" | Same behavior |
```

## Output

```markdown
# Validation Report

## Reference Check
- [x] skill-a: All references valid
- [ ] skill-b: Missing `shared/example.md`

## Test Prompts
### skill-a
- "process this document" → Should activate

## Issues Found
- [List any problems]
```

## Next Step

If issues found → Fix and re-run validation
If all pass → Refactoring complete
