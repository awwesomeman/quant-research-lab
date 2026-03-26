# OpenClaw Config Setup Guide

新環境部署時照本文件設定。目錄結構說明見 workspace `TOOLS.md`。

---

## 架構概念

- **`openclaw.json`**：系統層設定（agent 定義、模型、頻道、認證）→ 不進 repo
- **`workspace/AGENTS.md`**：行為層規範（職責、回報格式、scope、SOP）→ 進 repo
- **兩者不重複**：模型/身份只在 `openclaw.json`，行為規則只在 `AGENTS.md`
- **子代理**：透過 `sessions_spawn(agentId=...)` 派工

---

## 敏感檔案（不進 repo）

| 檔案 | 設定方式 |
|------|---------|
| `openclaw.json` | 用 `openclaw-config/openclaw.template.json` 為模板，填入 token |
| `agents/*/agent/auth-profiles.json` | 手動建立，填入 Anthropic API token |
| `credentials/` | `openclaw configure` 自動產生 |

---

## 新環境快速設定

```bash
# 1) 安裝 OpenClaw
curl -fsSL https://openclaw.ai/install.sh | bash

# 2) Clone workspace
git clone <your-research-lab-repo> ~/.openclaw/workspace

# 3) 從模板建立 openclaw.json（填入敏感 token）
cp ~/.openclaw/workspace/openclaw-config/openclaw.template.json ~/.openclaw/openclaw.json

# 4) Symlink cron
ln -sf ~/.openclaw/workspace/openclaw-config/cron/jobs.json ~/.openclaw/cron/jobs.json

# 5) 安裝 skills
# 5a) 外部 skills（python-skills repo）
git clone https://github.com/awwesomeman/python-skills.git ~/.openclaw/repos/python-skills
for s in git python quant skill-creator; do
  ln -sf ~/.openclaw/repos/python-skills/skills/$s ~/.openclaw/skills/$s
done

# 5b) workspace 自訂 skills（quant-workflow、artifact-lifecycle-manager 等）
for s in ~/.openclaw/workspace/skills/*/; do
  name=$(basename "$s")
  # 跳過 README/INSTALL 等非 skill 目錄
  [ -f "$s/SKILL.md" ] && ln -sf "$s" ~/.openclaw/skills/"$name"
done

# 6) 子代理認證
mkdir -p ~/.openclaw/agents/backend/agent ~/.openclaw/agents/frontend/agent
# 每個資料夾建立 auth-profiles.json（填入 API token）

# 7) 啟動
openclaw gateway start
```

---

## 注意事項

- **絕對不要** commit `openclaw.json` 或 `auth-profiles.json`
- `.gitignore` 已排除上述檔案
- `~/.claude/skills/` 為 legacy，不需要維護
