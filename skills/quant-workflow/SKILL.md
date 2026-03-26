---
name: quant-workflow
description: Enforce consistent quant development workflow for quant-strategy-lab. Use when user asks to backtest a strategy, run robustness tests, or start strategy monitoring.
---

# quant-strategy-lab Workflow Conventions

通用量化/Python 規範見 `python` 和 `quant` skill，此處只記錄本專案專屬 convention。

---

## 觸發路由

| 使用者要求 | 執行規則 |
|-----------|---------|
| 回測/分析策略 | 見「回測流程」 |
| 穩健性/walk-forward/成本壓測 | 見「穩健性測試」 |
| 監控策略 | 見「監控回報格式」 |

---

## 回測流程

- **三段式**：Train + Validation 選穩定參數 → OOS 單次驗證（不可以 OOS 反推）
- **樣本期一致性**：統一所有策略的 Train/Val/OOS 期間；微調須註記原因
- **必含指標**：交易次數、勝率、平均報酬率（每筆）、PF、年化報酬、Sharpe、MDD
- **成本設定**：必標 `round_trip_cost=...`；台指期預設小台雙邊 NT$100（約 2 點）

---

## 穩健性測試（必含三項）

1. Walk-forward 驗證
2. 穩定參數區間（鄰近參數績效一致性）
3. 成本壓測（不同滑價/手續費假設下仍正報酬）

---

## 監控回報格式

每次監控任務必回報：
- `setup/trigger`：進場條件是否仍滿足
- `state`：當前持倉狀態
- `log`：最近 N 筆訊號摘要
- `dedup`：去重機制是否正常

---

## 回測效能建議

- **向量化預處理**：技術指標優先在 `initialize` 階段預算（Pandas），`on_trading_iteration` 只查表讀取，避免逐 bar 重算
- **效能卡點**：若回測明顯慢，優先檢查 `on_trading_iteration` 內的計算量是否可向量化

---

## 專案約定

- 策略 single truth：Lumibot 回測與即時訊號共用同一策略模組
- 指標計算集中在 `metrics.py`，不在策略腳本內重算
- 命名：`[StrategyName]_v[Major].[Minor]-[TF]-[Side]-[Asset]`（Major=邏輯改變、Minor=參數調整）
