---
name: workflow-consistency
description: Enforce consistent daily workflow outputs for quant operations. Use when user asks to backtest a strategy, request TODO status, run robustness tests, or start strategy monitoring. Apply fixed templates (brief/full/robust/todo), enforce naming rules, require best-practice coding checks (code review + unit tests), and standardize response structure (執行中/待執行/等待決策).
---

# Workflow Consistency

回測、穩健性測試、監控策略的一致性標準。
通用規範（命名、回報格式、Agent 分工、TODO）見 workspace `AGENTS.md`。

---

## 回測流程規範

**樣本期一致性**：統一所有策略的 Train / Validation / OOS 期間，避免比較偏差。

允許微調的情況：
1. 資料源歷史長度限制
2. 交易日曆/商品上市時間差異
3. 已明確標註的特殊研究目的

微調須在回報中註記：調整原因 + 調整後期間。

**回測流程**：Train+Validation 選穩定參數 → OOS 單次驗證（不可以 OOS 結果反推）。

---

## 穩健性測試規範（必含）

1. Walk-forward 驗證
2. 穩定參數區間（鄰近參數績效一致性）
3. 成本壓測（不同滑價/手續費假設下仍正報酬）

---

## 監控策略回報格式

每次監控任務必回報：
- `setup/trigger`：進場條件是否仍滿足
- `state`：當前持倉狀態
- `log`：最近 N 筆訊號摘要
- `dedup`：去重機制是否正常

---

## Code Guardrails（量化策略專用）

1. 指標計算邏輯集中在 `metrics.py`（不在策略腳本內重算）
2. 策略 single truth：同一策略的回測與即時信號共用同一邏輯模組
3. 樣本期邊界：確認 indicator warmup 期已排除，無 look-ahead bias
4. 成本模型：手續費 + 滑價必須納入，台指期預設小台雙邊 NT$100

---

## Cleanup Suggestion Rule（強制）

優化或調整後，提供「刪除候選」清單：
1. Cache/build artifacts（`__pycache__`、`*.pyc`）
2. Runtime logs（可重建、非長期記錄）
3. 過時產出（已被新版取代）

先列候選 + 風險，確認後才刪。不刪策略 single truth 檔案或正式報告。
