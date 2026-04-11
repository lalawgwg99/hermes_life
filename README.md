# hermes_dreaming

`hermes_dreaming` 是一個「給人與 AI 共用」的知識作業系統。

它不是一般筆記 app，也不是聊天記錄備份。  
它的目標是把你的工作資訊變成可持續更新、可追溯、可被 AI 正確讀寫的長期知識庫。

## 這個產品到底是什麼

一句話：  
`hermes_dreaming = Markdown 腦庫（真相來源） + 規則 + 自動化流程`

你可以把它理解為：

- 一個 Git 管理的個人/團隊知識資料庫
- 一套固定資料模型（結論層 + 證據層）
- 一套代理可遵守的寫入規範（避免亂寫、覆蓋、幻覺）

## 它解決什麼問題

多數知識管理會卡在三件事：

1. 資訊進得來，但長期找不到
2. 有結論，但不知道證據來源
3. AI 可以回答，但不能可靠地「累積記憶」

`hermes_dreaming` 的解法：

- `Compiled Truth` 放「目前結論」
- `Timeline (append-only)` 放「證據事件流」
- 每條新事實都要帶 `source`
- 人類可直接編輯，且人類編輯永遠優先

## 誰適合用

- 要讓 AI 長期協作的人（創業者、投資、研究、BD、顧問）
- 有大量人物/公司/會議資訊需要持續更新的人
- 想要「可回溯」而不是「一次性摘要」的人

## 核心原則（很重要）

- Source of Truth 在這個 repo（L0）
- Human edits always win
- 先讀再寫（Read before write）
- Timeline 只追加，不回寫
- 沒來源不升級為結論

## 你每天怎麼用

1. 把新訊息放到 `inbox/`
2. 整理到對應實體頁（`people/`、`companies/`、`ideas/`）
3. 在 `Timeline` 追加事件與來源
4. 有足夠證據才更新 `Compiled Truth`
5. 跑驗證腳本後再提交

```bash
bash scripts/validate_brain.sh
```

## 第一個實際流程（5 分鐘）

假設你今天開完會拿到三個訊息：

- Jordan 要從 A 公司轉去 B 公司
- B 公司要在下季啟動新產品
- 資訊來源是今天會議紀錄

做法：

1. 先把原始筆記丟進 `inbox/2026-04-11_jordan_update.md`
2. 更新 `people/Jordan.md` 的 timeline（新增事件 + source）
3. 更新 `companies/B.md` 的 timeline（新增產品規劃事件 + source）
4. 若證據足夠，再更新對應頁的 `Compiled Truth`
5. 執行驗證並 commit

## 資料模型（你要遵守的格式）

`people/`、`companies/`、`ideas/` 的頁面必須包含：

1. YAML frontmatter
2. `## Compiled Truth`
3. `## Timeline (append-only)`
4. Timeline 每一行都帶 `(source: ...)`

範例：

```md
---
type: company
updated_at: 2026-04-11
tags:
  - ai
  - infra
---

# Example Co

## Compiled Truth
- What it does: ...
- Stage: ...

## Timeline (append-only)
- 2026-04-11 — Signed pilot with Acme. (source: meeting | Weekly Sync | 2026-04-11)
```

## 資料夾說明

- `people/`：人物頁（一人一檔）
- `companies/`：公司頁（一公司一檔）
- `ideas/`：概念與論點
- `meetings/`：會議紀錄/逐字稿
- `media/`：文章/書/影片摘要
- `originals/`：原創想法與輸出
- `inbox/`：待整理輸入
- `templates/`：模板
- `meta/`：規則、流程、整合文件

## 品質保證（Quality Gate）

- 本地驗證：`bash scripts/validate_brain.sh`
- CI 驗證：`.github/workflows/brain-guard.yml`

目前會檢查：

- frontmatter 是否完整（`type`、`updated_at`、`tags`）
- `Compiled Truth`/`Timeline` 區塊是否存在
- Timeline 條目是否帶 `source`

## 和 gbrain 的關係

- 這個 repo 是 L0（真相來源）
- gbrain 是 L1（搜尋/向量檢索層）

你可以先不用 gbrain，純 Markdown 也能運作。  
當檔案量變大再接 gbrain。

整合流程看這裡：

- `meta/GBRAIN_INTEGRATION.md`

## 重要文件（先看這些）

- `meta/SIMPLIFIED_MODE.md`：最短使用方式
- `meta/WORKFLOW.md`：日常流程
- `meta/AGENT_RULES.md`：代理讀寫規範
- `meta/MIXED_MODE.md`：混合模式（L0/L1）
- `meta/MATURITY_ROADMAP.md`：成熟化路線圖
