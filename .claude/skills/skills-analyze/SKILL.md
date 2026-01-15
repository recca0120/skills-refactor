---
name: analyzing-skills
description: "Analyzes Claude Code Skills for lint issues and duplicate content. Use when user asks to analyze skills, lint skills, check quality, or find duplicates."
---

# Skills Analyzer

## When to Use

This is **Step 1** of the refactoring workflow:

1. **analyze** ← You are here
2. extract → Run after analysis identifies issues
3. validate → Run after extraction completes

## Workflow

### Step 1: Scan Skills

Locate all `SKILL.md` files in the target directory:
- Project: `.claude/skills/`
- Personal: `~/.claude/skills/`

### Step 2: Check Issues

For each SKILL.md, check:

| Category | Rules | Severity |
|----------|-------|----------|
| frontmatter | name, description format | Error |
| content | max 500 lines, no `\` paths | Warning |
| naming | prefer verb-ing, no vague names | Warning/Info |
| reference | all paths must exist | Error |
| security | no secrets/API keys | Error |

> Rule details: `references/lint-rules.md`

### Step 3: Detect Duplicates

Compare content across all SKILL.md files:

| Type | Criteria | Action |
|------|----------|--------|
| Exact | 100% identical, ≥5 lines | Extract to shared/ |
| Near | ≥80% similar | Parameterize or extract |
| Structural | Same pattern, different values | Create template |

> Detection patterns: `references/duplicate-patterns.md`

## Output

```markdown
# Analysis Report

## Summary
- Skills: [count] | Errors: [n] | Warnings: [n] | Duplicates: [n]

## Issues
- `[rule]` skill-name/SKILL.md:[line] - [message]

## Duplicates
- [type]: [locations] → Extract to shared/[name].md
```

## Next Step

If issues found → Run **skills-extract** to refactor
