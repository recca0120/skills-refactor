# Validation Checklist

Complete checklist for validating Skills after refactoring.

---

## Static Validation Checklist

### Frontmatter

- [ ] File starts with `---`
- [ ] `name` field exists
- [ ] `name` is lowercase with hyphens only
- [ ] `name` is <= 64 characters
- [ ] `description` field exists
- [ ] `description` is non-empty
- [ ] `description` is <= 1024 characters
- [ ] YAML is valid (no syntax errors)
- [ ] Closing `---` exists

### Content Structure

- [ ] Valid markdown syntax
- [ ] Headers properly nested (no skipped levels)
- [ ] All code blocks properly closed
- [ ] All lists properly formatted
- [ ] All tables have matching columns

### References

- [ ] All file paths exist
- [ ] All markdown links work
- [ ] All code references point to valid files
- [ ] Shared directory exists (if referenced)

---

## Test Case Templates

### Template 1: Basic Trigger Test

```markdown
## Test: Basic Trigger

**Skill**: [skill-name]
**Prompt**: "[primary trigger phrase from description]"

**Expected**:
- Skill activates
- [Expected first action]
- [Expected output format]

**Status**: [ ] PASS  [ ] FAIL
```

### Template 2: Alternative Trigger Test

```markdown
## Test: Alternative Trigger

**Skill**: [skill-name]
**Prompt**: "[alternative phrasing]"

**Expected**:
- Same behavior as basic trigger
- Skill recognizes alternative phrasing

**Status**: [ ] PASS  [ ] FAIL
```

### Template 3: Edge Case Test

```markdown
## Test: Edge Case - [description]

**Skill**: [skill-name]
**Prompt**: "[edge case prompt]"

**Expected**:
- Skill handles gracefully
- [Specific expected behavior]
- No errors or crashes

**Status**: [ ] PASS  [ ] FAIL
```

### Template 4: Reference Integrity Test

```markdown
## Test: Reference Integrity

**Skill**: [skill-name]
**Check**: Verify all references work

**Steps**:
1. List all referenced files in SKILL.md
2. Verify each file exists
3. Verify content is accessible

**References**:
- [ ] `shared/file1.md` - EXISTS / MISSING
- [ ] `shared/file2.md` - EXISTS / MISSING
- [ ] `references/doc.md` - EXISTS / MISSING

**Status**: [ ] PASS  [ ] FAIL
```

---

## Regression Test Checklist

### Before/After Comparison

| Aspect | Before Refactor | After Refactor | Match? |
|--------|-----------------|----------------|--------|
| Trigger phrases | [list] | [list] | [ ] |
| Main actions | [list] | [list] | [ ] |
| Output format | [describe] | [describe] | [ ] |
| Referenced files | [list] | [list] | [ ] |

### Functionality Preservation

- [ ] All original trigger phrases still work
- [ ] All original actions still performed
- [ ] Output format unchanged (or intentionally changed)
- [ ] No new errors introduced
- [ ] No content accidentally removed

---

## Test Execution Log Template

```markdown
# Test Execution Log

**Date**: YYYY-MM-DD
**Tester**: [name]
**Skills Tested**: [list]

## Summary

| Skill | Tests | Passed | Failed |
|-------|-------|--------|--------|
| skill-a | 4 | 4 | 0 |
| skill-b | 3 | 2 | 1 |
| skill-c | 5 | 5 | 0 |

## Detailed Results

### skill-a

#### Test 1: Basic Trigger
- **Prompt**: "..."
- **Expected**: ...
- **Actual**: ...
- **Status**: PASS

#### Test 2: ...

### skill-b

#### Test 1: Basic Trigger
- **Prompt**: "..."
- **Expected**: ...
- **Actual**: ...
- **Status**: FAIL
- **Issue**: [describe the issue]
- **Fix Required**: [yes/no]

## Issues Found

1. **skill-b Test 1**: [issue description]
   - Severity: [high/medium/low]
   - Suggested fix: [description]

## Recommendations

- [Any recommendations based on testing]
```

---

## Quick Validation Commands

### Check File Exists
```bash
ls -la .claude/skills/[skill-name]/SKILL.md
```

### Check Shared References
```bash
ls -la .claude/skills/shared/
```

### Validate YAML Frontmatter
```bash
head -20 .claude/skills/[skill-name]/SKILL.md
```

### Count Lines
```bash
wc -l .claude/skills/[skill-name]/SKILL.md
```

### Find Broken References
```bash
grep -E '\[.*\]\(.*\)' .claude/skills/[skill-name]/SKILL.md
```
