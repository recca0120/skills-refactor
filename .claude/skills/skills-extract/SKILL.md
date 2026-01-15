---
name: extracting-skills
description: "Refactors Claude Code Skills by extracting duplicate content into shared modules. Use when user asks to refactor skills, extract common content, merge duplicates, or create shared references."
---

# Skills Extractor

Refactors Skills by extracting duplicate content into shared modules.

## Trigger Conditions

Activate when user requests:
- "refactor skills", "extract common content"
- "merge duplicates", "create shared references"
- "extract [specific content] to shared"

## Workflow

### Step 1: Review Analysis Report

If an analysis report exists, use it to identify:
- Duplicate content locations
- Suggested extractions
- Priority based on reduction potential

If no report exists, suggest running `skills-analyze` first.

### Step 2: Confirm Extraction Plan

Present the extraction plan to user:

```markdown
## Extraction Plan

### Item 1: [content name]
- **Source locations**:
  - skill-a/SKILL.md:10-25
  - skill-b/SKILL.md:15-30
- **Target**: shared/[filename].md
- **Strategy**: [extract | parameterize | template]
- **Lines saved**: ~30

Proceed with this extraction? (y/n)
```

### Step 3: Execute Extraction

Based on the strategy:

#### Strategy A: Extract to Reference

For exact duplicates - create shared file and add references.

**Steps**:
1. Create `shared/` directory if not exists
2. Create shared file with extracted content
3. Replace original content with reference

**Reference syntax**:
```markdown
> See `shared/[filename].md` for [description].
```

#### Strategy B: Parameterize Template

For near duplicates with variable parts.

**Steps**:
1. Identify variable parts
2. Create template with placeholders
3. Update each skill with specific values + reference

**Template format**:
```markdown
# In shared/templates/[name].md

## [Title]

[Common content here]

**Variable section**: {{variable_name}}

[More common content]
```

**Usage in SKILL.md**:
```markdown
> Template: `shared/templates/[name].md`
> Current value: [specific value for this skill]
```

#### Strategy C: Simplify References

When shared file already exists.

**Steps**:
1. Verify shared file exists and is up-to-date
2. Replace duplicate content with reference
3. Ensure consistent reference format

### Step 4: Update Original Files

For each affected SKILL.md:

1. **Backup**: Create `.bak` file or note original content
2. **Replace**: Remove duplicate content
3. **Add reference**: Insert reference to shared file
4. **Verify**: Ensure file is still valid markdown

### Step 5: Verify Results

After extraction:
1. Check all references point to existing files
2. Verify SKILL.md frontmatter is intact
3. Confirm line count reduction

---

## Reference Syntax Guidelines

### Simple Reference
```markdown
> See `shared/filename.md` for details.
```

### Reference with Context
```markdown
> Workflow diagram: `shared/workflow-diagrams.md`
> Current stage: **Implement**
```

### Reference with Section
```markdown
> See `shared/api-reference.md#authentication` for auth details.
```

### Conditional Reference
```markdown
> For basic usage, see instructions below.
> For advanced features, see `shared/advanced-features.md`.
```

---

## Directory Structure

After extraction, the skills directory should look like:

```
.claude/skills/
├── shared/                    # Shared content
│   ├── workflow-diagrams.md   # Extracted diagrams
│   ├── reference-list.md      # Common references
│   └── templates/             # Reusable templates
│       ├── test-plan.md
│       └── output-format.md
├── skill-a/
│   └── SKILL.md              # References shared/
├── skill-b/
│   └── SKILL.md              # References shared/
└── skill-c/
    └── SKILL.md              # References shared/
```

---

## Output Report

After extraction, provide summary:

```markdown
# Extraction Complete

## Changes Made

### Created Files
- shared/workflow-diagrams.md (45 lines)
- shared/reference-list.md (12 lines)

### Modified Files
- skill-a/SKILL.md: -30 lines, +2 lines (reference)
- skill-b/SKILL.md: -30 lines, +2 lines (reference)
- skill-c/SKILL.md: -30 lines, +2 lines (reference)

## Summary
- Total lines removed: 90
- Total lines added: 6 (references) + 57 (shared files)
- Net reduction: 27 lines
- Duplication eliminated: 3 locations → 1 source
```

---

## Important Notes

- Always confirm with user before modifying files
- Create backups or allow undo
- Preserve YAML frontmatter in all SKILL.md files
- Verify all references after extraction
- Keep shared file names descriptive
