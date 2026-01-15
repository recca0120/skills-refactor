# Skills Refactor

[![GitHub](https://img.shields.io/github/license/recca0120/skills-refactor)](LICENSE)

一組用於分析、重構和驗證 Claude Code Skills 的工具。幫助維護乾淨、不重複（DRY）的 skill 定義。

[English README](README.md)

## 功能特色

- **Lint 檢查**：依據 18 條官方最佳實踐規則驗證 skills
- **品質評分**：根據規則合規性計算 0-100 分
- **重複檢測**：找出精確重複、近似重複和結構重複的內容
- **重構抽取**：將共用內容抽取到 shared 模組
- **驗證**：確認重構後的 skills 仍能正常運作
- **Context 高效**：優化 token 使用量（總計約 4,400 tokens）

## 安裝

將 skills 複製到你的專案或個人 skills 目錄：

```bash
# 專案層級（複製全部三個 skills）
cp -r .claude/skills/skills-* /path/to/your/project/.claude/skills/

# 個人（全域）
cp -r .claude/skills/skills-* ~/.claude/skills/
```

或個別複製：

```bash
cp -r .claude/skills/skills-analyze ~/.claude/skills/
cp -r .claude/skills/skills-extract ~/.claude/skills/
cp -r .claude/skills/skills-validate ~/.claude/skills/
```

## 使用方式

### 分析 Skills

掃描 skills 找出 lint 問題和重複內容：

```
使用者：「分析 .claude/skills/ 目錄的 skills」
使用者：「lint 檢查我的 skills」
使用者：「找出 skills 目錄中的重複內容」
```

**輸出**：結構化報告，包含 lint 錯誤/警告和重複內容位置。

### 抽取共用內容

將重複內容重構到 shared 模組：

```
使用者：「把 workflow diagram 抽取到 shared/」
使用者：「重構 skills - 合併重複內容」
使用者：「建立共用參考檔案」
```

**輸出**：建立 `shared/` 目錄和抽取的內容，並更新原始 skills 的引用。

### 驗證 Skills

驗證重構後的 skills 是否正常運作：

```
使用者：「驗證重構後的 skills」
使用者：「產生 skills 的測試案例」
使用者：「檢查 skills 是否還能正常運作」
```

**輸出**：驗證報告和供手動測試的測試案例。

## Lint 規則

依據 [Anthropic 官方 Skill 撰寫最佳實踐](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)。

### Frontmatter 規則

| 規則 | 說明 | 嚴重度 |
|------|------|--------|
| `frontmatter/required` | 必須有 YAML frontmatter | Error |
| `frontmatter/starts-line-one` | frontmatter 必須從第 1 行開始 | Error |
| `frontmatter/name-required` | 必須有 `name` 欄位 | Error |
| `frontmatter/name-max-length` | `name` 最多 64 字元 | Error |
| `frontmatter/name-format` | `name` 只能用小寫字母、數字、連字號 | Error |
| `frontmatter/name-no-reserved` | `name` 不能含 "anthropic" 或 "claude" | Error |
| `frontmatter/name-no-xml` | `name` 不能含 XML 標籤 | Error |
| `frontmatter/description-required` | 必須有 `description` 欄位 | Error |
| `frontmatter/description-max-length` | `description` 最多 1024 字元 | Error |
| `frontmatter/description-no-first-person` | `description` 要用第三人稱 | Warning |
| `frontmatter/description-no-xml` | `description` 不能含 XML 標籤 | Error |

### 內容規則

| 規則 | 說明 | 嚴重度 |
|------|------|--------|
| `content/max-lines` | SKILL.md 最多 500 行 | Warning |
| `content/no-windows-paths` | 使用正斜線 `/` | Warning |
| `content/spaces-not-tabs` | 使用空格縮排，不用 tab | Warning |

### 命名規則

| 規則 | 說明 | 嚴重度 |
|------|------|--------|
| `naming/prefer-gerund` | 建議用動名詞形式（如 `processing-pdfs`）| Info |
| `naming/no-vague` | 避免 `helper`、`utils`、`tools` | Warning |
| `naming/no-generic` | 避免 `documents`、`data`、`files` | Warning |

### 引用規則

| 規則 | 說明 | 嚴重度 |
|------|------|--------|
| `reference/valid-paths` | 引用的檔案必須存在 | Error |
| `reference/max-depth` | 引用保持一層深 | Warning |

### 安全規則

| 規則 | 說明 | 嚴重度 |
|------|------|--------|
| `security/no-secrets` | 不含 API keys、密碼、tokens | Error |

## 品質評分

Skills 根據規則合規性評分 0-100：

```
分數 = 100 - (Errors × 10) - (Warnings × 5) + 加分 (最高 100)

加分項目：
  +5 使用動名詞命名（如 processing-pdfs）
  +5 description 包含 "Use when"
  +5 references 組織良好
  +5 少於 200 行
```

| 分數 | 等級 | 意義 |
|------|------|------|
| 90-100 | A | 優秀 |
| 80-89 | B | 良好 |
| 70-79 | C | 尚可 |
| 60-69 | D | 待改進 |
| <60 | F | 需要修改 |

## 專案結構

```
skills-refactor/
├── .claude/skills/                 # 複製這些到你的專案
│   ├── skills-analyze/             # 分析 + Lint skill
│   │   ├── SKILL.md
│   │   └── references/
│   │       ├── lint-rules.md
│   │       └── duplicate-patterns.md
│   │
│   ├── skills-extract/             # 重構 skill
│   │   ├── SKILL.md
│   │   └── references/
│   │       └── example.md
│   │
│   └── skills-validate/            # 驗證 skill
│       ├── SKILL.md
│       └── references/
│           └── checklist.md
│
├── examples/                       # 測試範例（不需複製）
│   └── sample-skills/
│       ├── skill-a/                # 正確範例（展示精確/近似重複）
│       ├── skill-b/                # 正確範例（展示精確/近似重複）
│       ├── Skill_C/                # 違規：name-format, secrets, windows-paths
│       ├── Skill_D/                # 違規：starts-line-one, name-no-xml, description-no-xml
│       ├── Skill_E/                # 違規：spaces-not-tabs
│       └── helper/                 # 違規：name-no-reserved, naming/no-vague
│
└── docs/
    └── claude-code-skills-guide.md
```

## 報告範例

```markdown
# Skills 分析報告

## 摘要
- 目錄：.claude/skills/sample-skills/
- 掃描 Skills：3 個
- Lint 問題：5 個（2 Error, 2 Warning, 1 Info）
- 發現重複：2 處

## Lint 檢查結果

### Errors (2)
- [frontmatter/name-format] Skill_C/SKILL.md
  name 含有底線和大寫：Helper_Tool → helper-tool

- [security/no-secrets] Skill_C/SKILL.md:25
  包含 API key 模式：api_key = sk-...

### Warnings (2)
- [frontmatter/description-no-first-person] Skill_C/SKILL.md
  使用第一人稱："I can help you..."

- [content/no-windows-paths] Skill_C/SKILL.md:20
  Windows 風格路徑：docs\missing.md → docs/missing.md

## 重複內容清單

### 重複 #1：Workflow Diagram
- 類型：近似重複（相似度 90%）
- 位置：
  - skill-a/SKILL.md:10-18
  - skill-b/SKILL.md:10-18
  - Skill_C/SKILL.md:10-18
- 建議：抽取到 shared/workflow-diagram.md
```

## 文件

詳見 [docs/claude-code-skills-guide.md](docs/claude-code-skills-guide.md) 獲取 Claude Code Skills 完整指南，包括：

- Skills 結構和配置
- 官方文件的最佳實踐
- 進階功能
- 範例

## 貢獻

歡迎貢獻！請隨時提交 issues 或 pull requests。

## 授權

MIT License - 詳見 [LICENSE](LICENSE)。

## 參考資料

- [Agent Skills - Claude Code Docs](https://code.claude.com/docs/en/skills)
- [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [How to Create Custom Skills](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills)
