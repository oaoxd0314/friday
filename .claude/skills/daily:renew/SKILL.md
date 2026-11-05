---
name: daily:renew
description:使用者打 "/daily:renew" 時使用。開始指令：以現在系統日期重新開一個 daily 看板。只有一個防呆：若目前 session 已經開著同一天的 daily、且今天的檔都在，就不重開。不收尾、不寫舊日、沒有其他 side effect。
---

# Daily Renew（以現在日期重開）

> 開始指令，不是結束指令。只做「判斷 → 開場」。舊日的東西一律不碰，`/daily` 途中沒存的就是沒存。

## 流程

### 1. 判斷（不動任何檔）
1. `today`：載入 `daily-note`，跑 `date` 依 04:00 規則取現在日期。不用對話裡的日期。
2. `session_date`：本 context 最後一次 daily 看板的日期；沒有看板（例如剛 `/clear`）→ 無。
3. `exists`：`daily/<YYYY>/<today>/` 下 entry 與 `health.md` 都在。

### 2. 分支
- **`session_date == today` 且 `exists`** → 不重開。只回一行：`今天還是 <today>，daily 已在。`
- **其他情況** → 忘掉本 session 先前的 daily 狀態（看板、暫停 / 繼續），載入 `daily` 對 `today` 執行開場。開場本身就是 idempotent 的 `ensure_*`，檔案在就沿用、不在才建。

到這裡結束。之後的分流、收尾都回到 `daily`。

## 邊界
- 不對舊日做任何事：不整理日記、不印舊日總結、不勾 checkbox、不改 `health.md`。
- 不接受日期參數，日期只來自系統時間。
- 全程繁體中文，回覆一行或一個看板。
