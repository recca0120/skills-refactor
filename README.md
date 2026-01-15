# Skills Refactor

[![GitHub](https://img.shields.io/github/license/recca0120/skills-refactor)](LICENSE)

A set of Claude Code Skills for analyzing, refactoring, and validating Claude Code Skills. Helps maintain clean, DRY (Don't Repeat Yourself) skill definitions.

[中文版 README](README.zh-TW.md)

## Features

- **Lint Checking**: Validate skills against official best practices
- **Duplicate Detection**: Find exact, near, and structural duplicates
- **Refactoring**: Extract common content into shared modules
- **Validation**: Verify refactored skills still work correctly

## Installation

Copy the skills to your project or personal skills directory:

```bash
# Project-level (copy all three skills)
cp -r .claude/skills/skills-* /path/to/your/project/.claude/skills/

# Personal (global)
cp -r .claude/skills/skills-* ~/.claude/skills/
```

Or copy individually:

```bash
cp -r .claude/skills/skills-analyze ~/.claude/skills/
cp -r .claude/skills/skills-extract ~/.claude/skills/
cp -r .claude/skills/skills-validate ~/.claude/skills/
```

## Usage

### Analyze Skills

Scan skills for lint issues and duplicates:

```
User: "Analyze the skills in .claude/skills/"
User: "Lint check my skills"
User: "Find duplicates in skills directory"
```

**Output**: A structured report with lint errors/warnings and duplicate content locations.

### Extract Common Content

Refactor duplicates into shared modules:

```
User: "Extract the workflow diagram to shared/"
User: "Refactor skills - merge duplicate content"
User: "Create shared reference for common resources"
```

**Output**: Creates `shared/` directory with extracted content and updates original skills with references.

### Validate Skills

Verify skills work after refactoring:

```
User: "Validate the refactored skills"
User: "Generate test cases for skills"
User: "Check if skills still work"
```

**Output**: Validation report with test cases for manual verification.

## Lint Rules

Based on [Anthropic's official Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices).

### Frontmatter Rules

| Rule | Description | Severity |
|------|-------------|----------|
| `frontmatter/required` | Must have YAML frontmatter | Error |
| `frontmatter/starts-line-one` | Frontmatter must start on line 1 | Error |
| `frontmatter/name-required` | Must have `name` field | Error |
| `frontmatter/name-max-length` | `name` <= 64 characters | Error |
| `frontmatter/name-format` | `name` uses lowercase, numbers, hyphens only | Error |
| `frontmatter/name-no-reserved` | No "anthropic" or "claude" in name | Error |
| `frontmatter/name-no-xml` | No XML tags in `name` | Error |
| `frontmatter/description-required` | Must have `description` field | Error |
| `frontmatter/description-max-length` | `description` <= 1024 characters | Error |
| `frontmatter/description-no-first-person` | Use third person in description | Warning |
| `frontmatter/description-no-xml` | No XML tags in `description` | Error |

### Content Rules

| Rule | Description | Severity |
|------|-------------|----------|
| `content/max-lines` | SKILL.md <= 500 lines | Warning |
| `content/no-windows-paths` | Use forward slashes `/` | Warning |
| `content/spaces-not-tabs` | Use spaces for indentation, not tabs | Warning |

### Naming Rules

| Rule | Description | Severity |
|------|-------------|----------|
| `naming/prefer-gerund` | Use verb-ing form (e.g., `processing-pdfs`) | Info |
| `naming/no-vague` | Avoid `helper`, `utils`, `tools` | Warning |
| `naming/no-generic` | Avoid `documents`, `data`, `files` | Warning |

### Reference Rules

| Rule | Description | Severity |
|------|-------------|----------|
| `reference/valid-paths` | All referenced files must exist | Error |
| `reference/max-depth` | Keep references one level deep | Warning |

### Security Rules

| Rule | Description | Severity |
|------|-------------|----------|
| `security/no-secrets` | No API keys, passwords, tokens | Error |

## Project Structure

```
skills-refactor/
├── .claude/skills/                 # Copy these to your project
│   ├── skills-analyze/             # Analyze + Lint skill
│   │   ├── SKILL.md
│   │   └── references/
│   │       ├── lint-rules.md
│   │       └── duplicate-patterns.md
│   │
│   ├── skills-extract/             # Refactoring skill
│   │   ├── SKILL.md
│   │   └── references/
│   │       └── example.md
│   │
│   └── skills-validate/            # Validation skill
│       ├── SKILL.md
│       └── references/
│           └── checklist.md
│
├── examples/                       # Test examples (don't copy)
│   └── sample-skills/
│       ├── skill-a/                # Correct example (shows exact/near duplicates)
│       ├── skill-b/                # Correct example (shows exact/near duplicates)
│       ├── Skill_C/                # Violations: name-format, secrets, windows-paths
│       ├── Skill_D/                # Violations: starts-line-one, name-no-xml, description-no-xml
│       ├── Skill_E/                # Violations: spaces-not-tabs
│       └── helper/                 # Violations: name-no-reserved, naming/no-vague
│
└── docs/
    └── claude-code-skills-guide.md
```

## Example Report

```markdown
# Skills Analysis Report

## Summary
- Directory: .claude/skills/sample-skills/
- Skills scanned: 3
- Lint issues: 5 (2 Error, 2 Warning, 1 Info)
- Duplicates found: 2

## Lint Results

### Errors (2)
- [frontmatter/name-format] Skill_C/SKILL.md
  name contains underscore and uppercase: Helper_Tool → helper-tool

- [security/no-secrets] Skill_C/SKILL.md:25
  Contains API key pattern: api_key = sk-...

### Warnings (2)
- [frontmatter/description-no-first-person] Skill_C/SKILL.md
  Uses first person: "I can help you..."

- [content/no-windows-paths] Skill_C/SKILL.md:20
  Windows-style path: docs\missing.md → docs/missing.md

## Duplicate Content

### Duplicate #1: Workflow Diagram
- Type: near (90% similar)
- Locations:
  - skill-a/SKILL.md:10-18
  - skill-b/SKILL.md:10-18
  - Skill_C/SKILL.md:10-18
- Suggestion: Extract to shared/workflow-diagram.md
```

## Documentation

See [docs/claude-code-skills-guide.md](docs/claude-code-skills-guide.md) for a comprehensive guide on Claude Code Skills, including:

- Skills structure and configuration
- Best practices from official documentation
- Advanced features
- Examples

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

MIT License - see [LICENSE](LICENSE) for details.

## References

- [Agent Skills - Claude Code Docs](https://code.claude.com/docs/en/skills)
- [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [How to Create Custom Skills](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills)
