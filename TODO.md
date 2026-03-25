# TODO

## In Progress

- [ ] 真實 Binance 資料 → InfluxDB → Grafana/Streamlit e2e 打通（2026-03-25）
- [ ] Lumibot 正式整合為回測主入口 — 改用 H1 資料餵入（2026-03-25）
- [ ] Grafana MVP 儀表板重新設計（精簡指標、去重複、4 行層次結構）（2026-03-25）
- [ ] 清除 InfluxDB 舊假資料（2026-03-25）

## Queued

- [ ] Streamlit 策略漂移偵測（回測 vs 實盤疊圖）
- [ ] Grafana 告警規則（MDD > 閾值、心跳失敗）
- [ ] MLflow 整合（Phase 1）
- [ ] 排程自動化（cron/Prefect）
- [ ] FastAPI skeleton
- [ ] Telegram 訊號推播正式版

## Done

- [x] Lumibot PoC 驗證（訊號一致率 100%）— 2026-03-24
- [x] Legacy 回測核心移除 — 2026-03-24
- [x] Grafana MVP 監控儀表板 — 2026-03-24
- [x] Streamlit 回測儀表板（含 token/plotly 修復）— 2026-03-25
- [x] 可擴展績效指標模組（metrics.py）— 2026-03-25
- [x] TrendPullback BTC 策略核心 + runners — 2026-03-25
- [x] Binance 真實資料抓取模組 — 2026-03-25
- [x] Telegram adapter（feature flag）— 2026-03-25
- [x] CI 分流（core / tw-live）— 2026-03-24
- [x] pyproject.toml 依賴分層 — 2026-03-24
- [x] Agent 編排定案（single truth AGENTS.md）— 2026-03-25
- [x] Workspace 基礎設定整合與精簡化 — 2026-03-25
- [x] Memory P0/P1/P2 分級 + 錯誤學習機制 — 2026-03-25
