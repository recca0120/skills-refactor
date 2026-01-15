---
name: validating-skills
description: "Validates Claude Code Skills after refactoring to ensure they still work correctly. Use when user asks to validate skills, verify refactoring, check skills work, or generate test cases."
---

# Skills Validator

Validates Skills after refactoring to ensure correctness and generates test cases for manual verification.

## Trigger Conditions

Activate when user requests:
- "validate skills", "verify refactoring"
- "check skills work", "test skills"
- "generate test cases for skills"

## Workflow

### Step 1: Static Validation

Check all Skills for structural correctness:

#### 1.1 Frontmatter Validation
- [ ] YAML frontmatter exists and is valid
- [ ] `name` field present and valid format
- [ ] `description` field present and non-empty

#### 1.2 Reference Validation
- [ ] All referenced files exist
- [ ] No broken links (markdown links, code references)
- [ ] Shared files are accessible

#### 1.3 Structure Validation
- [ ] SKILL.md is valid markdown
- [ ] Headers are properly nested
- [ ] Code blocks are properly closed

### Step 2: Consistency Check

Compare refactored skills with pre-refactor state (if available):

- [ ] Same triggers should still work
- [ ] No content accidentally removed
- [ ] References correctly point to shared content

### Step 3: Generate Test Cases

For each skill, generate test prompts based on:
1. **Description keywords**: Extract trigger phrases
2. **Content analysis**: Identify expected behaviors
3. **Edge cases**: Consider variations

> See `references/checklist.md` for test case templates

### Step 4: Output Validation Report

```markdown
# Skills Validation Report

## Static Checks

### Passed ([count])
- [x] skill-a/SKILL.md - All checks passed
- [x] skill-b/SKILL.md - All checks passed

### Failed ([count])
- [ ] skill-c/SKILL.md
  - Missing reference: `shared/missing.md`
  - Invalid YAML frontmatter

## Reference Integrity

### Shared Files
| File | Referenced By | Status |
|------|---------------|--------|
| shared/workflow.md | skill-a, skill-b | OK |
| shared/reference.md | skill-a, skill-c | OK |
| shared/missing.md | skill-c | NOT FOUND |

## Manual Test Cases

### skill-a: [name]

| # | Test Prompt | Expected Behavior |
|---|-------------|-------------------|
| 1 | "[trigger phrase 1]" | Skill activates, performs [action] |
| 2 | "[trigger phrase 2]" | Skill activates, outputs [format] |
| 3 | "[edge case]" | Skill handles gracefully |

### skill-b: [name]
...
```

---

## Test Case Generation

### From Description

Extract trigger phrases from the `description` field:

```yaml
description: "Processes Excel files and generates reports. Use when working with spreadsheets or .xlsx files."
```

**Generated test cases**:
1. "Process this Excel file"
2. "Generate a report from spreadsheet"
3. "Work with this .xlsx file"

### From Content

Analyze SKILL.md content for:
- Step descriptions → Test each step
- Output formats → Verify format compliance
- Error handling → Test error conditions

### Edge Cases

Always include:
- Minimal input
- Missing input
- Invalid input
- Boundary conditions

---

## Validation Checklist

### Pre-Refactor vs Post-Refactor

| Aspect | Check |
|--------|-------|
| **Trigger** | Same prompts activate the skill |
| **Behavior** | Same actions are performed |
| **Output** | Same format is produced |
| **References** | All referenced content is accessible |

### Regression Checks

- [ ] No functionality removed
- [ ] No new errors introduced
- [ ] Performance not degraded (response time similar)
- [ ] All use cases still covered

---

## Manual Testing Guide

After validation report is generated:

1. **Review test cases**: Ensure they cover all scenarios
2. **Execute tests**: Run each prompt in Claude Code
3. **Compare results**: Check against expected behavior
4. **Document issues**: Note any discrepancies
5. **Iterate**: Fix issues and re-validate

### Test Execution Format

```markdown
## Test Execution Log

### Test 1: [skill-name] - [test description]
- **Prompt**: "[test prompt]"
- **Expected**: [expected behavior]
- **Actual**: [what actually happened]
- **Status**: PASS / FAIL
- **Notes**: [any observations]
```

---

## Important Notes

- Run validation after every refactoring session
- Keep test cases updated as skills evolve
- Document any intentional behavior changes
- Use consistent test prompts for reproducibility
