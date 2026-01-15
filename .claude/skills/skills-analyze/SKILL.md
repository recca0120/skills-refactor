---
name: analyzing-skills
description: "Analyzes Claude Code Skills directories for lint issues and duplicate content. Use when user asks to analyze skills, lint skills, check skills quality, find duplicates, or review skills structure."
---

# Skills Analyzer

Analyzes Claude Code Skills for quality issues (lint) and duplicate content.

## Trigger Conditions

Activate when user requests:
- "analyze skills", "lint skills", "check skills"
- "find duplicates in skills", "check skills quality"
- "review skills structure"

## Workflow

### Step 1: Identify Target Directory

Ask user for the skills directory path if not specified. Common locations:
- `.claude/skills/` (project)
- `~/.claude/skills/` (personal)

### Step 2: Scan All Skills

For each subdirectory containing `SKILL.md`:
1. Read and parse the SKILL.md file
2. Extract YAML frontmatter
3. Analyze content structure

### Step 3: Run Lint Checks

Execute lint rules in this order:

#### 3.1 Frontmatter Rules (Critical)

Check each SKILL.md for:

| Rule | Check | Severity |
|------|-------|----------|
| `frontmatter/required` | Has YAML frontmatter (starts with `---`) | Error |
| `frontmatter/name-required` | Has `name` field | Error |
| `frontmatter/name-max-length` | `name` <= 64 characters | Error |
| `frontmatter/name-format` | `name` uses only lowercase, numbers, hyphens | Error |
| `frontmatter/name-no-reserved` | `name` doesn't contain "anthropic" or "claude" | Error |
| `frontmatter/description-required` | Has `description` field | Error |
| `frontmatter/description-max-length` | `description` <= 1024 characters | Error |
| `frontmatter/description-no-first-person` | No "I can", "I will", "You can" in description | Warning |

#### 3.2 Content Rules

| Rule | Check | Severity |
|------|-------|----------|
| `content/max-lines` | SKILL.md body <= 500 lines | Warning |
| `content/no-windows-paths` | No backslash paths (use `/` not `\`) | Warning |

#### 3.3 Naming Rules

| Rule | Check | Severity |
|------|-------|----------|
| `naming/prefer-gerund` | Directory name uses verb-ing form | Info |
| `naming/no-vague` | Not named helper, utils, tools, etc. | Warning |
| `naming/no-generic` | Not named documents, data, files, etc. | Warning |

#### 3.4 Reference Rules

| Rule | Check | Severity |
|------|-------|----------|
| `reference/valid-paths` | All referenced files exist | Error |
| `reference/max-depth` | References are max 1 level deep | Warning |

#### 3.5 Security Rules

| Rule | Check | Severity |
|------|-------|----------|
| `security/no-secrets` | No API keys, passwords, tokens | Error |

> For detailed rule explanations, see `references/lint-rules.md`

### Step 4: Detect Duplicates

Compare content across all SKILL.md files:

1. **Exact duplicates**: Identical text blocks (>= 5 lines)
2. **Near duplicates**: Similar blocks (>= 80% similarity)
3. **Structural duplicates**: Same patterns with different values

> For duplicate detection patterns, see `references/duplicate-patterns.md`

### Step 5: Generate Report

Output a structured markdown report:

```markdown
# Skills Analysis Report

## Summary
- Directory: [path]
- Skills scanned: [count]
- Lint issues: [count] ([errors] Error, [warnings] Warning, [info] Info)
- Duplicates found: [count]

## Lint Results

### Errors ([count])
- [rule-id] path/SKILL.md:line
  Description of the issue

### Warnings ([count])
...

### Info ([count])
...

## Duplicate Content

### Duplicate #1: [name]
- Type: [exact/near/structural]
- Locations:
  - skill-a/SKILL.md:10-25
  - skill-b/SKILL.md:15-30
- Suggestion: Extract to shared/[filename].md

...
```

## Important Notes

- Always report the full path to each issue
- Include line numbers when possible
- Provide actionable suggestions for each issue
- Group related duplicates together
- Prioritize Errors > Warnings > Info in the report
