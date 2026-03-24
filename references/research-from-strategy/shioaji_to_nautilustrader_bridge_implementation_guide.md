# Shioaji to NautilusTrader 實時數據橋接實作指南

本文件提供 Shioaji（永豐）API 接入 NautilusTrader 實時交易引擎的核心模組實作方向。此模組負責將台股與台指期的非標準數據流轉化為引擎可識別的 `TradeTick` 事件。

---

## 1) ShioajiDataClient 核心實作

### 給 AI Agent 的開發指令

請以此模板為基礎，實作一個健壯的 `ShioajiDataClient`，開發時請特別注意：

1. **異步安全**
   - 確保 Shioaji 回調不阻塞 Nautilus 事件循環（Event Loop）。

2. **多商品路由**
   - 優化 `on_quote` 分發邏輯，支援同時訂閱多個台指期合約或股票標的。

3. **異常處理**
   - 加入斷線自動重連（Reconnection）與登入狀態檢查。

---

## 參考實作代碼

```python
import shioaji as sj
from typing import Dict

from nautilus_trader.live.data_client import LiveDataClient
from nautilus_trader.model.data.tick import TradeTick
from nautilus_trader.model.enums import ComponentState
from nautilus_trader.model.identifiers import InstrumentId
from nautilus_trader.model.objects import Price, Quantity


class ShioajiDataClient(LiveDataClient):
    """
    將 Shioaji API 封裝為 NautilusTrader 標準實時數據客戶端。
    """

    def __init__(self, api_key: str, secret_key: str, **kwargs):
        super().__init__(**kwargs)
        self._api = sj.Shioaji()
        self._api_key = api_key
        self._secret_key = secret_key

        # 建立映射表，用於將 Shioaji 訂閱代碼對應回 Nautilus InstrumentId
        self._symbol_map: Dict[str, InstrumentId] = {}

    def _connect(self):
        """執行永豐 API 登入並初始化行情訂閱器"""
        self._api.login(
            api_key=self._api_key,
            secret_key=self._secret_key,
            subscribe_trade=True,
        )

        # 註冊全局回調函數（避免在 subscribe 內重複定義）
        self._api.on_quote(self._handle_sj_quote)

        self.set_state(ComponentState.CONNECTED)
        self.log.info("Successfully connected to Shioaji API")

    def subscribe_ticks(self, instrument_id: InstrumentId):
        """訂閱指定商品的逐筆成交數據（Tick）"""
        sj_symbol = self._map_to_sj_symbol(instrument_id)
        self._symbol_map[sj_symbol] = instrument_id

        # 取得合約物件並訂閱
        contract = self._api.Contracts.Futures[sj_symbol] or self._api.Contracts.Stocks[sj_symbol]
        self._api.quote.subscribe(contract, quote_type=sj.constant.QuoteType.Tick)

        self.log.info(f"Subscribed to ticks for {instrument_id}")

    def _handle_sj_quote(self, exchange: str, quote: dict):
        """數據轉換核心：將 Shioaji dict 映射至 Nautilus TradeTick"""
        sj_code = quote.get("code")
        if sj_code not in self._symbol_map:
            return

        instrument_id = self._symbol_map[sj_code]

        # 封裝為 NautilusTrader 官方 TradeTick 格式
        tick = TradeTick(
            instrument_id=instrument_id,
            ts_event=self.clock.timestamp_ns(),  # 建議改為解析 quote 內交易所時間
            price=Price.from_str(str(quote["close"])),
            size=Quantity.from_int(int(quote["volume"])),
        )

        # 發布數據至 MessageBus，驅動策略引擎與存儲 Actor
        self.publish_data(tick)

    def _map_to_sj_symbol(self, instrument_id: InstrumentId) -> str:
        """ID 轉換邏輯（例如：TXF.TW -> TXF）"""
        return instrument_id.symbol.value

    def _disconnect(self):
        self._api.logout()
        self.set_state(ComponentState.DISCONNECTED)
```

---

## 2) 本地監控：Grafana + InfluxDB 數據流設計

為了達成「零框架費用」且「高性能視覺化」，系統採用異步解耦設計。

### 為什麼選擇此方案

1. **無感監控（Zero-Overhead）**
   - NautilusTrader 專注交易執行。
   - 數據由獨立 `InfluxDBActor` 在後台異步推送。
   - 即使監控面板高頻刷新，也不影響策略延遲。

2. **AI 易於維護**
   - AI Agent 只需維護獨立 Actor。
   - 不需修改核心交易邏輯。

3. **多維度分析**
   - Grafana 可並排顯示：
     - 台指期 Tick 圖
     - 加密貨幣深度圖
     - AI 預測機率分布

---

## 3) 實作備註（後續建議）

- `ts_event` 建議優先使用交易所原始時間戳，避免本機時間偏差。
- `contract` 取得建議拆分 Futures / Stocks 解析器，避免 `or` 混用造成例外未明確。
- 需補強：
  - 心跳檢查
  - 斷線重連 backoff
  - 訂閱恢復（re-subscribe）
  - 重複 Tick 去重
- 建議搭配本專案統一日誌 schema（setup/trigger/signal/skip）。
