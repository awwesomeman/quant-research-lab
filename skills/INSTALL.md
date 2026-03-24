# Skills 安裝 SOP（OpenClaw / Claude / Gemini）

> 目標：用同一套來源，一鍵安裝到多個工具。

## 0) 先安裝第三方 skills 套件（python-skills）

```bash
cd ~
git clone https://github.com/awwesomeman/python-skills.git
cd ~/python-skills
```

> 若已存在可先 `git pull` 更新。

---

## 1) 一鍵安裝到 OpenClaw / Claude / Gemini（全域路徑）

```bash
cd ~/python-skills
bash install.sh openclaw claude gemini
```

### 驗證

```bash
ls -la ~/.openclaw/skills
ls -la ~/.claude/skills
ls -la ~/.gemini/skills
```

---

## 2) 只安裝指定分類（例如 quant + python）

```bash
cd ~/python-skills
bash install.sh --skills "quant,python" openclaw claude gemini
```

---

## 3) 安裝到目前專案的本地路徑（非全域）

```bash
cd ~/python-skills
bash install.sh --local openclaw claude gemini
```

> 本地模式會裝到當前專案下各工具的本地 skills 目錄。

---

## 4) 解除安裝（對應同樣參數）

```bash
cd ~/python-skills
bash uninstall.sh openclaw claude gemini
```

指定分類解除：

```bash
cd ~/python-skills
bash uninstall.sh --skills "quant,python" openclaw claude gemini
```

---

## 5) 本專案自定義 skills（quant-research-lab/skills）

本 repo 追蹤的是「自定義規範型 skills」：
- `workflow-consistency/`
- `artifact-lifecycle-manager/`

建議流程：
1. 在 `quant-research-lab/skills/` 更新 SKILL 檔案
2. commit + push 到 `quant-research-lab`
3. 視需要同步到運行環境（`~/.openclaw/workspace/skills/`）

---

## 6) 常見問題

### Q1: `Key is already in use`
- 若使用 GitHub Deploy Key，同一把 key 不能重複綁不同 repo。
- 解法：為每個 repo 建獨立 deploy key，或改用帳號層級 SSH key。

### Q2: 改了 SKILL.md 要不要重裝？
- 若是既有 skill 內容修改（同路徑），通常不用重跑 install。
- 若新增/刪除 skill 目錄，建議重跑 `install.sh`。
