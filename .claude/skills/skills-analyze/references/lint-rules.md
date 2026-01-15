# Lint Rules Reference

Detailed explanations for each lint rule, based on [Anthropic's official Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices).

---

## Frontmatter Rules

### frontmatter/required
**Severity**: Error

Every SKILL.md must start with YAML frontmatter enclosed in `---` delimiters.

```yaml
---
name: my-skill
description: Does something useful
---
```

### frontmatter/name-required
**Severity**: Error

The `name` field is required in frontmatter.

### frontmatter/name-max-length
**Severity**: Error

The `name` field must be at most 64 characters.

### frontmatter/name-format
**Severity**: Error

The `name` field must contain only:
- Lowercase letters (a-z)
- Numbers (0-9)
- Hyphens (-)

```yaml
# Good
name: processing-pdfs
name: code-review-2

# Bad
name: Processing_PDFs    # uppercase, underscore
name: code review        # space
name: my.skill           # period
```

### frontmatter/name-no-reserved
**Severity**: Error

The `name` field cannot contain reserved words:
- `anthropic`
- `claude`

```yaml
# Bad
name: claude-helper
name: anthropic-tools
```

### frontmatter/description-required
**Severity**: Error

The `description` field is required in frontmatter.

### frontmatter/description-max-length
**Severity**: Error

The `description` field must be at most 1024 characters.

### frontmatter/description-no-first-person
**Severity**: Warning

The `description` should use third person, not first or second person. The description is injected into the system prompt, and inconsistent point-of-view can cause discovery problems.

```yaml
# Good
description: "Processes Excel files and generates reports"
description: "Extracts text from PDF documents"

# Bad
description: "I can help you process Excel files"
description: "You can use this to extract PDF text"
```

---

## Content Rules

### content/max-lines
**Severity**: Warning

The SKILL.md body (excluding frontmatter) should be at most 500 lines. If content exceeds this, use progressive disclosure by splitting into separate reference files.

**Why**: The context window is a shared resource. Keeping SKILL.md concise ensures efficient token usage.

**Fix**: Move detailed content to `references/` directory and link from SKILL.md:
```markdown
For detailed API reference, see `references/api.md`
```

### content/no-windows-paths
**Severity**: Warning

Use forward slashes (`/`) for all file paths, even on Windows.

```markdown
# Good
See `scripts/helper.py`
Reference: `reference/guide.md`

# Bad
See `scripts\helper.py`
Reference: `reference\guide.md`
```

**Why**: Unix-style paths work across all platforms, while Windows-style paths cause errors on Unix systems.

---

## Naming Rules

### naming/prefer-gerund
**Severity**: Info

Skill directory names should use gerund form (verb + -ing) to clearly describe the activity.

```
# Recommended (gerund)
processing-pdfs/
analyzing-code/
generating-tests/

# Acceptable alternatives
pdf-processing/
code-analyzer/
```

### naming/no-vague
**Severity**: Warning

Avoid vague, non-descriptive names:
- `helper`
- `utils`
- `tools`
- `misc`
- `common`

**Why**: These names don't describe what the skill does, making it hard for Claude to determine when to use it.

### naming/no-generic
**Severity**: Warning

Avoid overly generic names:
- `documents`
- `data`
- `files`
- `stuff`
- `things`

**Why**: Generic names don't help Claude understand when to activate the skill.

---

## Reference Rules

### reference/valid-paths
**Severity**: Error

All file references in SKILL.md must point to existing files.

Check for patterns like:
- `See [filename](path)`
- `See \`path/to/file\``
- `Reference: path/to/file`

### reference/max-depth
**Severity**: Warning

Keep references one level deep from SKILL.md. All reference files should link directly from SKILL.md to ensure Claude reads complete files when needed.

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

SKILL.md must not contain sensitive information:
- API keys (patterns like `sk-`, `api_key=`, `apikey:`)
- Passwords
- Access tokens
- Private keys
- Connection strings with credentials

**Detection patterns**:
```
# API Keys
sk-[a-zA-Z0-9]{20,}
api[_-]?key\s*[:=]\s*['"]?[a-zA-Z0-9]+

# AWS
AKIA[0-9A-Z]{16}
aws[_-]?secret[_-]?access[_-]?key

# Generic secrets
password\s*[:=]\s*['"]?[^\s'"]+
secret\s*[:=]\s*['"]?[^\s'"]+
token\s*[:=]\s*['"]?[^\s'"]+
```

---

## Severity Levels

| Level | Meaning | Action |
|-------|---------|--------|
| **Error** | Critical issue that will cause problems | Must fix |
| **Warning** | Issue that may cause problems or violates best practices | Should fix |
| **Info** | Suggestion for improvement | Consider fixing |
