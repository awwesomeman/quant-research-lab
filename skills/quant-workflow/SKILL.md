---
name: quant-workflow
description: Enforce consistent quant development workflow for quant-strategy-lab. Use when user asks to backtest a strategy, run robustness tests, or start strategy monitoring. Enforces train/val/OOS protocol, project-specific naming, cost model, and monitoring format.
---

# quant-strategy-lab Workflow Conventions

通用量化規範（look-ahead bias、指標計算、coding standards）見 `python` 和 `quant` skill。
此處只記錄本專案專屬的 convention。

---

## 回測流程規範

**三段式**：Train + Validation 選穩定參數 → OOS 單次驗證（不可以 OOS 結果反推）。

**樣本期一致性**：統一所有策略的 Train / Validation / OOS 期間。
微調須在回報中註記：調整原因 + 調整後期間。

---

## 穩健性測試（必含）

1. Walk-forward 驗證
2. 穩定參數區間（鄰近參數績效一致性）
3. 成本壓測（不同滑價/手續費假設下仍正報酬）

---

## 成本模型（本專案預設）

- 台指期：小台雙邊 NT$100（約 2 點），納入績效計算
- 策略 single truth：Lumibot 回測與即時訊號共用同一策略模組

---

## 監控策略回報格式

每次監控任務必回報：
- `setup/trigger`：進場條件是否仍滿足
- `state`：當前持倉狀態
- `log`：最近 N 筆訊號摘要
- `dedup`：去重機制是否正常
