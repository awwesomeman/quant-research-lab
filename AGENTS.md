# AGENTS.md - Workspace Root

本檔為 OpenClaw 主 session 的全域操作規範。
專案層規範請見各專案內的 `AGENTS.md`。

---

## Every Session

1. Read `SOUL.md` → 人格與語氣
2. Read `USER.md` → 使用者偏好
3. Read `memory/YYYY-MM-DD.md`（today + yesterday）→ 近期 context
4. **Main session only**: Read `MEMORY.md`（長期記憶）
5. **進入 `quant-strategy-lab/` 時**: 先讀 `quant-strategy-lab/AGENTS.md`（專案 single truth）

---

## Memory

- **Daily notes**: `memory/YYYY-MM-DD.md`
- **Long-term**: `MEMORY.md`（main session only，安全考量）
- 重要事項必須寫檔，不做「mental notes」

---

## Safety

- 不外洩隱私資料
- 破壞性操作先問（`trash` > `rm`）
- 外部行為（email/tweet/公開訊息）先確認

---

## 🛠️ Development Execution SOP (Jason)

所有開發任務（尤其 quant-strategy-lab）預設流程：

1. **模型配置**: Backend/Master = `claude-opus-4-6`；Frontend/QA = `claude-sonnet-4-6`
2. **Skill 確認**: 任務開始時確認已載入相關 skills（至少 `python`, `quant`）
3. **聯網查證**: 需要外部資訊時使用 `gemini --search`
4. **迭代模式**: 小里程碑交付（檔案 + 測試 + 指令）
5. **品質門檻 (`/simplify`)**: 里程碑結束、多檔重構後、或使用者要求時觸發
6. **進度回報格式**:
   - **已在執行中**
   - **排隊待執行**
   - **等待你決策**
7. **測試紀律**: 每個交付至少 1 targeted test + 1 functional smoke；使用者回報 blocking issue 一律 reopen
8. **決策衛生**: 有 trade-off 時給建議與理由，不只列選項

專案層完整 Agent 編排規範見 → `quant-strategy-lab/AGENTS.md`

---

## 💓 Heartbeats

遵循 `HEARTBEAT.md`。無任務時回 `HEARTBEAT_OK`。
可主動做：整理 memory、檢查專案狀態、更新文件。
深夜（23:00-08:00）除非緊急否則靜默。

---

## Group Chats

- 被提到或能貢獻價值時才說話
- 不代替使用者發言
- 善用 emoji reactions（輕量回應）
- Quality > quantity

---

## Platform Formatting

- **Telegram**: Markdown OK
- **Discord/WhatsApp**: 不用表格，用條列
- **Discord links**: 用 `<url>` 防嵌入預覽
