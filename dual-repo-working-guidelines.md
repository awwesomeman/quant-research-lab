# 雙 Repo 工作規範（統一版）

> 本文件為唯一準則（single source of truth）。

## Repo 定位

### 1) `quant-strategy-lab`（平台/程式碼）
用途：可部署、可測試、可重現的工程資產。

包含：
- 策略程式碼（signals / execution / portfolio / risk）
- ETL、資料串接、監控、儀表板程式
- 測試（unit/integration/smoke）
- 部署設定與執行腳本
- 與正式執行相關的 schema / contracts

不包含：
- 長篇對話紀錄、臨時任務筆記
- 個人記憶檔（memory）
- 可重建實驗資料（cache/log/tmp）

---

### 2) `quant-research-lab`（研究脈絡/決策）
用途：可攜式研究上下文，支援環境前移與交接。

包含：
- `TODO.md`（任務狀態）
- `decisions/`（拍板決策）
- `discussions/`（重要討論摘要）
- `handover/`（交接與前移清單）
- 研究規範文件與工作原則（本文件）

不包含：
- 大型原始資料、回測中間檔
- 測試用程式碼與暫時腳本
- 可由 platform repo 重建之產物

---

## 提交流程（Mandatory）

1. **先決策、後開發**：涉及方向變更，先更新 `quant-research-lab/decisions/`。
2. **程式變更只進 platform**：所有可執行程式碼僅進 `quant-strategy-lab`。
3. **任務狀態即時更新**：每次任務狀態改變，同步更新 `quant-research-lab/TODO.md`。
4. **重大完成需雙寫**：
   - `quant-strategy-lab`：commit code
   - `quant-research-lab`：記錄成果、風險、下一步

---

## 分類判斷規則（遇到模糊項目時）

- 問題 1：這個檔案是否「直接影響執行結果」？
  - 是 → `quant-strategy-lab`
- 問題 2：這個檔案是否「主要用來理解脈絡或交接」？
  - 是 → `quant-research-lab`
- 問題 3：是否可由程式重建？
  - 可重建 → 不進 research repo

---

## 分支與訊息規範

- 兩個 repo 預設 `main`，必要時用短生命週期 feature branch。
- commit message 建議：
  - platform：`feat/fix/refactor/test/chore(scope): ...`
  - research：`docs/decision/chore(scope): ...`

---

## 最小同步清單（每次交接前）

- [ ] `quant-research-lab/TODO.md` 已更新
- [ ] 新決策已寫入 `decisions/`
- [ ] 重要討論已沉澱到 `discussions/`（非逐字稿）
- [ ] `quant-strategy-lab` 程式碼與測試狀態可被他人接手

---

## 禁止事項

- 不把 `memory/`、`TODO.md`、`USER.md` 回灌到 `quant-strategy-lab`
- 不把 `.venv/`、`__pycache__/`、log、cache 推到任一正式 repo
- 不在 research repo 放「唯一真實程式碼來源」

---

## 維護責任

- 本文件放在 `quant-research-lab` 統一管理。
- 規範有變更時，先在 `decisions/` 留一筆決策，再更新本文件。
