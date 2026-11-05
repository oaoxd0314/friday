# CLAUDE.md

This file provides guidance to Codex when working with code in this repository.

## Repository Type

This is an **Obsidian vault** for personal knowledge management, not a software codebase.

## Vault Structure

- `/daily` - 一天一個資料夾 `daily/YYYY/YYYY-MM-DD/`，入口是 `/daily` skill
  - `YYYY-MM-DD.md` - entry（Obsidian Calendar / Daily Notes 建的就是這個；habit、待辦、health 摘要 embed）
  - `health.md` - 體重 / 飲食 / 訓練（`health-log` skill 內部維護），config 在 `plan/Body Build/減脂計畫.md`，聚合視圖 `plan/Body Build/health.base`
  - `journal.md` - 日記（`journal` skill 內部維護）
- `/concept` - Curated, organized thoughts and ideas
- `/reference` - Detailed reference materials with explicit sources (e.g., book notes)
- `/random` - Unstructured inspiration and exploratory thinking
- `/assets` - Stored objects: templates, excalidraw drawings, images
- `/plan` - Scheduled plans and goals
- `/personality` - Claude 維護的使用者個性 / 價值觀 wiki（`about-me` skill 讀寫）

## Key Conventions

### Daily Notes
- Location: `daily/YYYY/YYYY-MM-DD/YYYY-MM-DD.md`（daily-notes format `YYYY/YYYY-MM-DD/YYYY-MM-DD`，folder `daily`）
- Template: `assets/template/daily template.md`
- Uses the Tasks plugin for todo management with queries
- Skill 分工：`daily`（入口 / 分流）→ `daily-note`（entry 檔與路徑）、`health-log`（health.md）、`journal`（journal.md）、`about-me`（個性 wiki）。使用者只會用 `/daily`，其他是內部模組

### Templates
- Stored in: `assets/template/`

### Installed Plugins
- Calendar
- Excalidraw
- Git (auto backup)
- Icon Folder
- List Callouts
- Tasks
- Zoom

## Language

Content is primarily in **Traditional Chinese (繁體中文)**.

## 關於使用者（Personalization）

使用者的個性 / 價值觀 wiki 放在 `personality/`，入口是 `personality/index.md`。

- **給個人化建議前**（職涯、金錢、學習方向、感情、AI/未來、時間與優先序等個人決策），先用 `about-me` skill batch-load 這份檔案，讓建議扣合他真實的取向。
- **收到關於他的新個人想法時**（尤其來自 `daily/YYYY/YYYY-MM-DD/journal.md`），主動更新 `personality/topics/` 對應分頁，並在「來源」區用 wikilink 標明是哪一天的 journal。維護規則見 `personality/index.md`。
- 語氣：不要無條件奉承；建議直接、對等、可被反駁。
