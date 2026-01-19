# Validation Checklist

Quick reference for validating refactored skills.

---

## Reference Check

- [ ] All `shared/*.md` files exist
- [ ] All markdown links resolve
- [ ] YAML frontmatter valid

## Content Check

| Aspect | Before | After | Match? |
|--------|--------|-------|--------|
| Triggers | [list] | [list] | [ ] |
| Actions | [list] | [list] | [ ] |
| Output | [format] | [format] | [ ] |

## Test Prompt Template

```markdown
## Test: [skill-name]

**Prompt**: "[trigger phrase]"
**Expected**: Skill activates, performs [action]
**Actual**: [result]
**Status**: PASS / FAIL
```

## Common Issues

| Issue | Fix |
|-------|-----|
| Missing shared file | Create the file or update reference |
| Broken YAML | Check `---` delimiters and syntax |
| Wrong behavior | Compare with original, restore if needed |
