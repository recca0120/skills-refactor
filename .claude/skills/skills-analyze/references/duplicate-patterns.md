# Duplicate Detection Patterns

Guide for identifying duplicate content across Skills.

---

## Types of Duplicates

### 1. Exact Duplicates

Identical text blocks appearing in multiple SKILL.md files.

**Detection criteria**:
- Block size >= 5 lines
- 100% identical content (after whitespace normalization)

**Common examples**:
- Reference resource lists
- Workflow diagrams
- Boilerplate sections
- Import statements

**Example**:
```markdown
# In skill-a/SKILL.md
## Reference Resources
- **Workflow guide**: `shared/workflow.md`
- **API reference**: `shared/api.md`
- **Examples**: `shared/examples.md`

# In skill-b/SKILL.md (identical)
## Reference Resources
- **Workflow guide**: `shared/workflow.md`
- **API reference**: `shared/api.md`
- **Examples**: `shared/examples.md`
```

---

### 2. Near Duplicates

Similar text blocks with minor differences (>= 80% similarity).

**Detection criteria**:
- Block size >= 5 lines
- Similarity score >= 80%
- Same structure, different values

**Common examples**:
- Workflow position diagrams (different highlighted stages)
- Step-by-step instructions (same steps, different details)
- Configuration templates (same structure, different values)

**Example**:
```markdown
# In skill-a/SKILL.md
## Current Stage
```
┌─────────┐   ┌─────────┐   ┌─────────┐
│ Design  │ → │Implement│ → │ Review  │
│  (✓)    │   │  (🔨)   │   │         │
└─────────┘   └─────────┘   └─────────┘
```

# In skill-b/SKILL.md (near duplicate - different highlight)
## Current Stage
```
┌─────────┐   ┌─────────┐   ┌─────────┐
│ Design  │ → │Implement│ → │ Review  │
│         │   │  (✓)    │   │  (🔨)   │
└─────────┘   └─────────┘   └─────────┘
```
```

---

### 3. Structural Duplicates

Same patterns/templates with completely different content.

**Detection criteria**:
- Same markdown structure (headers, lists, tables)
- Same placeholder patterns
- Different actual values

**Common examples**:
- Test plan templates
- Output format specifications
- Checklist structures

**Example**:
```markdown
# In skill-a/SKILL.md
## Test Plan
| # | Test Name | Type | Purpose |
|---|-----------|------|---------|
| 1 | Login test | Unit | Verify login |
| 2 | Auth test | Integration | Verify auth flow |

# In skill-b/SKILL.md (structural duplicate)
## Test Plan
| # | Test Name | Type | Purpose |
|---|-----------|------|---------|
| 1 | Export test | Unit | Verify export |
| 2 | Import test | Integration | Verify import flow |
```

---

## Refactoring Suggestions

### For Exact Duplicates

**Recommendation**: Extract to shared reference file.

```markdown
# Before (in multiple skills)
## Reference Resources
- **Workflow guide**: `shared/workflow.md`
...

# After
# In shared/reference-list.md
## Reference Resources
- **Workflow guide**: `shared/workflow.md`
...

# In each SKILL.md
> See `shared/reference-list.md` for available resources.
```

### For Near Duplicates

**Recommendation**: Create parameterized template.

```markdown
# Create shared/workflow-position.md with parameter
## Current Stage: {{current_stage}}

┌─────────┐   ┌─────────┐   ┌─────────┐
│ Design  │ → │Implement│ → │ Review  │
│{{design}}│   │{{impl}} │   │{{review}}│
└─────────┘   └─────────┘   └─────────┘

# In each SKILL.md, reference with context
> Current workflow position: **Implement** stage
> See `shared/workflow-position.md` for full diagram
```

### For Structural Duplicates

**Recommendation**: Extract template to shared file.

```markdown
# Create shared/templates/test-plan.md
## Test Plan Template

| # | Test Name | Type | Purpose |
|---|-----------|------|---------|
| 1 | [name] | [type] | [purpose] |
| 2 | [name] | [type] | [purpose] |

# In each SKILL.md
> Use test plan format from `shared/templates/test-plan.md`

## Tests for This Skill
[Specific tests listed here]
```

---

## Similarity Calculation

### Method 1: Line-by-line comparison

For blocks of similar size:
1. Normalize whitespace
2. Compare line by line
3. Calculate matching ratio

```
Similarity = matching_lines / total_lines
```

### Method 2: Token-based comparison

For varying content:
1. Tokenize both blocks
2. Calculate Jaccard similarity

```
Similarity = |A ∩ B| / |A ∪ B|
```

### Method 3: Structural comparison

For template detection:
1. Extract structure (headers, list markers, table structure)
2. Compare structural patterns
3. Identify placeholder positions

---

## Reporting Format

When reporting duplicates:

```markdown
### Duplicate #N: [descriptive name]

- **Type**: exact | near (X% similar) | structural
- **Lines affected**: [total lines across all occurrences]
- **Locations**:
  - `skill-a/SKILL.md:10-25` (16 lines)
  - `skill-b/SKILL.md:15-30` (16 lines)
  - `skill-c/SKILL.md:20-35` (16 lines)
- **Suggestion**: [specific refactoring recommendation]
- **Estimated reduction**: [lines that would be saved]
```
