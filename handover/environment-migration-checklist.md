# 環境前移 Checklist（精簡版）

## 必帶檔案
- `TODO.md`
- `decisions/*.md`
- `README.md`

## 新環境啟動後第一步
1. 先讀 `decisions/2026-03-core-decisions.md`
2. 再看 `TODO.md` 的「進行中 / 待執行」
3. 最後確認平台 repo 目前分支與最新 commit

## 不搬內容
- `data/` 下可重建資料
- `logs/`、`cache/`、`__pycache__/`
- 測試用臨時腳本、demo seed

## 建議同步策略
- 所有重大討論拍板後，24h 內更新一筆 decision
- TODO 每次任務狀態改變即時更新
