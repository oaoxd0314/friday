---
name: daily-note
description: Internal module (Claude-invoked, not a command the user types) that owns the per-day folder daily/<YYYY>/<YYYY-MM-DD>/ and its entry file <YYYY-MM-DD>.md. Load it whenever you need today's date under the 04:00 rule, the path of the entry / health.md / journal.md, to create the entry from the daily template, to tick a habit checkbox, add or check a 本日待辦 item, list open items, or keep the ## 身體 embed in place. Also documents the Obsidian daily-notes / Calendar settings the folder layout depends on. Used by `daily`, `health-log`, `journal`.
---

# Daily Note（一天的容器）

負責「一天」這個容器：日期、資料夾、entry 檔與它的固定錨點。不碰 `health.md` / `journal.md` 的內容。

## 路徑合約（其他模組只認這裡）

- `today`：`date +%Y-%m-%d\ %H:%M` 取系統時間。**04:00 前算前一天**。不從對話猜日期。
- `day_dir` = `daily/<YYYY>/<YYYY-MM-DD>/`
- `entry` = `<day_dir>/<YYYY-MM-DD>.md` ← Obsidian Calendar / Daily Notes 建的就是這個檔
- `health` = `<day_dir>/health.md`（`health-log` 維護）
- `journal` = `<day_dir>/journal.md`（`journal` 維護）

連結寫法（basename 都叫 `health` / `journal`，wikilink **必須帶完整路徑**）：
- `[[daily/2026/2026-09-18/health|健康紀錄]]`、`[[daily/2026/2026-09-18/journal|日記]]`
- entry 裡的摘要 embed：`![[daily/2026/2026-09-18/health#今日]]`（只嵌「今日」區，不嵌整檔）
- 別處引用某天日記：`[[daily/2026/2026-06-12/journal|journal 2026-06-12]]`
- 引用某天 entry：`[[daily/2026/2026-09-15/2026-09-15|2026-09-15]]`

## Obsidian 設定（動路徑前先看這裡）

`.obsidian/daily-notes.json`：
```json
{ "folder": "daily", "format": "YYYY/YYYY-MM-DD/YYYY-MM-DD", "template": "assets/template/daily template" }
```
- Calendar 外掛用 daily-notes 設定，點日期就建 `daily/YYYY/YYYY-MM-DD/YYYY-MM-DD.md`，缺的資料夾會自動建。跨年不用改設定。
- entry 為什麼不叫 `index.md`：Calendar 只拿 format 最後一段對 **basename** 解析日期（`format.split("/").pop()`），檔名沒有日期整個月曆就壞掉；Tasks 查詢也靠 `{{query.file.filenameWithoutExtension}}` 當日期。
- 模板：`assets/template/daily template.md`。區塊順序固定：`## Daily`（habit）→ `## 本日待辦`（手寫項目 + tasks query）→ `## Yesterday Review`（前一天 journal `#review` 的 embed，隔天早上照著開始）→ `## 身體`（health 摘要 embed）→ `## Reference`。**embed 一律在 tasks 區塊之下、Reference 之上**，不放上半部。

## 操作

### `ensure_entry()`
`entry` 不存在 → `mkdir -p day_dir`，讀模板，手動展開後寫入：
- `{{date:FORMAT}}` → 該日期的 moment 格式（`YYYY`、`YYYY-MM-DD`…）
- `{{title}}` → `<YYYY-MM-DD>`；`{{time}}` → `HH:mm`
- `{{yesterday:FORMAT}}` / `{{tomorrow:FORMAT}}` → 前一天 / 後一天的日期（Daily Notes 核心外掛原生支援這兩個變數，模板的 `## Yesterday Review` embed 靠它）
存在就不動任何內容。

### `ensure_health_embed()`
確認 entry 有 `## 身體` 區塊且含 `![[<day_dir>health#今日]]`。沒有 `## 身體`（舊 entry）→ 在 `## Reference` 之前、tasks 區塊之後插入整個區塊。有區塊但 embed 是舊寫法 → 改成新寫法。

### `ensure_review_embed(day)`
確認 `day` 的 entry 有 `## Yesterday Review` 區塊且含 `![[daily/<Y>/<day-1>/journal#review]]`（指向**前一天**的 journal）。沒有區塊 → 在 tasks 區塊之後、`## 身體` 之前插入整塊；有區塊但路徑不對（例如 Obsidian 建的舊 entry）→ 改成正確路徑。`day` 的 entry 不存在 → 先 `ensure_entry(day)`。由 `journal` 收尾時以 `next_day` 呼叫；`daily` 開場也對 `today` 呼叫一次，確保 Calendar 建的 entry 有這塊。前一天 journal 沒有 `## review` 時 embed 會顯示找不到區塊，這是預期行為，不要為了補它去寫假日記。

### `mark_habit(label)`
在 `## Daily` 區塊找含 `label` 的 `- [ ]` 行改 `- [x]`（例 `label = "每日操練"`）。已勾就不動。

### `add_todo(text)`
在 `## 本日待辦` 標題與 ```` ```tasks ```` 之間 append `- [ ] text`，放在既有手寫項目之後。不加 `#task`、不加日期，這區是當日臨時待辦。

### `check_todo(text)`
在 `## 本日待辦` 手寫區模糊比對描述，`- [ ]` → `- [x]`。多個候選就只問一句。

### `list_open()`
回傳 `## Daily` 與 `## 本日待辦` 手寫區的未勾項目（含縮排子項），去掉 `!/@/~/$` 前綴與 wikilink 語法，給 `daily` 看板用。tasks query 區塊是外掛動態算的，不解析。

## 原則
- 讀 → 改 → 寫，每次一個操作，不批次重寫整檔。
- 不改 habit 文字、不改 tasks query，那是 使用者 手動維護的模板內容。
- 新增區塊或 embed 到 entry 一律插在 tasks 下方（使用者 明確要求）。
