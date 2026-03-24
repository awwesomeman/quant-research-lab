---
name: workflow-consistency
description: Enforce consistent daily workflow outputs for quant operations. Use when user asks to backtest a strategy, request TODO status, run robustness tests, or start strategy monitoring. Apply fixed templates (brief/full/robust/todo), enforce naming rules, require best-practice coding checks (code review + unit tests), and standardize response structure (執行中/待執行/等待決策).
---

# Workflow Consistency

Use this skill to keep outputs and process stable across repeated quant tasks.

Read `references/trigger-map.md` first when this skill triggers.

## Required Output Routing

- 回測分析：統一使用單一模板輸出（不再區分 brief/full），流程固定 Train+Validation 選穩定參數，再做 OOS 單次驗證。
- 穩健性測試：使用 robust 模板，必含 WF/穩定區/成本壓測。
- 待辦清單：固定 完成/進行中/待執行，且每項有簡單描述。
- 監控策略：固定回報 setup/trigger、state、log、去重機制。

## Research Period Consistency Rule

在策略研究階段，預設統一所有策略的 Train / Validation / OOS 期間，避免樣本期不一致造成比較偏差。

僅在以下情況允許微調：
1) 資料源歷史長度限制
2) 交易日曆/商品上市時間差異
3) 已明確標註的特殊研究目的

若有微調，回報中必須明確註記「調整原因 + 調整後期間」。

## Required Guardrails Before Finalizing Code

1. Keep naming consistent (file/variable/function naming style).
2. Keep logic DRY and modular (no duplicate business logic).
3. Run syntax check (`py_compile`) for changed Python files.
4. Run unit tests for core logic touched.
5. Report using three blocks:
   - 執行中
   - 待執行
   - 等待決策

## Naming Standard

Use strategy naming:
`[StrategyName]_v[L].[F].[P]-[TF]-[Side]-[Asset]`

- L: 邏輯改變
- F: 頻率/週期改變
- P: 參數改變

## LLM Tool Routing

Use tool selection by task type:
- 策略研究（research / hypothesis / experiment design）: prioritize Codex.
- 策略開發（implementation / refactor / production scripts）: prioritize Claude CLI.

## Claude CLI Model Routing (coding tasks)

When delegating coding tasks via Claude CLI, route by difficulty:
- Sonnet 4.6: default for routine coding, moderate refactor, standard script work.
- Opus 4.6: complex architecture changes, high-risk refactor, ambiguous/critical logic decisions.

Prefer Sonnet for speed; escalate to Opus when correctness/risk dominates.

## Completion Checklist

Before sending final response, confirm all:
- Required metrics present
- Best-practice checks completed (py_compile + unit test for core changes)
- New features include unit tests; add integration tests when interaction risk is non-trivial.
- TODO synchronized for new/changed commitments
- Response blocks use: 執行中 / 待執行 / 等待決策 (when status update is requested)

## Proactive Completion Rule (mandatory)

Do not wait for user to ask progress after a background task is started.
When a tracked task/session completes or fails, proactively send:
1) task status (completed/failed)
2) key outputs and file paths
3) validation/test result
4) next recommended action

If multiple tasks complete close together, send one batched update instead of silence.

## Cleanup Suggestion Rule (mandatory)

After each optimization or adjustment (code or documentation), provide a "deletion candidates" list to keep research workflow efficient.

Minimum categories:
1) Cache/build artifacts (e.g., `__pycache__`, `*.pyc`, temporary cache files)
2) Runtime logs (rebuildable and not required as long-term records)
3) Outdated outputs (superseded by newer canonical results)

Rule: list candidates + risk note first, then delete after confirmation (or when explicitly pre-approved). Do not delete strategy source-of-truth files or official reports.
