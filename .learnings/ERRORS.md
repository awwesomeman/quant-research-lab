# ERRORS.md — 錯誤學習追蹤

同類錯誤出現 3+ 次 → 提升到 MEMORY.md（P0）或更新 AGENTS.md 規則。

---

## 格式

```
### [日期] 錯誤簡述
- 原因：
- 修正：
- 防呆：（已加規則 / 待加）
- 出現次數：1
```

---

### 2026-03-25 Streamlit ImportError（SCHEMA_VERSION）
- 原因：`nautilus_lab/contracts/` 目錄名撞 Python module `nautilus_lab.contracts`
- 修正：rename 為 `nautilus_lab/schemas/`
- 防呆：AGENTS.md 已加「交付前 functional smoke」規則
- 出現次數：1

### 2026-03-25 Streamlit Missing INFLUX_TOKEN
- 原因：啟動時無環境變數也無 fallback token
- 修正：加 `_load_token_from_env_file()` + default fallback
- 防呆：加測試 `test_get_cfg_*`
- 出現次數：1

### 2026-03-25 Streamlit plotly not installed
- 原因：`.venv` 缺 plotly 套件
- 修正：`pip install plotly`
- 防呆：應列入 pyproject.toml 依賴（待補）
- 出現次數：1
