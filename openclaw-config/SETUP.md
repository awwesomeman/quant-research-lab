# OpenClaw Config Setup Guide

新環境部署時，照本文件設定 `~/.openclaw/` 下的檔案與 symlink。

---

## 架構概念

- **`openclaw.json`**：系統層設定（agent 定義、模型、頻道、認證）→ 不進 repo
- **`workspace/AGENTS.md`**：行為層規範（職責、回報格式、scope、SOP）→ 進 repo
- **兩者不重複**：模型/身份只在 `openclaw.json`，行為規則只在 `AGENTS.md`
- **子代理**：透過 `sessions_spawn(agentId=...)` 派工，不使用 Claude CLI

---

## 目錄結構

```
~/.openclaw/
├── openclaw.json            ← 核心設定（含敏感 token，不進 repo）
├── credentials/             ← OAuth token（不進 repo）
├── agents/
│   ├── main/                ← main agent（系統自動產生）
│   ├── backend/agent/
│   │   └── auth-profiles.json  ← 含 API token（不進 repo）
│   └── frontend/agent/
│       └── auth-profiles.json  ← 含 API token（不進 repo）
├── skills/                  ← symlink → workspace/skills/python-skills/skills/*
├── cron/
│   └── jobs.json            ← symlink → workspace/openclaw-config/cron/jobs.json
└── workspace/               ← git repo（quant-research-lab）
```

系統自動產生（不用管）：`memory/main.sqlite`、`logs/`、`media/`

---

## Symlink 設定

實際檔案放 workspace（repo 追蹤），`~/.openclaw/` 用 symlink 指過來。

```bash
# cron jobs
ln -sf ~/.openclaw/workspace/openclaw-config/cron/jobs.json ~/.openclaw/cron/jobs.json

# skills → workspace/skills/python-skills/skills/*
# （見 step 5）
```

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

# 5) 安裝 skills（唯一路徑：~/.openclaw/skills/）
git clone https://github.com/awwesomeman/python-skills.git ~/python-skills
for s in git python quant skill-creator; do
  ln -sf ~/python-skills/skills/$s ~/.openclaw/skills/$s
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
