# friday

一個給 Claude Code 用的 Obsidian vault 模板：每日紀錄（日記 / 健康 / 待辦）由 skill 驅動，個性 wiki 讓建議越用越貼合自己。

## 怎麼開始

1. 用 Obsidian 開啟這個資料夾（community plugins 與主題已附上，第一次開啟要在設定裡信任並啟用）。
2. 填 `plan/Body Build/減脂計畫.md` frontmatter（體重、熱量區間、週課表）。
3. 改 `assets/template/daily template.md` 的 habit 清單。
4. 在 Claude Code 裡打 `/daily` 開始記錄。
5. `personality/` 會由 Claude 在日記收尾時自己維護，不用手動填。

## 結構

- `/daily/YYYY/YYYY-MM-DD/` — 一天一個資料夾：`YYYY-MM-DD.md`（entry）、`health.md`、`journal.md`
- `/concept` — 整理過的想法
- `/reference` — 有明確來源的參考資料
- `/random` — 靈感與零碎思考
- `/assets` — template / excalidraw / 圖片
- `/plan` — 排定的計畫；`plan/Body Build/` 是 health-log 的 config
- `/personality` — Claude 維護的個性 / 價值觀 wiki

## Skills（`.claude/skills/`）

| Skill | 用法 |
|---|---|
| `daily` | 唯一入口。`/daily` 開看板，之後直接丟體重 / 餐點 / 想法 / 待辦，「結束日記」收尾 |
| `daily:renew` | 跨日後以系統日期重開看板 |
| `daily-note` / `health-log` / `journal` / `about-me` | `daily` 的內部模組，不需手動呼叫 |
| `travel-plan` | 「幫我規劃 X 旅行」→ 在 `plan/` 建行程資料夾 + canvas |
| `find-flight` | 「幫我找機票」→ 分層搜尋策略 + 瀏覽器驗價 |
| `stock-analyze` | 「分析 台股 2330」→ 四面向分析寫進 `reference/stocks/` |
| `obsidian-markdown` / `obsidian-bases` / `obsidian-cli` | Obsidian 語法與 CLI 參考 |

## 客製化入口

- 每日 habit：`assets/template/daily template.md`
- 健康參數與課表：`plan/Body Build/減脂計畫.md`、`plan/Body Build/餐點速查.md`
- 預設出發機場：`.claude/skills/find-flight/SKILL.md`
- 語氣與個人化規則：`CLAUDE.md`
