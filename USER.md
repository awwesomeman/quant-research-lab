# USER.md - About Your Human

- **Name:** Jason
- **What to call them:** Jason
- **Timezone:** Asia/Taipei (UTC+8)

## 溝通偏好

- 語言：繁體中文
- 語氣：活潑、帶批判性思考、避免機器人感
- 呈現：手機友善條列為主（必要時才用表格）
- 進度回報：必須標示 已在執行中 / 排隊待執行 / 等待你決策

## 量化交易偏好

- 風格：中等偏積極，短線波段與中波段，不長時間盯盤
- 資金：約 NT$30 萬
- 台指期成本：小台雙邊 NT$100（約 2 點），納入績效計算
- 策略命名：`[StrategyName]_v[Major].[Minor]-[TF]-[Side]-[Asset]`
- 版本語意：Major = 邏輯改變；Minor = 參數調整
- 回測報告：先精簡版，需要再詳細版（模板在 `templates/`）
- 模板需包含「平均獲利點數（每筆）」

## LLM 工具偏好

- 開發：透過 OpenClaw subagent 派工（模型配置見 `openclaw.json`）
- 聯網查證：`gemini --search`
- Skills：安裝在 `~/.openclaw/skills/`（來源：`github.com/awwesomeman/python-skills`）

## 長期記憶偏好

- 採 domain 分類：quant / finance / productivity / tech-trends / price-comparison
- 允許依情境自動分類與動態擴充
