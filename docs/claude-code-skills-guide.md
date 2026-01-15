# Claude Code Skills 完整指南

## 目錄

1. [概述](#概述)
2. [Skills vs Slash Commands vs CLAUDE.md](#skills-vs-slash-commands-vs-claudemd)
3. [Skills 結構](#skills-結構)
4. [SKILL.md 檔案格式](#skillmd-檔案格式)
5. [最佳實踐](#最佳實踐)
6. [進階功能](#進階功能)
7. [提高 Skills 觸發可靠性](#提高-skills-觸發可靠性)
8. [現有工具與資源](#現有工具與資源)
9. [範例](#範例)
10. [參考資料](#參考資料)

---

## 概述

Claude Code Skills 是一種可擴展的功能包，可以為 Claude 注入特定任務的指令。Skills 是專門的 prompt 模板，透過上下文注入而非傳統程式碼執行來增強 Claude 的能力。

### 核心特點

- **自動觸發**：Claude 根據用戶請求自動判斷並調用適合的 Skill
- **按需載入**：Skills 只在需要時載入，保持基礎 prompt 精簡
- **模組化設計**：每個 Skill 專注於特定任務，可組合使用
- **跨平台標準**：2025 年 12 月，Anthropic 將 Agent Skills 規範作為開放標準發布，OpenAI Codex CLI 也採用相同格式

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
    │   ├── helper.py
    │   └── analyze.sh
    ├── references/       # 可選 - 參考文件
    │   └── REFERENCE.md
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
name: "My Skill Name"
description: "簡短描述此 Skill 的用途和觸發時機"
---

# Skill 標題

## 指令內容

這裡放置 Claude 應該遵循的指令...
```

### Frontmatter 欄位

| 欄位 | 必須 | 說明 |
|------|------|------|
| `name` | 是 | Skill 名稱，最多 64 字元 |
| `description` | 是 | 觸發描述，最多 200 字元。Claude 依此判斷何時調用 |
| `allowed-tools` | 否 | 限制可使用的工具清單 |
| `model` | 否 | 指定使用的模型 |

### allowed-tools 範例

```yaml
---
name: "Code Analyzer"
description: "分析程式碼結構和品質"
allowed-tools:
  - Read
  - Grep
  - Glob
---
```

當此 Skill 啟用時，Claude 只能使用指定的工具，無需額外請求權限。這對於安全敏感的工作流程很有用。

---

## 最佳實踐

> 以下內容整理自 [Anthropic 官方 Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### 核心原則

#### 1. 簡潔是關鍵 (Concise is Key)

Context window 是公共資源。你的 Skill 與其他所有內容共享 context window：系統 prompt、對話歷史、其他 Skills 的 metadata、實際請求。

**預設假設**：Claude 已經非常聰明。只添加 Claude 還不知道的內容。

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

#### 2. 設定適當的自由度

| 自由度 | 使用時機 | 範例 |
|--------|----------|------|
| **高** | 多種方法都有效、決定取決於上下文 | 程式碼審查流程 |
| **中** | 有偏好的模式但允許變化 | 帶參數的腳本 |
| **低** | 操作脆弱易錯、一致性關鍵 | 資料庫遷移 |

#### 3. 保持專注 (Single Responsibility)

每個 Skill 應專注於單一工作流程。多個專注的 Skills 比一個大型 Skill 更容易組合使用。

```
✅ 好的做法：
- code-reviewer/
- test-generator/
- security-auditor/

❌ 不好的做法：
- all-in-one-dev-helper/
```

### 命名規範

使用 **動名詞形式**（verb + -ing）命名，清楚描述 Skill 提供的能力。

```yaml
✅ 好的命名（動名詞形式）：
- processing-pdfs
- analyzing-spreadsheets
- managing-databases
- testing-code
- writing-documentation

✅ 可接受的替代：
- pdf-processing（名詞片語）
- process-pdfs（動作導向）

❌ 避免：
- helper, utils, tools（模糊）
- documents, data, files（過於通用）
- anthropic-helper, claude-tools（保留字）
```

### 撰寫有效的 description

description 欄位啟用 Skill 發現功能，應包含 **做什麼** 和 **何時使用**。

**重要**：永遠使用**第三人稱**。description 會注入系統 prompt，不一致的人稱會導致發現問題。

```yaml
✅ 好的：
description: "Processes Excel files and generates reports"

❌ 避免：
description: "I can help you process Excel files"
description: "You can use this to process Excel files"
```

**有效範例**：

```yaml
# PDF Processing
description: "Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction."

# Git Commit Helper
description: "Generate descriptive commit messages by analyzing git diffs. Use when the user asks for help writing commit messages or reviewing staged changes."
```

**避免模糊描述**：

```yaml
❌ description: "Helps with documents"
❌ description: "Processes data"
❌ description: "Does stuff with files"
```

### 使用漸進式揭露 (Progressive Disclosure)

這是構建 Skills **最重要的概念**。只顯示足夠的資訊幫助 Claude 決定下一步，然後在需要時揭露更多細節。

```
my-skill/
├── SKILL.md           # 核心指令（500 行以內）
└── references/
    ├── detailed-api.md    # 詳細 API 文件
    ├── examples.md        # 範例程式碼
    └── troubleshooting.md # 疑難排解
```

**保持引用一層深**：所有參考檔案應直接從 SKILL.md 連結。

```markdown
❌ 太深：
SKILL.md → advanced.md → details.md → actual info

✅ 一層深：
SKILL.md → advanced.md
SKILL.md → reference.md
SKILL.md → examples.md
```

**長參考檔案要有目錄**：超過 100 行的參考檔案，頂部要包含目錄。

### 控制 SKILL.md 長度

保持 SKILL.md 在 **500 行以內**。如果內容超過，使用漸進式揭露模式拆分到獨立檔案。

### 避免時間敏感資訊

```markdown
❌ 不好（會過時）：
If you're doing this before August 2025, use the old API.

✅ 好的（使用 old patterns 區塊）：
## Current method
Use the v2 API endpoint

## Old patterns
<details>
<summary>Legacy v1 API (deprecated 2025-08)</summary>
...
</details>
```

### 使用一致的術語

```
✅ 一致：
- 永遠用 "API endpoint"
- 永遠用 "field"
- 永遠用 "extract"

❌ 不一致：
- 混用 "API endpoint", "URL", "API route", "path"
- 混用 "field", "box", "element", "control"
- 混用 "extract", "pull", "get", "retrieve"
```

### 路徑格式

永遠使用正斜線，即使在 Windows 上：

```
✅ 好的：scripts/helper.py, reference/guide.md
❌ 避免：scripts\helper.py, reference\guide.md
```

### 安全考量

- 不要在 SKILL.md 中硬編碼敏感資訊（API keys、密碼等）
- 下載的 Skills 使用前要先審查
- 使用 `allowed-tools` 限制工具權限

---

## 進階功能

### 整合可執行腳本

Skills 可以包含 Python、JavaScript/Node.js、Bash 等腳本。

```yaml
---
name: "Data Analyzer"
description: "分析資料集並產生報告"
dependencies: "python>=3.8, pandas>=1.5.0"
---
```

在 `scripts/` 目錄中：

```python
# scripts/analyze.py
import pandas as pd
import sys

def analyze(file_path):
    df = pd.read_csv(file_path)
    return df.describe()

if __name__ == "__main__":
    print(analyze(sys.argv[1]))
```

### Subagent 整合

內建的 agents（Explore、Plan、general-purpose）**無法**存取你的 Skills。只有在 `.claude/agents/` 中定義的自訂 subagent，且有明確的 `skills` 欄位，才能使用 Skills。

```yaml
# .claude/agents/my-agent.yml
name: "Custom Agent"
skills:
  - my-skill
  - another-skill
```

### 雙訊息模式

Skills 每次調用會注入兩個訊息：
1. **可見的 metadata 訊息**（`isMeta: false`）- 讓用戶知道正在發生什麼
2. **隱藏的指令 prompt**（`isMeta: true`）- Claude 接收但用戶看不到

---

## 提高 Skills 觸發可靠性

根據測試，Skills 的自動觸發並不總是可靠。以下是經過驗證的改善方法：

### 問題

基本指令方式只有約 20-50% 的成功率。

### 解決方案 1：Forced Eval Hook（84% 成功率）

建立一個 commitment 機制，要求 Claude 明確評估每個 skill 並回答 YES/NO。

三步驟流程：
1. **評估** - Claude 檢視每個可用的 Skill
2. **承諾** - Claude 明確表示 YES 或 NO
3. **執行** - 執行已承諾的 Skill

### 解決方案 2：LLM Eval Hook（80% 成功率）

在送出 prompt 前先用 Claude API 預先評估 skills。成本降低約 10%，速度快 17%。

### 實作資源

這些 hooks 可在 `claude-skills-cli` repository 中找到完整實作。

---

## 現有工具與資源

### 1. Claude Code Skill Factory

**GitHub**: https://github.com/alirezarezvani/claude-code-skill-factory

強大的開源工具包，用於大規模建置和部署 Claude Skills、Code Agents、自訂 Slash Commands 和 LLM Prompts。

**主要功能**：
- Skills Factory - 生成完整的 SKILL.md 檔案
- Agents Factory - 建立專門的 Claude Code agents
- Prompt Factory - 產生 69 種專業 preset
- Hooks Factory - 建立 Claude Code hooks
- Slash Command Factory - 建立自訂命令
- Codex CLI Bridge - Claude Code 與 OpenAI Codex CLI 互通

**快速開始**：
```
/build skill  → 回答 4-5 個問題 → /validate-output → /install-skill
```

### 2. Skill Builder (metaskills)

**GitHub**: https://github.com/metaskills/skill-builder

專注於將 sub-agents 轉換為 skills 的工具。

### 3. Claude Code Tresor

提供 8 個 production-ready 的自主 skills：
- code-reviewer
- test-generator
- security-auditor
- 等等...

### 4. Claude Skills Library

提供 26+ 預建的領域套件，涵蓋：
- Marketing
- Product Management
- Engineering
- C-level Advisory

### 5. SkillsMP (Skills Marketplace)

**網站**: https://skillsmp.com/

超過 63,000+ agent skills，支援 Claude Code、Codex CLI 和 ChatGPT。

---

## 範例

### 範例 1：程式碼審查 Skill

```
.claude/skills/code-reviewer/
├── SKILL.md
└── references/
    └── review-checklist.md
```

**SKILL.md**:
```yaml
---
name: "Code Reviewer"
description: "當用戶要求審查程式碼、PR review 或 code review 時使用"
allowed-tools:
  - Read
  - Grep
  - Glob
---

# Code Reviewer

## 職責

你是一位專業的程式碼審查者。當被要求審查程式碼時，請：

1. 檢查程式碼品質和可讀性
2. 識別潛在的 bugs 和安全問題
3. 評估架構和設計模式
4. 提供建設性的改善建議

## 審查清單

參見 `references/review-checklist.md` 獲取詳細檢查項目。

## 輸出格式

以結構化的方式提供審查結果：
- 摘要
- 問題列表（按嚴重程度排序）
- 改善建議
- 優點
```

### 範例 2：測試生成 Skill

```yaml
---
name: "Test Generator"
description: "當用戶要求生成測試、建立測試案例或寫單元測試時使用"
---

# Test Generator

## 職責

生成高品質的測試程式碼。

## 步驟

1. 分析目標程式碼
2. 識別測試場景
3. 生成測試案例
4. 包含邊界條件和錯誤情況

## 測試框架

根據專案語言使用適當的測試框架：
- JavaScript/TypeScript: Jest, Vitest
- Python: pytest
- Java: JUnit 5
- Go: testing package
```

---

## 參考資料

### 官方文件
- [Agent Skills - Claude Code Docs](https://code.claude.com/docs/en/skills)
- [How to Create Custom Skills - Claude Help Center](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills)
- [Using Skills in Claude - Claude Help Center](https://support.claude.com/en/articles/12512180-using-skills-in-claude)

### 深入分析
- [Inside Claude Code Skills: Structure, Prompts, Invocation](https://mikhail.io/2025/10/claude-code-skills/)
- [Claude Agent Skills: A First Principles Deep Dive](https://leehanchung.github.io/blogs/2025/10/26/claude-skills-deep-dive/)
- [Claude Code Customization Guide](https://alexop.dev/posts/claude-code-customization-guide-claudemd-skills-subagents/)

### 可靠性改善
- [How to Make Claude Code Skills Activate Reliably](https://scottspence.com/posts/how-to-make-claude-code-skills-activate-reliably)

### 工具與資源
- [Claude Code Skill Factory](https://github.com/alirezarezvani/claude-code-skill-factory)
- [Skill Builder](https://github.com/metaskills/skill-builder)
- [Claude Skills Library Guide](https://gist.github.com/alirezarezvani/a0f6e0a984d4a4adc4842bbe124c5935)
- [Skills Marketplace](https://skillsmp.com/)

### 教學
- [Claude Skills Tutorial: Give Your AI Superpowers](https://www.siddharthbharath.com/claude-skills/)
- [How to Build Claude Skills - Codecademy](https://www.codecademy.com/article/how-to-build-claude-skills)

---

## 總結

Claude Code Skills 是一個強大的擴展機制，允許你為 Claude 添加專門的知識和工作流程。關鍵要點：

1. **結構簡單**：只需 SKILL.md 即可開始
2. **自動觸發**：Claude 根據 description 判斷何時使用
3. **漸進式揭露**：保持核心精簡，詳細內容按需載入
4. **工具限制**：使用 `allowed-tools` 提高安全性
5. **現有工具**：可使用 Skill Factory 等工具加速開發

建議從簡單的 Skill 開始，逐步增加複雜度，並根據實際使用情況調整 description 以提高觸發準確性。
