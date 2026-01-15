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
- **Cross-project**: Multiple directories (if specified)

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

### Step 4: Calculate Quality Score

Calculate a 0-100 score based on rule compliance:

| Factor | Weight | Calculation |
|--------|--------|-------------|
| Error-free | 40% | -10 per Error |
| Warning-free | 30% | -5 per Warning |
| Best practices | 20% | +points for gerund naming, good structure |
| Documentation | 10% | +points for clear descriptions |

### Step 5: Cross-Project Analysis (Optional)

When analyzing multiple projects/directories:

1. **Find shared patterns** across projects
2. **Identify candidates** for organization-level shared skills
3. **Recommend consolidation** opportunities

| Pattern | Recommendation |
|---------|----------------|
| Same skill in 3+ projects | Create org-level shared skill |
| Similar workflows | Extract to shared template |
| Common references | Create shared resource library |

## Output

```markdown
# Analysis Report

## Summary
- Skills: [count] | Score: [0-100] | Errors: [n] | Warnings: [n]

## Quality Score: [score]/100
- Errors: -[n] points
- Warnings: -[n] points
- Best practices: +[n] points

## Issues with Auto-fix

### `[rule]` skill-name/SKILL.md:[line]
**Problem**: [description]
**Fix**:
\`\`\`yaml
# Before
[original code]

# After
[fixed code]
\`\`\`

## Duplicates
- [type]: [locations] → Extract to shared/[name].md

## Cross-Project (if multiple directories)
### Consolidation Opportunities
- [skill-name]: Found in [n] projects → Move to org-level
- [pattern]: Shared across [n] skills → Create template
```

> Rule details and fix patterns: `references/lint-rules.md`

## Next Step

If issues found → Run **skills-extract** to refactor
