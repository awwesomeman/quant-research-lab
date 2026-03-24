# 從 quant-strategy-lab 可前移到 quant-research-lab 的內容

## 已同步（本次）
- `TODO.md`
- `memory/2026-03-03.md`
- `memory/2026-03-05.md`
- `memory/2026-03-06.md`
- `USER.md`（作為研究協作背景）

## 建議在 quant-strategy-lab 後續移除/降權追蹤的類型
> 目的：讓 strategy repo 聚焦「可部署程式碼與基礎建設」。

1. 討論紀錄 / 對話型記憶
   - `memory/*.md`
2. 任務管理文件（非程式）
   - `TODO.md`
3. 長篇研究摘要（若非直接驅動部署）
   - 可改為放在 research repo，strategy repo 保留連結。

## 保留在 quant-strategy-lab 的內容
- 策略程式碼（`scripts/`, `nautilus_lab/`）
- ETL / 監控 / 儀表板程式
- 測試與 CI 相關檔案
- 部署與執行設定
