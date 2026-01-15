# Claude Code Skills Complete Guide

[中文版](claude-code-skills-guide.md)

## Table of Contents

1. [Overview](#overview)
2. [Skills vs Slash Commands vs CLAUDE.md](#skills-vs-slash-commands-vs-claudemd)
3. [Skills Structure](#skills-structure)
4. [SKILL.md File Format](#skillmd-file-format)
5. [Best Practices](#best-practices)
6. [Advanced Features](#advanced-features)
7. [Development Workflow](#development-workflow)
8. [Anti-patterns](#anti-patterns)
9. [Checklist](#checklist)
10. [References](#references)

---

## Overview

Claude Code Skills are extensible capability packages that inject task-specific instructions into Claude. Skills are specialized prompt templates that enhance Claude's abilities through context injection rather than traditional code execution.

### Core Features

- **Automatic Triggering**: Claude automatically determines and invokes the appropriate Skill based on user requests
- **On-demand Loading**: Skills are only loaded when needed, keeping the base prompt lean
- **Modular Design**: Each Skill focuses on a specific task and can be composed together
- **Cross-platform Standard**: Anthropic has released the Agent Skills specification as an open standard

---

## Skills vs Slash Commands vs CLAUDE.md

| Feature | Skills | Slash Commands | CLAUDE.md |
|---------|--------|----------------|-----------|
| **Trigger Method** | Model auto-triggers | User manually types `/command` | Auto-loaded |
| **Location** | `.claude/skills/` | `.claude/commands/` | Project root |
| **Structure** | Directory + SKILL.md + resources | Single markdown file | Single markdown file |
| **Use Case** | Complex, multi-file workflows | Repeatable explicit actions | Project-wide rules and conventions |
| **Context Impact** | On-demand loading | On-demand loading | Permanently loaded |

### When to Use What

- **CLAUDE.md**: Short, always-applicable project conventions and standards
- **Slash Commands**: Need explicit, repeatable terminal entry points
- **Skills**: Need Claude to automatically apply richer workflows (usually with supporting files)

---

## Skills Structure

### Directory Locations

- **Personal Skills**: `~/.claude/skills/`
- **Project Skills**: `.claude/skills/` (recommended to add to version control)

### Basic Structure

```
.claude/skills/
└── my-skill/
    ├── SKILL.md          # Required - core instruction file
    ├── scripts/          # Optional - executable scripts
    │   └── helper.py
    ├── references/       # Optional - reference documents
    │   └── guide.md
    └── assets/           # Optional - templates and static resources
        └── template.json
```

### File Descriptions

| File/Directory | Purpose |
|----------------|---------|
| `SKILL.md` | Core prompt file containing YAML frontmatter and instructions |
| `scripts/` | Python, Bash, and other executable automation scripts |
| `references/` | Additional reference documents Claude reads when needed |
| `assets/` | Templates and static resources |

---

## SKILL.md File Format

### Basic Format

```yaml
---
name: my-skill-name
description: "Describes what this skill does. Use when user asks to do X or Y."
---

# Skill Title

## Instructions

Instructions for Claude to follow...
```

### Frontmatter Fields

| Field | Required | Limit | Description |
|-------|----------|-------|-------------|
| `name` | Yes | Max 64 chars | Only lowercase letters, numbers, and hyphens |
| `description` | Yes | Max 1024 chars | Describes what it does and when to use; must use third person |
| `allowed-tools` | No | - | List of allowed tools |

### Name Format Rules

- ✅ Can only contain: lowercase letters (a-z), numbers (0-9), hyphens (-)
- ❌ Cannot contain: uppercase letters, underscores, spaces, special characters
- ❌ Cannot contain reserved words: `anthropic`, `claude`

```yaml
# Correct
name: processing-pdfs
name: code-review-2

# Incorrect
name: Processing_PDFs    # uppercase, underscore
name: my skill           # space
name: claude-helper      # reserved word
```

### Description Rules

**Always use third person**. The description is injected into the system prompt; inconsistent person usage makes it difficult for Claude to correctly identify and trigger the Skill.

```yaml
# Correct
description: "Processes Excel files and generates reports. Use when working with spreadsheets."

# Incorrect
description: "I can help you process Excel files"
description: "You can use this to process Excel files"
```

---

## Best Practices

> The following content is compiled from [Anthropic's official Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### 1. Concise is Key

The context window is a shared resource. **Default assumption**: Claude is already very smart; only add what Claude doesn't already know.

```markdown
✅ Good (about 50 tokens):
## Extract PDF text
Use pdfplumber for text extraction:
```python
import pdfplumber
with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```

❌ Bad (about 150 tokens):
## Extract PDF text
PDF (Portable Document Format) files are a common file format...
(Too much explanation of things Claude already knows)
```

### 2. Set Appropriate Freedom Levels

| Freedom | When to Use | Example |
|---------|-------------|---------|
| **High** | Multiple methods work, decision depends on context | Code review process |
| **Medium** | Have preferred patterns but allow variation | Scripts with parameters |
| **Low** | Operations are fragile, consistency is critical | Database migrations |

### 3. Single Responsibility

Each Skill should focus on a single workflow.

```
✅ Good approach:
- code-reviewer/
- test-generator/
- security-auditor/

❌ Bad approach:
- all-in-one-dev-helper/
```

### 4. Naming Conventions

Use **gerund form** (verb + -ing) for naming.

```yaml
✅ Good naming:
- processing-pdfs
- analyzing-spreadsheets
- testing-code

✅ Acceptable alternatives:
- pdf-processing (noun phrase)
- process-pdfs (action-oriented)

❌ Avoid:
- helper, utils, tools (vague)
- documents, data, files (too generic)
```

### 5. Progressive Disclosure

This is **the most important concept** for building Skills. Only show enough information to help Claude decide the next step.

```
my-skill/
├── SKILL.md           # Core instructions (under 500 lines)
└── references/
    ├── detailed-api.md    # Detailed API documentation
    └── examples.md        # Example code
```

**Keep references one level deep**:

```markdown
❌ Too deep:
SKILL.md → advanced.md → details.md → actual info

✅ One level deep:
SKILL.md → advanced.md
SKILL.md → reference.md
```

### 6. Control SKILL.md Length

Keep SKILL.md **under 500 lines**. If longer, split into separate files.

### 7. Use Workflows and Checklists

Use checklists to track progress for complex tasks:

```markdown
## Workflow

Copy this checklist and track progress:

```
Task Progress:
- [ ] Step 1: Analyze input
- [ ] Step 2: Process data
- [ ] Step 3: Validate output
- [ ] Step 4: Generate report
```

**Step 1: Analyze input**
...
```

### 8. Implement Feedback Loops

Validate → Fix → Repeat

```markdown
## Validation workflow

1. Make changes
2. Run validation: `python scripts/validate.py`
3. If validation fails:
   - Review error message
   - Fix the issue
   - Run validation again
4. Only proceed when validation passes
```

---

## Advanced Features

### Integrating Executable Scripts

Skills can include Python, Bash, and other scripts.

```
my-skill/
├── SKILL.md
└── scripts/
    ├── analyze.py
    └── validate.py
```

Explain usage in SKILL.md:

```markdown
## Utility scripts

**analyze.py**: Extract data from input
```bash
python scripts/analyze.py input.pdf
```

**validate.py**: Verify output format
```bash
python scripts/validate.py output.json
```
```

### MCP Tool References

When using MCP tools, use the full name:

```markdown
Use the BigQuery:bigquery_schema tool to retrieve schemas.
Use the GitHub:create_issue tool to create issues.
```

Format: `ServerName:tool_name`

---

## Development Workflow

### Evaluation-Driven Development

**Build evaluations first, then write documentation.**

1. **Identify gaps**: Execute the task without a Skill, record failure points
2. **Build evaluations**: Create 3 test scenarios
3. **Establish baseline**: Measure performance without Skill
4. **Write minimal instructions**: Only write enough to solve the problem
5. **Iterate**: Run evaluations, compare to baseline, improve

### Iterative Development with Claude

1. **Complete the task**: First complete a task with Claude A
2. **Identify patterns**: Find reusable knowledge
3. **Build the Skill**: Ask Claude A to create the Skill
4. **Review for conciseness**: Remove unnecessary explanations
5. **Test**: Test with Claude B (new instance)
6. **Iterate**: Improve based on observations

### Observing How Claude Uses Skills

Watch for:
- Does Claude read files in the expected order?
- Does Claude miss important references?
- Does Claude repeatedly read the same file? (Consider moving to main SKILL.md)
- Does Claude never access certain files? (May not be needed)

---

## Anti-patterns

### Avoid Windows Paths

```
✅ Good: scripts/helper.py
❌ Avoid: scripts\helper.py
```

### Avoid Providing Too Many Options

```markdown
❌ Bad:
"You can use pypdf, or pdfplumber, or PyMuPDF, or..."

✅ Good:
"Use pdfplumber for text extraction.
For scanned PDFs requiring OCR, use pdf2image instead."
```

### Avoid Time-sensitive Information

```markdown
❌ Bad:
If you're doing this before August 2025, use the old API.

✅ Good:
## Current method
Use the v2 API endpoint

## Old patterns
<details>
<summary>Legacy v1 API (deprecated)</summary>
...
</details>
```

### Avoid Assuming Tools Are Installed

```markdown
❌ Bad:
"Use the pdf library to process the file."

✅ Good:
"Install required package: `pip install pypdf`
Then use it:
```python
from pypdf import PdfReader
```"
```

---

## Checklist

### Core Quality

- [ ] Description is specific and contains keywords
- [ ] Description includes what it does and when to use
- [ ] Description uses third person
- [ ] SKILL.md content is under 500 lines
- [ ] Additional details are in separate files
- [ ] No time-sensitive information
- [ ] Consistent terminology
- [ ] Examples are concrete, not abstract
- [ ] File references are only one level deep
- [ ] Appropriate use of progressive disclosure
- [ ] Workflows have clear steps

### Code and Scripts

- [ ] Scripts handle errors rather than pushing to Claude
- [ ] Error handling is clear and helpful
- [ ] No "magic numbers" (all values are explained)
- [ ] Required packages are listed
- [ ] No Windows paths
- [ ] Critical operations have validation steps
- [ ] Quality-critical tasks have feedback loops

### Testing

- [ ] Created at least 3 evaluation scenarios
- [ ] Tested with Haiku, Sonnet, Opus
- [ ] Tested with real scenarios
- [ ] Incorporated team feedback

---

## References

### Official Documentation

- [Agent Skills Overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
- [Skill Authoring Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [Claude Code Skills Docs](https://code.claude.com/docs/en/skills)

### Official Resources

- [Anthropic Skills Repository](https://github.com/anthropics/skills)

### Deep Dives

- [Inside Claude Code Skills: Structure, Prompts, Invocation](https://mikhail.io/2025/10/claude-code-skills/)
- [Claude Agent Skills: A First Principles Deep Dive](https://leehanchung.github.io/blogs/2025/10/26/claude-skills-deep-dive/)

### Tools and Resources

- [Claude Code Skill Factory](https://github.com/alirezarezvani/claude-code-skill-factory)
- [Skills Marketplace](https://skillsmp.com/)
