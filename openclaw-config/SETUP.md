# OpenClaw Config Setup Guide

新環境部署時，照本文件設定 `~/.openclaw/` 下的檔案與 symlink。

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
├── memory/main.sqlite       ← 系統自動產生
├── logs/                    ← 系統自動產生
├── media/                   ← 系統自動產生
└── workspace/               ← git repo（quant-research-lab）
```

---

## Symlink 設定（實際檔案在 workspace，~/.openclaw 指過來）

```bash
# cron jobs
ln -sf ~/.openclaw/workspace/openclaw-config/cron/jobs.json ~/.openclaw/cron/jobs.json

# skills（已設定，確認即可）
# ~/.openclaw/skills/git → workspace/skills/python-skills/skills/git
# ~/.openclaw/skills/python → workspace/skills/python-skills/skills/python
# ~/.openclaw/skills/quant → workspace/skills/python-skills/skills/quant
# ~/.openclaw/skills/skill-creator → workspace/skills/python-skills/skills/skill-creator
```

---

## 敏感檔案（不進 repo，需手動設定）

| 檔案 | 說明 | 設定方式 |
|------|------|---------|
| `openclaw.json` | 核心設定 | 用 `openclaw-config/openclaw.template.json` 為模板，填入 token 後存為 `~/.openclaw/openclaw.json` |
| `agents/*/agent/auth-profiles.json` | 子代理 API 認證 | 手動建立，填入 Anthropic API token |
| `credentials/` | OAuth | `openclaw configure` 自動產生 |

---

## 新環境快速設定步驟

```bash
# 1) 安裝 OpenClaw
curl -fsSL https://openclaw.ai/install.sh | bash

# 2) Clone workspace
git clone <your-research-lab-repo> ~/.openclaw/workspace

# 3) 從模板建立 openclaw.json
cp ~/.openclaw/workspace/openclaw-config/openclaw.template.json ~/.openclaw/openclaw.json
# 手動填入 botToken, gateway.auth.token 等敏感值

# 4) 建立 symlink
ln -sf ~/.openclaw/workspace/openclaw-config/cron/jobs.json ~/.openclaw/cron/jobs.json

# 5) 安裝 skills
cd ~/.openclaw/workspace/skills
git clone https://github.com/awwesomeman/python-skills.git
# symlink 到 ~/.openclaw/skills/
for s in git python quant skill-creator; do
  ln -sf ~/.openclaw/workspace/skills/python-skills/skills/$s ~/.openclaw/skills/$s
done

# 6) 設定子代理認證（每個 agent 各一份）
mkdir -p ~/.openclaw/agents/backend/agent ~/.openclaw/agents/frontend/agent
# 建立 auth-profiles.json（填入 API token）

# 7) 啟動
openclaw gateway start
```

---

## 注意事項

- `openclaw.template.json` 是去敏版，**絕對不要把真正的 openclaw.json commit 到 repo**
- `auth-profiles.json` 含 API key，**不進 repo**
- `.gitignore` 應包含：`openclaw.json`、`auth-profiles.json`、`credentials/`
