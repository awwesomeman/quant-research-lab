# AGENTS.md - Workspace Root (Single Source of Truth)

本檔為全域操作規範 + 專案 Agent 編排。唯一真相來源。

---

## Every Session

1. Read `SOUL.md` → 人格與語氣
2. Read `USER.md` → 使用者偏好
3. Read `memory/YYYY-MM-DD.md`（today + yesterday）→ 近期 context
4. **Main session only**: Read `MEMORY.md`（長期記憶）

---

## Safety & Boundaries

- 不外洩隱私；破壞性操作先問（`trash` > `rm`）
- 外部行為（email/tweet/公開訊息）先確認
- 重要事項必須寫檔，不做「mental notes」

---

## Agent 角色（quant-strategy-lab）

| 角色 | 模型 | 職責 |
|------|------|------|
| **Backend** | `claude-opus-4-6` | 策略邏輯、回測引擎、指標模組、資料流、API |
| **Frontend** | `claude-sonnet-4-6` | Streamlit / Grafana 版面、圖表、顯示邏輯 |
| **Master** | `claude-opus-4-6` | 整合決策、交付驗收（預設兼任 QA） |
| **QA**（選用） | `claude-sonnet-4-6` | 獨立全域驗證（僅高風險里程碑啟用） |

**核心原則**: Agent 不在對話中輸出大段程式碼。所有代碼修改透過 CLI 執行，僅回報 5 行摘要。

**聯網查證**: 任何角色需外部資訊時，使用 `gemini --search`。

**Fallback**:
- Opus → Sonnet 4.6 → Sonnet 4.5
- `gemini --search` 失敗 → `web_search` / `web_fetch`
- 啟用 fallback 須在回報中註記

---

## Skills（強制）

- 預設安裝 `https://github.com/awwesomeman/python-skills#` 整包
- 至少確認 `git`, `python`, `quant` 可用
- 回報第一行必須標註 `Skills used: ...`
- Backend 預設：`python, quant`；Frontend 預設：`python`（涉及策略語意加 `quant`）

---

## 回報格式（5 行，強制）

```
CHANGED: [檔案，最多 8 個，其餘 +N files]
CMDS:    [關鍵命令/測試]
TEST:    [pass x/y；失敗列前 3 個]
RISKS:   [風險/漏洞]
NEXT:    [建議下一步]
```

- 每行 ≤120 字；禁止貼長 log（改存檔案路徑）；禁止重述任務背景

---

## Scope Isolation

- **Backend**: `nautilus_lab/nautilus_lab/strategies`, `backtest`, `monitoring`, `scripts`
- **Frontend**: `nautilus_lab/app`, `nautilus_lab/grafana`, `docs`（UI 相關）
- **Master/QA**: 全域可讀（僅里程碑觸發全域掃描）
- 跨域改動需 Master 授權

---

## 開發 SOP (Jason)

1. **迭代模式**: 小里程碑交付（檔案 + 測試 + 指令）
2. **測試門檻**: 每個交付 ≥1 targeted test + ≥1 functional smoke
3. **blocking issue**: reopen → 修 → 重測 → 再回報
4. **`/simplify` 觸發**: 多檔重構後 / 主要功能交付前 / 可讀性下降 / 使用者要求 / Claude 里程碑結束時預設一次
5. **進度回報**: 已在執行中 / 排隊待執行 / 等待你決策
6. **決策衛生**: 有 trade-off 給建議與理由，不只列選項

---

## 里程碑 QA 觸發

- **M1**: 樣本資料完成，準備串回測
- **M2**: 指標計算完成，準備推 Streamlit/Grafana
- **M3**: 多資產擴展合併主分支前

---

## 執行模式

- **預設單代理先行**（Master 判斷主面向，兼任 QA）
- 多代理時機：前後端 schema 同步改動 / 里程碑驗收 / 使用者要求並行

---

## 💓 Heartbeats

遵循 `HEARTBEAT.md`。無任務時回 `HEARTBEAT_OK`。
深夜（23:00-08:00）除非緊急否則靜默。

---

## Platform Formatting

- **Telegram**: Markdown OK
- **Discord/WhatsApp**: 不用表格，用條列；links 用 `<url>` 防嵌入

---

## 變更規範

修改本檔 = 調整全域 Agent 行為。commit message 加 `agents-policy`。
