# Mixed Mode Design (Hybrid Knowledge Mode)

結論：Brain Repo 是真相來源；外部資料只作「來源輸入」。

## 1) 資料分層
- L0: Brain Repo（真相來源）
- L1: 可信外部來源（官方網站、文件、資料庫）
- L2: 非官方來源（新聞、社群、口述、推測）

## 2) 讀取優先序
- 先讀 L0
- 缺口才查 L1/L2
- L2 只能當線索，不可直接變結論

## 3) 寫入規則
- 外部資料先進 inbox/ 或 timeline
- 必須附 source + confidence
- compiled truth 只由 L1 或 L0 佐證才能更新

## 4) 可信度規則
- confirmed：L1 或有原始文件
- probable：可靠轉述（L2）
- tentative：不確定訊息（L2）

## 5) 衝突處理
- 不覆寫既有結論
- 在 timeline 記錄「待確認」+ 來源
- 標記需要人工核對

## 6) 自動化邊界
- 自動只做：收集、摘要、分類建議
- 不做：直接改 compiled truth
- 寫回需人工確認

## 7) 檢索策略
- 問題 → 先查 L0
- L0 缺口 → 查 L1/L2 → 結果寫入 inbox
- 回到 L0 決定是否更新

## 8) 審計與追溯
- timeline append-only
- 每條新增事實都有來源與日期
- 可回溯「當時為何這麼寫」
