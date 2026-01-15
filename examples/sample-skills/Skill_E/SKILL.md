---
name:	tab-indented-skill
description:	"This skill uses tabs for indentation. Tabs should not be used in YAML."
---

# Tab Indented Skill

This skill demonstrates the spaces-not-tabs violation.

## Violations in this file

1. **content/spaces-not-tabs**: YAML frontmatter uses tab characters for indentation

## Note

The `name` and `description` fields above use tab characters (not visible in rendered markdown).
To verify, use: `cat -A SKILL.md` to see `^I` markers for tabs.

## Workflow

1. Read input
2. Process
3. Output
