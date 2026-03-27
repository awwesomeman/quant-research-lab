# AGENTS.md - Workspace Root (Single Source of Truth)

本檔為全域操作規範 + 專案 Agent 行為編排。唯一真相來源。
模型配置見 `openclaw.json`（single truth for model/identity）。

---

## Every Session

1. Read `SOUL.md` → 人格與語氣
2. Read `USER.md` → 使用者偏好
3. Read `memory/YYYY-MM-DD.md`（today + yesterday）→ 近期 context
4. **Main session only**: Read `MEMORY.md`（長期記憶）

**啟動確認（強制）**：讀完以上檔案後，確認並遵守：
- Git push 規則：不主動 push，累積 ≥5 commits 時主動詢問
- No-fallback：連線失敗直接報錯，不做 fallback
- Sub-agent prompt：只給路徑 + 目標 + 驗收條件，禁止貼大段背景

---

## Safety & Boundaries

- 不外洩隱私；破壞性操作先問（`trash` > `rm`）
- 外部行為（email/tweet/公開訊息）先確認
- 重要事項必須寫檔，不做「mental notes」

**不使用 Fallback 機制**：所有資料源直連，連線失敗直接報錯，不做自動 fallback。保持程式碼單一路徑、易維護。

---

## Agent 角色（quant-strategy-lab）

模型與身份定義在 `openclaw.json`，此處只管行為規範。
子代理透過 `sessions_spawn(agentId=...)` 啟動，不要用 Claude CLI 直接跑。

| 角色 | 職責 |
|------|------|
| **Master**（main） | 整合決策、交付驗收、預設兼任 QA。關鍵時刻自動升級模型，完成後降回。 |
| **Backend** | 策略邏輯、回測引擎、指標模組、資料流、API |
| **Frontend** | Streamlit / Grafana 版面、圖表、顯示邏輯 |
| **QA**（選用） | 獨立全域驗證（僅高風險里程碑啟用） |

**核心原則**: Agent 不在對話中輸出大段程式碼。所有代碼修改透過工具執行，僅回報 5 行摘要。

**聯網查證**: 任何角色需外部資訊時，使用 `gemini --search`。失敗 → `web_search` / `web_fetch`。

**Fallback**: 啟用時須在回報中註記原模型、fallback 模型、原因。

**Sub-agent Prompt（Token 控制）**: 只給檔案路徑 + 目標行為 + 驗收條件。禁止貼整段背景。

---

## Skills（強制）

- 安裝路徑：`~/.openclaw/skills/`（唯一 skill 路徑，main + subagent 共用）
- 來源：`git@github.com:awwesomeman/python-skills.git`
- 至少確認 `git`, `python`, `quant` 可用
- 回報第一行必須標註 `Skills used: ... / simplify used: yes|no`
- Backend 預設：`python, quant`；Frontend 預設：`python`（涉及策略語意加 `quant`）

**安裝指令（新環境 / 重裝）：**
```bash
curl -fsSL https://raw.githubusercontent.com/awwesomeman/python-skills/main/remote-install.sh | bash -s -- claude openclaw gemini
```
- 預設安裝所有 skill
- 同時安裝到 claude / openclaw / gemini 全域路徑
- 不需要手動 clone repo，不需要 symlink

安裝方式見 `openclaw-config/SETUP.md`。

---

## 回報格式（7 行，強制）

```
META:    agent: [backend/frontend/qa/master] | skills: [used] | simplify: yes/no | tools: [gemini --search / none / ...]
STATUS:  ✅ completed / ⚠️ partial / ❌ failed
CHANGED: [檔案，最多 8 個，其餘 +N files]
CMDS:    [關鍵命令/測試]
TEST:    [pass x/y；失敗列前 3 個]
RISKS:   [風險/漏洞]
NEXT:    [建議下一步]
```

**觸發條件**：有新增/修改/刪除程式碼（.py/.json/.yaml 等）或執行測試時才主動回報。
純文件整理、目錄調整、rename 不需要觸發。

**完整回報存檔**：每次回報同步寫入 `workspace/logs/agent_runs.jsonl`（one JSON per line）。

**Master 轉述精簡版（4 行）**：
```
META:    agent: [backend/frontend/qa/master] | skills: ... | simplify: yes/no | tools: ...
STATUS:  ✅ completed / ⚠️ partial / ❌ failed
TEST:    pass x/y
NEXT:    [下一步]
```
CHANGED / CMDS / RISKS 存入 log，使用者需要時再查。

- 每行 ≤120 字；禁止貼長 log（改存檔案路徑）；禁止重述任務背景

---

## Scope Isolation

- **Backend**：禁止讀寫 UI / 儀表板相關檔案
- **Frontend**：禁止讀寫策略邏輯 / 回測引擎，只依賴已定義的 schema 與資料接口
- **Master/QA**：全域可讀（僅里程碑觸發全域掃描）
- 跨域改動需 Master 授權

---

## 開發 SOP (Jason)

1. **迭代模式**: 小里程碑交付（檔案 + 測試 + 指令）
2. **測試門檻**: 每個交付 ≥1 targeted test + ≥1 functional smoke
3. **blocking issue**: reopen → 修 → 重測 → 再回報
4. **`/simplify` 觸發**: 多檔重構後 / 主要功能交付前 / 可讀性下降 / 使用者要求 / 里程碑結束時預設一次
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

## 記憶管理

### Memory 分級
- **P0**（永久）：使用者偏好、基礎設施、核心模式、重大決策
- **P1**（定期review）：技術方案、專案狀態、帶日期的解法
- **P2**（30天過期）：實驗紀錄、臨時筆記、待驗證假設

### 自動 Memory Flush
- 每個里程碑（M1/M2/M3）完成時，Master 更新 `MEMORY.md` + `memory/YYYY-MM-DD.md`
- Heartbeat 時若距上次 memory 寫入 >24h，主動檢查是否有未記錄事項

### 錯誤學習
- 重要錯誤記錄到 `.learnings/ERRORS.md`
- 同類 3+ 次 → 提升到 MEMORY.md（P0）或更新 AGENTS.md
- 里程碑驗收前主動讀取 `.learnings/ERRORS.md`

---

## 💓 Heartbeats

遵循 `HEARTBEAT.md`。無任務時回 `HEARTBEAT_OK`。深夜靜默。

---

## 研究資源索引

- `research/`：研究性文件（按需讀取，不自動注入）
- `decisions/`：重大決策記錄

---

## TODO 管理

- 任務狀態變更時更新 `TODO.md`（In Progress / Queued / Done）

---

## Git Push 規則

- 不主動 push，只做 commit
- 使用者明確要求時才 push
- 累積 ≥5 個 commit 時主動詢問是否 push

---

## Platform Formatting

- **Telegram**: Markdown OK
- **Discord/WhatsApp**: 不用表格，用條列

---

## 變更規範

修改本檔 = 調整全域 Agent 行為。commit message 加 `agents-policy`。
