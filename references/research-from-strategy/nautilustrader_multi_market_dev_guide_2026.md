# NautilusTrader 多市場交易系統開發指南（2026）

> 用途：提供後續「回測 / 績效 / 監控」大改造時的統一開發守則。
> 範圍：台股期貨（Shioaji）+ 加密貨幣（Binance），純本地、零雲端綁定。

---

## 1) 系統架構全景

- **核心引擎**：NautilusTrader（Rust/Python 混合）
- **數據接入**：Shioaji + Binance Adapter
- **數據中繼**：InfluxDB OSS（本地時序資料庫）
- **視覺化**：Grafana（即時儀表板）
- **績效分析**：Empyrical + Qbrick（自研）

架構型態：**異步事件驅動**（高頻資料處理穩定、低延遲）

---

## 2) AI 開發核心原則（強制）

### 2.1 Strict Type Hinting
- 所有 class / function 必須加 Python Type Hints。
- NautilusTrader 核心對型別一致性要求高。

### 2.2 Composition over Inheritance
- 優先用 **Actors/組件**擴展功能。
- 避免修改 NautilusTrader 核心原始碼。

### 2.3 Async-First
- 所有 I/O（API、DB 寫入、通知）採 `async/await`。
- 嚴禁阻塞主交易循環。

### 2.4 No Framework Lock-in
- 元件必須可在 localhost / Docker 運行。
- 不依賴付費雲端平台。

---

## 3) 數據接入層設計（Data Provider Bridge）

## 3.1 Shioaji 接入（台股/台指期）
建立 `ShioajiDataClient`：
- 繼承 `nautilus_trader.live.data_client.LiveDataClient`
- 即時將 Quote / Tick 封裝為 Nautilus 的 `TradeTick` 或 `Bar`
- 補齊台灣市場特性：
  - 開收盤限制
  - 內盤 / 外盤邏輯
  - 期貨結算日換月提醒

## 3.2 Binance 接入（加密）
- 使用原生 `nautilus_trader.adapters.binance.BinanceDataClient`
- 區分 SPOT / FUTURES 帳戶
- 全部統一使用 UTC

---

## 4) 策略模板（Hybrid Strategy）

目標：支援 ML 推理 + 規則過濾混合。

```python
from nautilus_trader.strategy import Strategy
from nautilus_trader.model.data import Bar
from nautilus_trader.model.objects import Quantity

class HybridStrategy(Strategy):
    """支援 ML 推理與傳統過濾條件"""

    def on_bar(self, bar: Bar) -> None:
        # 1) 特徵工程
        features = self.feature_engine.process(bar)

        # 2) 模型推理
        prediction = self.model.predict(features)

        # 3) 混合執行邏輯
        if prediction > 0.8 and bar.close > self.ema_filter:
            self.submit_order(
                symbol=bar.symbol,
                side="BUY",
                quantity=Quantity.from_int(1),
            )

        # 4) 訊號發布（供監控）
        self.publish_signal(prediction, bar.close)
```

---

## 5) 績效與監控整合

## 5.1 Empyrical 績效計算（ResultProcessor）
- 從 `AccountState` 抽取 Equity Curve
- 計算：
  - `empyrical.sharpe_ratio`
  - `empyrical.max_drawdown`
  - `empyrical.sortino_ratio`
- 輸出 `.parquet` 供長期比較

## 5.2 Grafana 即時視覺化（GrafanaActor）
- 異步推送到 InfluxDB：Price / Equity / Drawdown / Signals
- 儀表板建議：
  1. 即時權益曲線（多策略對比）
  2. 市場相關性熱圖（台股 vs 加密）
  3. 訊號散佈（K 線上標註買賣點與預測機率）

---

## 6) 給 AI Agent 的執行步驟

1. **環境初始化**
   - 建立 `pyproject.toml`
   - 安裝：`nautilus_trader`, `shioaji`, `ccxt`, `empyrical`, `influxdb-client`

2. **先做 Shioaji Bridge**
   - 先確保 Tick 能正確進入 Nautilus 總線

3. **建策略原型**
   - 可同時處理 TXF 與 BTCUSDT 的異步策略類

4. **整合監控 Actor**
   - 開發 `InfluxDBActor`
   - 目標推送延遲 < 50ms

5. **視覺化部署**
   - 用 `docker-compose.yaml` 啟動 Grafana + InfluxDB

---

## 7) 本地部署清單（零成本）

| 組件 | 工具 | 備註 |
|---|---|---|
| 交易引擎 | NautilusTrader | 高效能、純本地、Rust 核心 |
| 資料庫 | InfluxDB OSS | 開源版足夠儲存大量訊號 |
| 視覺化 | Grafana | 免費且可高度客製化 |
| 通知 | Telegram Bot | 用 `httpx` API 即可免費推播 |

---

## 8) 本專案落地補充（給本 workspace）

- 報告輸出：統一單一模板（不再 brief/full 分流）
- 版本命名：`[StrategyName]_v[L].[F].[P]-[TF]-[Side]-[Asset]`
- 多策略比較：預設統一 Train/Validation/OOS，例外需註記原因
- 回測儀表板：先聚焦績效主體，穩健性可獨立二階段設計
