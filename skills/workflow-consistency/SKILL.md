---
name: workflow-consistency
description: Enforce consistent daily workflow outputs for quant operations. Use when user asks to backtest a strategy, request TODO status, run robustness tests, or start strategy monitoring. Apply fixed templates (brief/full/robust/todo), enforce naming rules, require best-practice coding checks (code review + unit tests), and standardize response structure (執行中/待執行/等待決策).
---

# Workflow Consistency

Use this skill to keep outputs and process stable across repeated quant tasks.

Read `references/trigger-map.md` first when this skill triggers.

## Required Output Routing

- 回測分析：統一模板輸出，流程固定 Train+Validation 選穩定參數，再做 OOS 單次驗證。
- 穩健性測試：使用 robust 模板，必含 WF/穩定區/成本壓測。
- 待辦清單：固定 完成/進行中/待執行，且每項有簡單描述。
- 監控策略：固定回報 setup/trigger、state、log、去重機制。

## Research Period Consistency Rule

預設統一所有策略的 Train / Validation / OOS 期間。

僅在以下情況允許微調：
1) 資料源歷史長度限制
2) 交易日曆/商品上市時間差異
3) 已明確標註的特殊研究目的

若有微調，回報中必須註記「調整原因 + 調整後期間」。

## Required Guardrails Before Finalizing Code

1. 命名一致（file/variable/function）。
2. 邏輯 DRY，不重複核心業務邏輯。
3. 變更的 Python 檔跑 `py_compile` 語法檢查。
4. 核心邏輯變更須有 unit test。
5. 回報格式三段：執行中 / 待執行 / 等待決策。

## Naming Standard

策略命名：`[StrategyName]_v[Major].[Minor]-[TF]-[Side]-[Asset]`

- Major：策略邏輯改變
- Minor：參數調整

## Agent Task Routing

子代理派工透過 `sessions_spawn(agentId=...)`，模型配置見 `openclaw.json`。

任務分流：
- 策略研究（research / hypothesis / experiment design）→ Backend agent
- 策略開發（implementation / refactor / production scripts）→ Backend agent
- 儀表板 / UI / 圖表 → Frontend agent
- 需要外部查證 → `gemini --search`

## Completion Checklist

回報前確認：
- 必要指標齊全
- 最佳實踐檢查完成（py_compile + unit test）
- 新功能有 unit test；互動風險高的加 integration test
- TODO 已同步
- 回報使用：執行中 / 待執行 / 等待決策

## Proactive Completion Rule（強制）

背景任務完成或失敗時，主動回報：
1) 任務狀態
2) 關鍵產出與檔案路徑
3) 驗證/測試結果
4) 建議下一步

多個任務同時完成 → 合併為一則更新。

## Cleanup Suggestion Rule（強制）

優化或調整後，提供「刪除候選」清單：
1) Cache/build artifacts（`__pycache__`、`*.pyc`）
2) Runtime logs（可重建、非長期記錄）
3) 過時產出（已被新版取代）

規則：先列候選 + 風險，確認後才刪。不刪策略 single truth 檔案或正式報告。
