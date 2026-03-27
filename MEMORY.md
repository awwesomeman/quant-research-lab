# MEMORY.md — Long-term Memory

長期記憶索引（主檔）。細節分流到 domain 檔案。

## Domain Index

- Quant Research: `memory/domains/quant.md`
- Finance Knowledge: `memory/domains/finance.md`
- Productivity: `memory/domains/productivity.md`
- Tech Trends: `memory/domains/tech-trends.md`
- Price Comparison: `memory/domains/price-comparison.md`

## Auto Classification Rule

- 依對話情境自動寫入最相關 domain
- 跨主題：主檔留摘要，分別寫入多個 domain
- 新情境出現時可動態新增 `memory/domains/<new-topic>.md`

---

## Events Timeline [P0]

- **2026-03-24** quant-strategy-lab 架構決策：Lumibot-first，legacy 回測已移除
- **2026-03-24** 建立 Grafana 監控儀表板 + Streamlit 回測儀表板
- **2026-03-25** Agent 編排定案：Backend/Master=Opus, Frontend/QA=Sonnet, Gemini=search only
- **2026-03-25** 兩 repo 推送 GitHub 成功（SSH config 修復）
- **2026-03-25** 基礎設定大整合：刪除重複檔案、單一 AGENTS.md、workspace 精簡化
- **2026-03-25** 導入 memory P0/P1/P2 分級 + 錯誤學習機制
- **2026-03-26** signal_engine pure function 抽出 + monitor pipeline 建立（Sprint 1-7）
- **2026-03-26** MarketAdapter / MarketHub 架構設計（decisions 記錄）
- **2026-03-26** Market Config 兩層架構（config/markets.yaml）
- **2026-03-27** TimescaleDB 完全取代 InfluxDB（Phase A-D）
- **2026-03-27** Grafana 三板架構定案：Backtest / Monitor / Live
- **2026-03-27** No-fallback 規範加入 AGENTS.md

---

## Infrastructure [P0]

- **VPS**: GCP `instance-20260302-070225` (asia-east1-b)
- **External IP**: 35.194.150.232
- **Services**: TimescaleDB(:5432), InfluxDB(:8086, 保留待退役), Grafana(:3000), Streamlit(:8502)
- **SSH**: 兩把 deploy key（research / strategy 各一）via `~/.ssh/config`
- **TimescaleDB**: 唯一資料源，DSN 從 `TIMESCALE_DSN` 環境變數讀
- **Grafana**: 三板（Backtest/Monitor/Live），TimescaleDB SQL datasource
- **Streamlit**: systemd service，auto-restart

---

## Tech Projects [P1]

### quant-strategy-lab
- **目錄**: `~/workspace/quant-strategy-lab`
- **Remote**: `git@github-quant-strategy:awwesomeman/quant-strategy-lab.git`
- **技術**: Lumibot + InfluxDB + Grafana + Streamlit
- **狀態**: Phase 0 進行中

### quant-research-lab
- **目錄**: `~/workspace`（workspace root）
- **Remote**: `git@github.com:awwesomeman/quant-research-lab.git`
- **狀態**: 基礎設定整理中
