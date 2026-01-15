# Skills Refactor

Claude Code Skills 開發和維護工具包，提供技能分析、重構和驗證功能。

## 專案概述

此專案包含三個互相關聯的 Claude Code Skills：

1. **skills-analyze** - 掃描技能目錄，檢查質量問題和重複內容
2. **skills-extract** - 將重複內容重構為共享模組
3. **skills-validate** - 驗證重構後的技能完整性

## 專案結構

```
.claude/skills/           # 主要技能目錄
├── skills-analyze/       # 分析技能（含 lint-rules.md, duplicate-patterns.md）
├── skills-extract/       # 重構技能（含 example.md 範例）
└── skills-validate/      # 驗證技能（含 checklist.md）
examples/sample-skills/   # 測試範例（包含正確和故意違規的範例）
docs/                     # 文檔（claude-code-skills-guide.md）
```

## 技術棧

- **Claude Code Skills Framework** - 技能定義系統
- **Markdown + YAML** - 技能文件格式（SKILL.md 含 YAML frontmatter）
- **無傳統建置系統** - 技能為 Markdown 模板，由 Claude Code 直接載入

## SKILL.md 文件格式

```yaml
---
name: lowercase-with-hyphens    # 必填，最多 64 字元
description: "第三人稱描述..."   # 必填，最多 1024 字元
---

# 標題
## Trigger Conditions
## Workflow
## Important Notes
```

## 命名慣例

- **技能目錄**: 使用動名詞形式（verb-ing）
  - 正確：`processing-pdfs`, `analyzing-skills`
  - 避免：`Helper`, `Tools`, `Utils`
- **name 欄位**: 小寫、數字、連字號（禁用 "anthropic" 或 "claude"）
- **description**: 使用第三人稱，避免 "I" 或 "my"

## Lint 規則（22 條）

### Frontmatter 規則
- `frontmatter/required` - 必須有 YAML frontmatter
- `frontmatter/starts-line-one` - frontmatter 必須從第 1 行開始
- `frontmatter/name-format` - 小寫、數字、連字號
- `frontmatter/name-no-xml` - name 不能含 XML 標籤
- `frontmatter/description-no-first-person` - 使用第三人稱
- `frontmatter/description-no-xml` - description 不能含 XML 標籤

### Content 規則
- `content/max-lines` - 內容最多 500 行
- `content/no-windows-paths` - 使用 `/` 而非 `\`
- `content/spaces-not-tabs` - 使用空格縮排，不用 tab

### Naming 規則
- `naming/prefer-gerund` - 使用動名詞形式
- `naming/no-vague` - 避免模糊名稱

### Reference 規則
- `reference/valid-paths` - 引用路徑必須存在
- `reference/max-depth` - 最多 1 層深度

### Security 規則
- `security/no-secrets` - 禁止 API 金鑰、密碼、Token

## 重複檢測類型

1. **完全重複**: 100% 相同的區塊（≥5 行）
2. **近似重複**: ≥80% 相似度
3. **結構重複**: 相同模式，不同數值

## 工作流程

```
分析 (skills-analyze)
  ↓ Lint 報告 + 重複位置
重構 (skills-extract)
  ↓ 建立 shared/ 目錄，更新引用
驗證 (skills-validate)
  ↓ 驗證報告 + 測試案例
```

## 開發指南

- 修改技能時，確保 YAML frontmatter 格式正確
- 新增引用時，確保目標文件存在
- 文檔需維護英文和繁體中文兩個版本

## 測試範例

| 範例 | 類型 | 展示的規則/模式 |
|------|------|-----------------|
| `skill-a/`, `skill-b/` | 正確 | 精確重複、近似重複、結構重複 |
| `Skill_C/` | 違規 | name-format, description-no-first-person, windows-paths, secrets |
| `Skill_D/` | 違規 | starts-line-one, name-no-xml, description-no-xml |
| `Skill_E/` | 違規 | spaces-not-tabs |
| `helper/` | 違規 | name-no-reserved, naming/no-vague |

## 重要參考文件

- `docs/claude-code-skills-guide.md` - 完整的 Claude Code Skills 指南
- `.claude/skills/skills-analyze/references/lint-rules.md` - 詳細 lint 規則說明
- `.claude/skills/skills-extract/references/example.md` - 重構範例
