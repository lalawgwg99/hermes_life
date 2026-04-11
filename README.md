# hermes_dreaming

一個「給人與 AI 共用」的知識作業系統。

不是筆記 app，不是聊天備份。  
目標是把你的工作資訊變成**可持續更新、可追溯、可被 AI 正確讀寫**的長期知識庫。

```
hermes_dreaming = Markdown 腦庫（真相來源） + 驗證引擎 + CLI 工具 + 自動化流程
```

## 它解決什麼問題

| 常見痛點 | hermes_dreaming 的做法 |
|---|---|
| 資訊進得來，長期找不到 | 固定結構：一個人一檔、一間公司一檔，全文搜尋 |
| 有結論但不知道來源 | Timeline append-only，每條事實附 `(source: ...)` |
| AI 能回答但不能累積記憶 | Git 版本控制 + 代理寫入規範，AI 可安全讀寫 |
| 建新頁很煩 | `brain add person "Jordan"` 一行建檔，自動套模板 |
| 不知道腦庫裡有什麼 | `brain status` 一覽全局 |

## 安裝

```bash
git clone https://github.com/lalawgwg99/hermes_dreaming.git
cd hermes_dreaming
python3 -m venv .venv && source .venv/bin/activate
pip install -e .
```

安裝後就有 `brain` 指令可用。

## 快速開始（5 分鐘）

### 1. 看 brain 狀態

```bash
$ brain status

Brain: hermes_dreaming
Files: 3
  companies: 3
Entities: 3
Timeline entries: 3
Top tags: top-revenue(3), retail(2), technology(1)

Recent:
  [company] Amazon (2026-04-11)
  [company] Walmart (2026-04-11)
```

### 2. 建一個人物頁

```bash
$ brain add person "Jordan Lee" --tags "bd,partner"

Created: people/Jordan_Lee.md
```

產生的檔案自動包含 frontmatter + Compiled Truth + Timeline 結構，直接可編輯。

### 3. 快速丟東西進 inbox

```bash
$ brain inbox "Jordan 要從 A 公司轉去 B 公司，下季啟動新產品"

Captured: inbox/2026-04-12_jordan_要從_a_公司轉去_b_公司.md
```

不確定歸類到哪？先丟 inbox，之後再整理。

### 4. 搜尋

```bash
$ brain search "retail"

  [companies] Amazon (score: 30)
    companies/Amazon.md
    > - retail
    > - Industry: Retail; Information technology
  [companies] Walmart (score: 30)
    companies/Walmart.md
    > - retail
    > - Industry: Retail
```

跨所有資料夾全文搜尋，按相關性排序。

### 5. 看某個實體的時間線

```bash
$ brain timeline Amazon

Amazon [company]
  - 2026-04-10 — Captured top-3 revenue ranking data. (source: link | https://en.wikipedia.org/wiki/... | 2026-04-10)
```

### 6. 驗證格式

```bash
$ brain validate

OK — 3 files, 0 errors
  (3 company)
```

確認所有 entity 的 frontmatter、結構、來源完整性。有錯會逐一列出。

### 7. 跑 Dream Cycle

```bash
$ brain dream

Dream Cycle — 2026-04-12
  Commits: 6
  Inbox items: 1
  Recent entities: 4
  Log: meta/dreams/2026-04-12.md
```

自動彙整最近 24 小時的 commit、inbox、entity 變更，輸出每日摘要到 `meta/dreams/`。

## CLI 完整參考

```
brain [-h] [--version] [--root ROOT]
      {validate,status,add,search,inbox,dream,timeline}
```

| 命令 | 說明 | 常用旗標 |
|---|---|---|
| `brain status` | 總覽：檔案數量、entity 統計、標籤分佈、近期活動 | `--json` |
| `brain validate` | 驗證 frontmatter、Compiled Truth/Timeline 結構、source 完整性 | `--json` |
| `brain add <type> <name>` | 從模板建立 entity | `--tags "a,b"` `--force` |
| `brain search <query>` | 全文搜尋，按相關性排序 | `--bucket people` `--json` |
| `brain inbox <text>` | 快速捕獲到 inbox/，自動加日期 | `--source "meeting"` |
| `brain timeline <entity>` | 顯示某 entity 的時間線 | `--json` |
| `brain dream` | 執行 Dream Cycle，生成每日摘要 | `--json` |

### `brain add` 支援的 entity 類型

```
person, company, idea, meeting, media, original, learning, health, finance
```

範例：

```bash
brain add person "Alice Chen" --tags "engineer,frontend"
brain add company "Acme Corp" --tags "ai,startup"
brain add idea "Shame-Driven Founders" --tags "thesis,psychology"
brain add meeting "Weekly Sync with Jordan"
```

### `--json` 輸出

所有命令都支援 `--json`，方便程式化串接：

```bash
$ brain validate --json | python -m json.tool

{
  "files_checked": 3,
  "total_errors": 0,
  "total_warnings": 0,
  "by_type": {"company": 3},
  "results": [...]
}
```

### `--root` 指定腦庫路徑

預設自動偵測（往上找 `meta/` + `templates/` 目錄），也可手動指定：

```bash
brain --root /path/to/another/brain status
```

## 資料模型

### Entity 結構

每個 entity（`people/`、`companies/`、`ideas/`）**必須**包含三個部分：

```md
---
type: company                  # 1. YAML frontmatter
updated_at: 2026-04-11         #    必填：type, updated_at, tags
tags:
  - ai
  - infra
---

# Example Co

## Compiled Truth                # 2. 當前結論（可被更新）
- What it does: AI 基礎設施
- Stage: Series B
- Key people: Jordan Lee (BD)

## Timeline (append-only)        # 3. 證據事件流（只追加）
- 2026-04-11 — Signed pilot with Acme. (source: meeting | Weekly Sync | 2026-04-11)
- 2026-04-10 — First contact via Jordan. (source: chat | Telegram | 2026-04-10)
```

### 為什麼分兩層

- **Compiled Truth**：你現在相信的結論。可以改、可以推翻。
- **Timeline**：發生過什麼事。只追加，不刪改。每條必須帶來源。

這樣做的好處：任何結論都能往下追溯「當初為什麼這麼寫」。

### 來源格式

```
(source: <類型> | <標題或連結> | <日期>)
```

範例：
- `(source: meeting | Weekly Sync | 2026-04-10)`
- `(source: chat | Telegram DM with Jordan | 2026-04-10)`
- `(source: link | https://example.com | 2026-04-10)`

可選加可信度：
- `(source: meeting | Weekly Sync | 2026-04-10 | confidence: confirmed)`

可信度分三級：
| 等級 | 意思 | 範例 |
|---|---|---|
| `confirmed` | 有原始文件或連結 | 官方公告、合約、錄音 |
| `probable` | 可靠二手來源 | 會議轉述、摘要 |
| `tentative` | 尚未確認 | 口頭印象、傳聞 |

## 資料夾說明

```
hermes_dreaming/
  people/          人物頁（一人一檔）
  companies/       公司頁（一公司一檔）
  ideas/           概念、論點、學習筆記
  meetings/        會議紀錄
  media/           文章、書、影片摘要
  originals/       原創想法與輸出
  inbox/           待整理輸入（先丟這裡）
  templates/       各類型的 Markdown 模板
  meta/            規則、流程、Dream Cycle 輸出
    dreams/        每日 Dream Cycle 摘要
    logs/          自動化日誌
  scripts/         Shell 腳本（gbrain 整合等）
  brain/           Python CLI 套件原始碼
  tests/           測試
```

## 核心原則

1. **Source of Truth 在這個 repo（L0）** — 這裡的 Markdown 是唯一正本
2. **Human edits always win** — AI 寫的東西人類可以覆蓋，反之不行
3. **先讀再寫** — 寫入前必須先讀現有內容，避免覆蓋
4. **Timeline 只追加** — 歷史不可改寫，只能往後加
5. **沒來源不升級為結論** — Timeline 裡的事實要有足夠來源，才能更新 Compiled Truth

## 每日工作流程

```
收到新資訊
    │
    ├── 不確定歸類 → brain inbox "..."
    │
    └── 知道歸類
          │
          ├── 新實體 → brain add person/company/idea "name"
          │
          └── 既有實體 → 編輯對應檔案
                │
                ├── Timeline 追加事件 + source
                │
                └── 有足夠證據 → 更新 Compiled Truth
                      │
                      └── brain validate → git commit
```

### 會議流程

1. **會前**：`brain search "Jordan"` 拉出歷史互動
2. **會後**：`brain add meeting "Weekly Sync"` 建會議紀錄，更新相關人物/公司的 Timeline
3. **追蹤**：在會議頁的 Follow-ups 勾選行動項目

### Dream Cycle（每日自動整理）

```bash
brain dream
```

做的事：
- 彙整最近 24 小時的 git commit
- 列出 inbox 裡未整理的項目
- 列出近期更新的 entity
- 輸出建議（例：幾個 inbox 待分類）
- 全部寫到 `meta/dreams/YYYY-MM-DD.md`

安全性：Dream Cycle **不會**修改任何 entity 的 Compiled Truth，只產生摘要。

## 品質保證

### 本地驗證

```bash
brain validate
```

會檢查：
- frontmatter 是否包含 `type`、`updated_at`（YYYY-MM-DD）、`tags`（非空 list）
- `type` 是否和所在資料夾匹配（`people/` → `person`、`companies/` → `company`、`ideas/` → `idea`）
- `## Compiled Truth` 區塊是否存在
- `## Timeline (append-only)` 區塊是否存在
- Timeline 每一行是否帶 `(source: ...)`
- 全局重複 entity 偵測（同名不同檔）

### CI

每次 push/PR 自動跑驗證 + 測試（`.github/workflows/brain-guard.yml`）。

## AI 代理讀寫規範

詳見 `meta/AGENT_RULES.md`。重點：

1. **先讀再寫** — 不讀就寫等於覆蓋
2. **Timeline 只追加** — 不改歷史
3. **新事實必須帶來源** — 沒來源就放 Timeline，不動 Compiled Truth
4. **不覆寫人類結論** — 有衝突時標記「待確認」
5. **寫入要保守** — 寧可漏寫，不要亂連結

## 混合模式（L0 / L1 / L2）

| 層級 | 說明 | 可信度 |
|---|---|---|
| L0 | 本 repo 的 Markdown | 最高（正本） |
| L1 | 可信外部來源（官方文件、資料庫） | 高 |
| L2 | 非官方來源（新聞、社群、口述） | 需確認 |

讀取順序：L0 → L1 → L2。  
寫入規則：外部資料先進 inbox 或 Timeline，L1/L0 來源才能更新 Compiled Truth。

詳見 `meta/MIXED_MODE.md`。

## gbrain 整合（可選）

gbrain 提供向量檢索能力（L1 層），檔案量大時再接。

```bash
# 一鍵設定
bash scripts/setup_gbrain.sh

# 日常同步
gbrain sync --repo . && gbrain embed --stale

# 查詢
gbrain query "what changed recently"
```

夜間自動同步：`bash scripts/setup_gbrain_nightly_cron.sh`

詳見 `meta/GBRAIN_INTEGRATION.md`。

## 專案結構

```
brain/              Python CLI 套件
  __init__.py       版本定義
  cli.py            CLI 入口（argparse）
  parser.py         Entity 解析器（frontmatter + sections + timeline）
  validate.py       驗證引擎
  templates.py      模板引擎
  search.py         全文搜尋
  status.py         狀態統計
  inbox.py          快速捕獲
  dream.py          Dream Cycle 實作
tests/              37 個測試
scripts/            Shell 腳本（gbrain、驗證）
templates/          9 種 entity 模板
meta/               規則與流程文件
```

## 重要文件索引

| 檔案 | 內容 |
|---|---|
| `meta/AGENT_RULES.md` | 代理讀寫規範（8 條規則） |
| `meta/WORKFLOW.md` | 每日工作流程 |
| `meta/MIXED_MODE.md` | L0/L1/L2 混合模式設計 |
| `meta/SOURCE_CONFIDENCE.md` | 來源格式與可信度定義 |
| `meta/DREAM_CYCLE.md` | Dream Cycle 規格 |
| `meta/GBRAIN_INTEGRATION.md` | gbrain 整合指南 |
| `meta/TELEGRAM_INTAKE.md` | Telegram 輸入規則（規劃中） |
| `meta/MATURITY_ROADMAP.md` | 成熟化路線圖 |
| `meta/SIMPLIFIED_MODE.md` | 最簡使用方式 |
