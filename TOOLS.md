# TOOLS.md - Local Notes

## SSH

- `github.com` → key: `~/.ssh/id_ed25519_quant_research`（quant-research-lab）
- `github-quant-strategy` → key: `~/.ssh/id_ed25519_openclaw_quant`（quant-strategy-lab）

## Services（本機）

- InfluxDB: `http://localhost:8086`（token: see `nautilus_lab/deploy/.env.example`）
- Grafana: `http://localhost:3000`（admin/admin）
- Streamlit: port 8502（需手動啟動 `.venv/bin/python -m streamlit run ...`）

## 外網連結（需防火牆放行 tcp:3000,8502）

- Grafana: `http://35.194.150.232:3000/d/signal_monitoring_v0/quant-strategy-monitor`
- Streamlit: `http://35.194.150.232:8502`
