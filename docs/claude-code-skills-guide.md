# Claude Code Skills 完整指南

## 目錄

1. [概述](#概述)
2. [Skills vs Slash Commands vs CLAUDE.md](#skills-vs-slash-commands-vs-claudemd)
3. [Skills 結構](#skills-結構)
4. [SKILL.md 檔案格式](#skillmd-檔案格式)
5. [最佳實踐](#最佳實踐)
6. [進階功能](#進階功能)
7. [開發流程](#開發流程)
8. [反模式](#反模式)
9. [檢查清單](#檢查清單)
10. [參考資料](#參考資料)

---

## 概述

Claude Code Skills 是一種可擴展的功能包，可以為 Claude 注入特定任務的指令。Skills 是專門的 prompt 模板，透過上下文注入而非傳統程式碼執行來增強 Claude 的能力。

### 核心特點

- **自動觸發**：Claude 根據用戶請求自動判斷並調用適合的 Skill
- **按需載入**：Skills 只在需要時載入，保持基礎 prompt 精簡
- **模組化設計**：每個 Skill 專注於特定任務，可組合使用
- **跨平台標準**：Anthropic 將 Agent Skills 規範作為開放標準發布

---

## Skills vs Slash Commands vs CLAUDE.md

| 特性 | Skills | Slash Commands | CLAUDE.md |
|------|--------|----------------|-----------|
| **觸發方式** | 模型自動觸發 | 用戶手動輸入 `/command` | 自動載入 |
| **存放位置** | `.claude/skills/` | `.claude/commands/` | 專案根目錄 |
| **結構** | 目錄 + SKILL.md + 資源 | 單一 markdown 檔案 | 單一 markdown 檔案 |
| **用途** | 複雜、多檔案的工作流程 | 可重複的明確操作 | 專案基礎規則和慣例 |
| **上下文影響** | 按需載入 | 按需載入 | 永久載入 |

### 何時使用什麼

- **CLAUDE.md**：短小、永遠適用的專案慣例和標準
- **Slash Commands**：需要明確、可重複的終端入口點
- **Skills**：需要 Claude 自動應用較豐富的工作流程（通常包含支援檔案）

---

## Skills 結構

### 目錄位置

- **個人 Skills**：`~/.claude/skills/`
- **專案 Skills**：`.claude/skills/`（建議加入版本控制）

### 基本結構

```
.claude/skills/
└── my-skill/
    ├── SKILL.md          # 必須 - 核心指令檔案
    ├── scripts/          # 可選 - 可執行腳本
    │   └── helper.py
    ├── references/       # 可選 - 參考文件
    │   └── guide.md
    └── assets/           # 可選 - 模板和靜態資源
        └── template.json
```

### 檔案說明

| 檔案/目錄 | 用途 |
|-----------|------|
| `SKILL.md` | 核心 prompt 檔案，包含 YAML frontmatter 和指令 |
| `scripts/` | Python、Bash 等可執行自動化腳本 |
| `references/` | 額外的參考文件，Claude 需要時讀取 |
| `assets/` | 模板和靜態資源 |

---

## SKILL.md 檔案格式

### 基本格式

```yaml
---
name: my-skill-name
description: "Describes what this skill does. Use when user asks to do X or Y."
---

# Skill Title

## Instructions

Instructions for Claude to follow...
```

### Frontmatter 欄位

| 欄位 | 必須 | 限制 | 說明 |
|------|------|------|------|
| `name` | 是 | 最多 64 字元 | 只能用小寫字母、數字、連字號 |
| `description` | 是 | 最多 1024 字元 | 描述做什麼和何時使用，必須用第三人稱 |
| `allowed-tools` | 否 | - | 限制可使用的工具清單 |

### name 格式規則

- ✅ 只能包含：小寫字母 (a-z)、數字 (0-9)、連字號 (-)
- ❌ 不能包含：大寫字母、底線、空格、特殊符號
- ❌ 不能包含保留字：`anthropic`、`claude`

```yaml
# 正確
name: processing-pdfs
name: code-review-2

# 錯誤
name: Processing_PDFs    # 大寫、底線
name: my skill           # 空格
name: claude-helper      # 保留字
```

### description 規則

**永遠使用第三人稱**。description 會注入系統 prompt，不一致的人稱會導致發現問題。

```yaml
# 正確
description: "Processes Excel files and generates reports. Use when working with spreadsheets."

# 錯誤
description: "I can help you process Excel files"
description: "You can use this to process Excel files"
```

---

## 最佳實踐

> 以下內容整理自 [Anthropic 官方 Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### 1. 簡潔是關鍵 (Concise is Key)

Context window 是共享資源。**預設假設**：Claude 已經非常聰明，只添加 Claude 還不知道的內容。

```markdown
✅ 好的（約 50 tokens）：
## Extract PDF text
Use pdfplumber for text extraction:
```python
import pdfplumber
with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```

❌ 不好的（約 150 tokens）：
## Extract PDF text
PDF (Portable Document Format) files are a common file format...
（過多解釋 Claude 已經知道的內容）
```

### 2. 設定適當的自由度

| 自由度 | 使用時機 | 範例 |
|--------|----------|------|
| **高** | 多種方法都有效、決定取決於上下文 | 程式碼審查流程 |
| **中** | 有偏好的模式但允許變化 | 帶參數的腳本 |
| **低** | 操作脆弱易錯、一致性關鍵 | 資料庫遷移 |

### 3. 單一職責 (Single Responsibility)

每個 Skill 應專注於單一工作流程。

```
✅ 好的做法：
- code-reviewer/
- test-generator/
- security-auditor/

❌ 不好的做法：
- all-in-one-dev-helper/
```

### 4. 命名規範

使用 **動名詞形式**（verb + -ing）命名。

```yaml
✅ 好的命名：
- processing-pdfs
- analyzing-spreadsheets
- testing-code

✅ 可接受的替代：
- pdf-processing（名詞片語）
- process-pdfs（動作導向）

❌ 避免：
- helper, utils, tools（模糊）
- documents, data, files（過於通用）
```

### 5. 漸進式揭露 (Progressive Disclosure)

這是構建 Skills **最重要的概念**。只顯示足夠的資訊幫助 Claude 決定下一步。

```
my-skill/
├── SKILL.md           # 核心指令（500 行以內）
└── references/
    ├── detailed-api.md    # 詳細 API 文件
    └── examples.md        # 範例程式碼
```

**保持引用一層深**：

```markdown
❌ 太深：
SKILL.md → advanced.md → details.md → actual info

✅ 一層深：
SKILL.md → advanced.md
SKILL.md → reference.md
```

### 6. 控制 SKILL.md 長度

保持 SKILL.md 在 **500 行以內**。超過則拆分到獨立檔案。

### 7. 使用工作流程和檢查清單

複雜任務使用 checklist 追蹤進度：

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

### 8. 實作 Feedback Loop

驗證 → 修正 → 重複

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

## 進階功能

### 整合可執行腳本

Skills 可以包含 Python、Bash 等腳本。

```
my-skill/
├── SKILL.md
└── scripts/
    ├── analyze.py
    └── validate.py
```

在 SKILL.md 中說明用途：

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

### MCP 工具引用

使用 MCP 工具時，使用完整名稱：

```markdown
Use the BigQuery:bigquery_schema tool to retrieve schemas.
Use the GitHub:create_issue tool to create issues.
```

格式：`ServerName:tool_name`

---

## 開發流程

### Evaluation-Driven Development

**先建立評估，再寫文件。**

1. **識別缺口**：在沒有 Skill 的情況下執行任務，記錄失敗點
2. **建立評估**：建立 3 個測試場景
3. **建立基準**：測量無 Skill 時的表現
4. **撰寫最小指令**：只寫足夠解決問題的內容
5. **迭代**：執行評估，對比基準，改進

### 與 Claude 迭代開發

1. **完成任務**：先和 Claude A 完成一次任務
2. **識別模式**：找出可重複使用的知識
3. **建立 Skill**：請 Claude A 建立 Skill
4. **審查簡潔性**：移除不必要的解釋
5. **測試**：用 Claude B（新實例）測試
6. **迭代**：根據觀察改進

### 觀察 Claude 如何使用 Skills

注意：
- Claude 是否按預期順序讀取檔案？
- Claude 是否遺漏重要的引用？
- Claude 是否反覆讀取同一檔案？（考慮移到主 SKILL.md）
- Claude 是否從未存取某些檔案？（可能不需要）

---

## 反模式

### 避免 Windows 路徑

```
✅ 好的：scripts/helper.py
❌ 避免：scripts\helper.py
```

### 避免提供太多選項

```markdown
❌ 不好：
"You can use pypdf, or pdfplumber, or PyMuPDF, or..."

✅ 好的：
"Use pdfplumber for text extraction.
For scanned PDFs requiring OCR, use pdf2image instead."
```

### 避免時間敏感資訊

```markdown
❌ 不好：
If you're doing this before August 2025, use the old API.

✅ 好的：
## Current method
Use the v2 API endpoint

## Old patterns
<details>
<summary>Legacy v1 API (deprecated)</summary>
...
</details>
```

### 避免假設工具已安裝

```markdown
❌ 不好：
"Use the pdf library to process the file."

✅ 好的：
"Install required package: `pip install pypdf`
Then use it:
```python
from pypdf import PdfReader
```"
```

---

## 檢查清單

### 核心品質

- [ ] description 具體且包含關鍵詞
- [ ] description 包含做什麼和何時使用
- [ ] description 使用第三人稱
- [ ] SKILL.md 內容少於 500 行
- [ ] 額外細節放在獨立檔案
- [ ] 無時間敏感資訊
- [ ] 術語一致
- [ ] 範例具體而非抽象
- [ ] 檔案引用只有一層深
- [ ] 適當使用漸進式揭露
- [ ] 工作流程有清楚步驟

### 程式碼和腳本

- [ ] 腳本處理錯誤而非推給 Claude
- [ ] 錯誤處理明確且有幫助
- [ ] 無「魔術數字」（所有值都有說明）
- [ ] 列出必要的套件
- [ ] 無 Windows 路徑
- [ ] 關鍵操作有驗證步驟
- [ ] 品質關鍵任務有 feedback loop

### 測試

- [ ] 建立至少 3 個評估場景
- [ ] 用 Haiku、Sonnet、Opus 測試
- [ ] 用真實場景測試
- [ ] 納入團隊回饋

---

## 參考資料

### 官方文件

- [Agent Skills Overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
- [Skill Authoring Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [Claude Code Skills Docs](https://code.claude.com/docs/en/skills)

### 官方資源

- [Anthropic Skills Repository](https://github.com/anthropics/skills)

### 深入分析

- [Inside Claude Code Skills: Structure, Prompts, Invocation](https://mikhail.io/2025/10/claude-code-skills/)
- [Claude Agent Skills: A First Principles Deep Dive](https://leehanchung.github.io/blogs/2025/10/26/claude-skills-deep-dive/)

### 工具與資源

- [Claude Code Skill Factory](https://github.com/alirezarezvani/claude-code-skill-factory)
- [Skills Marketplace](https://skillsmp.com/)
