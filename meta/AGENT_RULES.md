# Agent Rules (Hermes/OpenClaw)

這份文件定義代理人如何讀寫 brain repo。
目標：可控、可追溯、可持續增長。

## 1) 必讀規則
1. 先讀再寫（read before write）
2. 編譯結論在上，證據時間線只追加（append-only）
3. 任何新事實都要附來源（source）
4. 不覆寫人類結論；有衝突時改寫為「待確認」並附來源
5. 寫入要保守，避免幻覺式關聯

## 2) 讀取流程（Read)
- 解析使用者問題
- 檢索：
  - 先查 people/companies/ideas 的相關頁
  - 再查 meetings/media/originals
  - 若找不到，才新增或提議新增

## 3) 寫入流程（Write)
- 每次寫入包含：
  - 實體頁面（人/公司/概念）更新
  - Timeline 追加：日期 + 事件 + 來源
- 編譯結論僅在「有來源」時更新
- 若來源不足：只追加 timeline，避免改 compiled truth

## 4) 來源格式（Source)
- 格式：source: <類型> | <標題/連結> | <日期>
- 範例：source: meeting | Weekly Sync | 2026-04-10
- 範例：source: chat | Telegram DM with JazzX DD | 2026-04-10
- 範例：source: link | https://example.com | 2026-04-10

## 5) 實體辨識（Entity Detection）
- 人名、公司、概念出現就記為候選實體
- 同名實體需標記不確定（"可能是…"）
- 關聯採保守策略：先用標籤或文字描述，不硬連結

## 6) Inbox 處理
- 所有未分類內容先進 inbox/
- Dream Cycle 或手動整理時再分類
- 分類後保留原始來源引用

## 7) 風險控管
- 不寫入敏感資料（密碼、金流、身分證件）
- 對外部系統的操作需先確認
- 批量寫入前先預覽變更

## 8) Dream Cycle 規則
- 夜間僅做整理、摘要、補實體、修引用
- 不進行大規模覆寫
- 所有輸出寫在 meta/dreams/YYYY-MM-DD.md
