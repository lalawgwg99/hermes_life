# hermes_dreaming

你的個人知識腦（Brain Repo）。
這個 repo 的定位很簡單：

- `L0 真相來源`：所有最終結論以這裡的 Markdown 為準
- `Human edits always win`：人類修改永遠優先
- 代理/自動化只能補充證據、整理結構、追加時間線

如果你只看一段：
把新資訊先丟 `inbox/`，再整理到實體頁（`people/companies/ideas`），每個新事實都附來源。

## 這個 repo 解決什麼問題

一般筆記最大問題不是「寫不下」，而是：
- 資訊散落，找不到
- 結論和證據混在一起
- 新資料進來後，舊結論沒更新

這套方法把知識拆成兩層：
- `Compiled Truth`：目前最可信的結論（可被新證據修正）
- `Timeline (append-only)`：證據事件流（只追加，不覆寫）

你會同時得到：
- 可讀的當前結論
- 可追溯的證據歷史

## 1 分鐘上手

1. 丟新資訊到 `inbox/`（會議、連結、語音摘要、想法都可以）
2. 建立或更新對應實體頁（`people/companies/ideas`）
3. 把事件追加到 `Timeline`，並附 `source`
4. 跑驗證：
```bash
bash scripts/validate_brain.sh
```
5. 提交 git

## 目錄結構（你該放哪裡）

- `people/`：人物頁（一人一檔）
- `companies/`：公司頁（一公司一檔）
- `ideas/`：概念、論點、假說
- `meetings/`：會議紀錄、逐字稿
- `media/`：文章/書/影片/Podcast 摘要
- `originals/`：你的原創產出
- `inbox/`：未分類輸入（先收再整理）
- `templates/`：標準頁面模板
- `meta/`：規則、流程、夢循環、整合文件

## 頁面格式（非常重要）

`people/companies/ideas` 的頁面都必須有：

1. YAML frontmatter
2. `## Compiled Truth`
3. `## Timeline (append-only)`
4. Timeline 每一條都要有 `(source: ...)`

範例（company）：

```md
---
type: company
updated_at: 2026-04-11
tags:
  - ai
  - infra
---

# Example Corp

## Compiled Truth
- What it does: ...
- Stage: ...
- Notes: ...

## Timeline (append-only)
- 2026-04-11 — Closed seed round. (source: meeting | Partner Sync | 2026-04-11)
```

## 寫入規則（代理與人都適用）

- 先讀再寫：先看既有頁，避免重複/衝突
- 證據優先：沒有來源就不要改 Compiled Truth
- 衝突處理：遇到矛盾，先標記待確認，再附來源
- 保守關聯：不確定關係不要硬連結
- 禁止覆蓋人類判斷：只能提出修正建議或追加證據

## 日常流程（建議）

1. 白天：快速收集到 `inbox/`
2. 晚上：整理 `inbox/` 到實體頁
3. 每次整理都跑 `validate_brain.sh`
4. 每週一次：看 `meta/dreams/` 和 inbox 積壓，做清理與補標籤

## Quality Gate（已內建）

本 repo 已有機器把關：

- 本地：`bash scripts/validate_brain.sh`
- CI：`.github/workflows/brain-guard.yml`

目前會檢查：
- 是否有 `Compiled Truth` / `Timeline (append-only)`
- Timeline 事件是否包含 `source`
- frontmatter 是否完整：`type`、`updated_at(YYYY-MM-DD)`、`tags(list)`
- `type` 是否符合資料夾（person/company/idea）

## gbrain 整合（可選，但建議）

這個 repo 是 L0（真相來源），`gbrain` 是 L1（檢索與向量索引）。

### 安裝與初始化

```bash
bash scripts/setup_gbrain.sh
gbrain init --supabase
```

### 匯入與建索引

```bash
gbrain import "/Users/jazzxx/hermes_dreaming" --no-embed
gbrain embed --stale
```

### 查詢驗證

```bash
gbrain query "what changed recently in companies"
```

### 每日自動同步（nightly）

```bash
bash scripts/setup_gbrain_nightly_cron.sh
```

預設每天 02:30 執行：
- `gbrain sync --repo ...`
- `gbrain embed --stale`

日誌位置：`meta/logs/gbrain-nightly.log`

更多細節看：`meta/GBRAIN_INTEGRATION.md`

## 常見問題（FAQ）

1. 為什麼要 Timeline append-only？
- 因為知識會演化，證據歷史不能消失。結論可改，證據要留。

2. 沒有來源可以先寫嗎？
- 可以先寫在 `inbox/` 或 timeline 註記「待確認」，不要直接改 Compiled Truth。

3. external 資料能不能直接改結論？
- 不建議。先落地到 timeline 或 inbox，再評估是否提升到 Compiled Truth。

4. 這個 repo 跟 AI agent memory 有什麼差異？
- 這裡存的是世界知識與長期脈絡；agent memory 偏操作偏好與執行狀態。

## 推薦閱讀順序

1. `meta/SIMPLIFIED_MODE.md`（先理解簡化版）
2. `meta/WORKFLOW.md`（日常實作）
3. `meta/AGENT_RULES.md`（代理寫入規範）
4. `meta/MIXED_MODE.md`（混合模式）
5. `meta/GBRAIN_INTEGRATION.md`（檢索層整合）
6. `meta/MATURITY_ROADMAP.md`（下一步升級）

## 專案現況

- 已有結構驗證與 CI gate
- 已可接 gbrain
- 已有 nightly cron 腳本
- 下一階段重點：dead-link 檢查、重複實體檢查、confidence 機器驗證
