# Lint Rules Reference

Detailed explanations for each lint rule, based on [Anthropic's official Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices).

---

## Frontmatter Rules

### frontmatter/required
**Severity**: Error

Every SKILL.md must start with YAML frontmatter enclosed in `---` delimiters.

```yaml
# Before (missing frontmatter)
# My Skill
Instructions here...

# After
---
name: my-skill
description: "Describes what this skill does. Use when..."
---

# My Skill
Instructions here...
```

### frontmatter/starts-line-one
**Severity**: Error

The frontmatter must start on line 1. No blank lines or content before the opening `---`.

```yaml
# Before (blank line at start)

---
name: my-skill
...

# After
---
name: my-skill
...
```

**Why**: Claude Code expects the frontmatter to begin immediately at line 1 for proper parsing.

### frontmatter/name-required
**Severity**: Error

The `name` field is required in frontmatter.

### frontmatter/name-max-length
**Severity**: Error

The `name` field must be at most 64 characters.

```yaml
# Before
name: this-is-a-very-long-skill-name-that-exceeds-the-maximum-allowed-length

# After
name: long-skill-name
```

### frontmatter/name-format
**Severity**: Error

The `name` field must contain only lowercase letters (a-z), numbers (0-9), and hyphens (-).

| Before | After |
|--------|-------|
| `Helper_Tool` | `helper-tool` |
| `My Skill` | `my-skill` |
| `ProcessPDFs` | `process-pdfs` |
| `my.skill` | `my-skill` |

### frontmatter/name-no-reserved
**Severity**: Error

The `name` field cannot contain reserved words: `anthropic`, `claude`.

| Before | After |
|--------|-------|
| `claude-helper` | `code-helper` |
| `anthropic-tools` | `ai-tools` |

### frontmatter/name-no-xml
**Severity**: Error

The `name` field cannot contain XML tags.

```yaml
# Before
name: data<processor>

# After
name: data-processor
```

**Why**: XML tags in the name field can interfere with Claude's system prompt injection.

### frontmatter/description-required
**Severity**: Error

The `description` field is required in frontmatter.

### frontmatter/description-max-length
**Severity**: Error

The `description` field must be at most 1024 characters.

### frontmatter/description-no-first-person
**Severity**: Warning

The `description` should use third person. The description is injected into the system prompt, and inconsistent point-of-view can cause discovery problems.

| Before | After |
|--------|-------|
| `"I can help you process files"` | `"Processes files and extracts content. Use when..."` |
| `"You can use this to..."` | `"Used for..."` |

### frontmatter/description-no-xml
**Severity**: Error

The `description` field cannot contain XML tags.

```yaml
# Before
description: "Processes <data> tags"

# After
description: "Processes data tags"
```

**Why**: XML tags in the description field can interfere with Claude's system prompt injection and skill discovery.

---

## Content Rules

### content/max-lines
**Severity**: Warning

The SKILL.md body (excluding frontmatter) should be at most 500 lines.

```markdown
# Before (600 lines in SKILL.md)
## Detailed API Reference
[300 lines of API docs]

# After
## API Reference
See `references/api.md` for detailed documentation.
```

**Why**: The context window is a shared resource. Keeping SKILL.md concise ensures efficient token usage.

### content/no-windows-paths
**Severity**: Warning

Use forward slashes (`/`) for all file paths, even on Windows.

| Before | After |
|--------|-------|
| `docs\guide.md` | `docs/guide.md` |
| `scripts\helper.py` | `scripts/helper.py` |

**Why**: Unix-style paths work across all platforms, while Windows-style paths cause errors on Unix systems.

### content/spaces-not-tabs
**Severity**: Warning

Use spaces for indentation in YAML frontmatter, not tabs.

```yaml
# Before (with tabs)
name:	my-skill
description:	"Does something useful"

# After (with spaces)
name: my-skill
description: "Does something useful"
```

**Why**: YAML specification recommends spaces over tabs. Tabs can cause parsing issues in some YAML parsers.

---

## Naming Rules

### naming/prefer-gerund
**Severity**: Info

Skill directory names should use gerund form (verb + -ing) to clearly describe the activity.

| Before | After |
|--------|-------|
| `pdf-tool/` | `processing-pdfs/` |
| `code-review/` | `reviewing-code/` |
| `test-gen/` | `generating-tests/` |

### naming/no-vague
**Severity**: Warning

Avoid vague, non-descriptive names: `helper`, `utils`, `tools`, `misc`, `common`.

| Before | After |
|--------|-------|
| `helper/` | `processing-documents/` |
| `utils/` | `formatting-code/` |
| `tools/` | `analyzing-data/` |

**Why**: These names don't describe what the skill does, making it hard for Claude to determine when to use it.

### naming/no-generic
**Severity**: Warning

Avoid overly generic names: `documents`, `data`, `files`, `stuff`, `things`.

| Before | After |
|--------|-------|
| `documents/` | `converting-documents/` |
| `data/` | `transforming-data/` |
| `files/` | `managing-config-files/` |

**Why**: Generic names don't help Claude understand when to activate the skill.

---

## Reference Rules

### reference/valid-paths
**Severity**: Error

All file references in SKILL.md must point to existing files.

```markdown
# Option 1: Fix the path
# Before
See `references/guide.md`
# After (if file is named differently)
See `references/user-guide.md`

# Option 2: Create the missing file
```

### reference/max-depth
**Severity**: Warning

Keep references one level deep from SKILL.md.

```
# Good (one level)
SKILL.md → references/api.md
SKILL.md → references/examples.md

# Bad (too deep)
SKILL.md → references/api.md → references/details.md
```

**Why**: Claude may partially read files when they're referenced from other referenced files.

---

## Security Rules

### security/no-secrets
**Severity**: Error

SKILL.md must not contain sensitive information: API keys, passwords, access tokens, private keys, connection strings with credentials.

```yaml
# Before
api_key = sk-1234567890abcdef
password = mysecretpassword

# After (use environment variables)
api_key = ${API_KEY}
password = ${PASSWORD}
```

**Detection patterns**:
```
sk-[a-zA-Z0-9]{20,}
api[_-]?key\s*[:=]\s*['"]?[a-zA-Z0-9]+
AKIA[0-9A-Z]{16}
password\s*[:=]\s*['"]?[^\s'"]+
```

---

## Severity Levels

| Level | Meaning | Action |
|-------|---------|--------|
| **Error** | Critical issue that will cause problems | Must fix |
| **Warning** | Issue that may cause problems or violates best practices | Should fix |
| **Info** | Suggestion for improvement | Consider fixing |

---

## Quality Score Calculation

```
Score = 100 - (Errors × 10) - (Warnings × 5) + BonusPoints (max 100)

BonusPoints (up to +20):
  + 5 if using gerund naming
  + 5 if description includes "Use when"
  + 5 if references are well-organized
  + 5 if under 200 lines (concise)
```

| Score | Grade | Meaning |
|-------|-------|---------|
| 90-100 | A | Excellent - follows all best practices |
| 80-89 | B | Good - minor improvements possible |
| 70-79 | C | Fair - some issues to address |
| 60-69 | D | Poor - significant issues |
| <60 | F | Failing - needs major revision |
