# TOOLS.md - Local Notes

## 目錄結構與用途

```
~/.openclaw/
├── workspace/               ← quant-research-lab（OpenClaw workspace）
│   └── quant-strategy-lab/  ← 主策略 repo
└── skills/                  ← 由 remote-install.sh 直接安裝
```

- `workspace/openclaw-config/`：部署工具包（不自動讀取，僅新環境設定時使用）
- skills 安裝：`curl -fsSL https://raw.githubusercontent.com/awwesomeman/python-skills/main/remote-install.sh | bash -s -- claude openclaw gemini`

## SSH

- `github.com` → key: `~/.ssh/id_ed25519_quant_research`（quant-research-lab）
- `github-quant-strategy` → key: `~/.ssh/id_ed25519_openclaw_quant`（quant-strategy-lab）

## Services（本機）

- InfluxDB: `http://localhost:8086`（token: see `quant_lab/deploy/.env.example`）
- Grafana: `http://localhost:3000`（admin/admin）
- Streamlit: port 8502（需手動啟動 `.venv/bin/python -m streamlit run ...`）

## 外網連結（需防火牆放行 tcp:3000,8502）

- Grafana: `http://35.194.150.232:3000/d/signal_monitoring_v0/quant-strategy-monitor`
- Streamlit: `http://35.194.150.232:8502`
