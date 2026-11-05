---
name: health-log
description: Internal module of `daily` (the user does not call it directly; if they type "/health-log", "記錄體重", "體重 XX.x", "早餐吃了…", "跑了 5K", "徒手做完了", or pastes a food / nutrition-label / watch photo, load `daily` instead). Owns daily/<YYYY>/<YYYY-MM-DD>/health.md - upserts weight, meals, run / strength training and notes, recomputes derived frontmatter, keeps the "## 今日" summary block (embedded by the daily entry) and the 每日操練 habit checkbox in sync. Reads all parameters, schedule and workout content from plan/Body Build/減脂計畫.md. Day-grain only.
---

# Health Log（每日身體紀錄模組）

> 使用者 直接呼叫時：改載入 `daily` skill。這份文件只定義「身體」這條線的規則。

一天一檔，隨記隨寫。狀態全在檔案裡，每次呼叫都是 read → upsert → write，不依賴對話記憶。

## 路徑與依賴
- Config（SSOT）：`plan/Body Build/減脂計畫.md` frontmatter — kcal / 蛋白質區間、目標體重、`schedule`（週課表，index 0 = 週一）、`run_workout`、`strength_workout`（今日該做什麼的文字）。**課表或動作改這裡，這份 skill 不用動。**
- 查表：`plan/Body Build/餐點速查.md`
- 今日檔：`<day_dir>/health.md`，`day_dir` 與 `today` 由 `daily-note` 給（04:00 前算前一天）。
- entry 檔的操作（勾 checkbox、確保 embed）全部呼叫 `daily-note`，不直接編 entry。
- 聚合視圖：`plan/Body Build/health.base`（讀 frontmatter，filter 是 `file.basename == "health"`，不用動）。

## 前置（每次呼叫）
1. 向 `daily-note` 取 `today` / `day_dir`，`ensure_entry()`、`ensure_health_embed()`。
2. 讀 config frontmatter 與 `餐點速查.md`。
3. `health.md` 不存在 → 建骨架。`day` = 與 `start_date` 的日差 + 1；課表依星期查 `schedule`；動作文字依課表取 `run_workout` / `strength_workout`。
   - **臨時 override**：config 若有 `override_from` / `override_to` 且 `today` 落在區間內（含兩端），課表改查 `override_schedule`（同樣 index 0 = 週一），動作文字查 `override_workouts.<key>`；key 以 `strength_` 開頭都算徒手（`strength` 三態、勾「每日操練」），`run` / `rest` 同原邏輯。「今日」訓練行課表顯示為 `💪 下肢` / `💪 全身`（取 key 底線後的中文對照：lower=下肢、full=全身）。區間外一律忽略 `override_*`。

骨架：
```markdown
---
date: 2026-09-18
day: 6
---
## 今日
- 體重（未量）
- 訓練 五 💪 徒手：未記錄 → 深蹲 15–20×3・伏地挺身 8–15×3・保加利亞分腿蹲 8–12/腳×3・臀橋 15–20×3・棒式 30–60 秒×2
- 飲食（未記錄）

## 飲食
- 早餐（未記錄）
- 午餐（未記錄）
- 晚餐（未記錄）

## 備註
```

## 「今日」區（entry 用 `![[…/health#今日]]` 只嵌這一區）
固定三行，每次寫檔都重算，這就是 daily 看板與 entry 看到的內容：
- 體重：`- 體重 X｜7 日均 Y｜距 goal_checkpoint_kg Z`，未量寫 `- 體重（未量）`
- 訓練：`- 訓練 <週幾> <課表>：<狀態>`
  - 課表：`🏃 5K` / `💪 徒手` / `休`
  - 狀態未記錄且非休息日：`未記錄 → <run_workout 或 strength_workout>`（讓他一眼看到今天要做什麼）
  - 有紀錄：`done 5.02K 30:25，6:05/km`、`done`、`partial，只做兩個動作`、`skipped，改明早補跑`
  - 休息日：`—`；休息日仍有訓練就直接寫結果 `🏃 2.42K 12:42，5:15/km`
- 飲食：`- 飲食 ~X kcal / ~Y g（n/3 餐）`，沒有任何餐寫 `- 飲食（未記錄）`

## 意圖分流
一則訊息可能同時含多種，全部處理；無法判斷才問，且只問一句。

### 體重
觸發：落在 config `start_weight` ± 10 的純數字、「體重 X」、「早上量 X」。
1. 寫 `weight`（兩位小數）。
2. `weight_7d`：讀最近 10 天內有 `weight` 的 health 檔（`daily/<YYYY>/<date>/health.md`），取最新 7 筆平均，round 2。不足 7 筆用現有筆數。
3. `rate_wk`：本日 `weight_7d` 減 7 天前那檔的 `weight_7d`，該檔不存在就不寫這個 key。
4. 更新「今日」區體重行。
5. 回一行狀態。若 `rate_wk` 存在，順帶對照 config 區間說在區間內 / 偏慢 / 偏快，只講事實一句，不給建議。`day` 超過 `baseline_weeks × 7` 且 rate 連續兩週落在區間外，才多加一句 config 裡對應的調整規則。這是唯一的建議時機。
6. 非空腹 / 非早晨的量測照記，但在備註寫一行條件。

### 飲食
觸發：早餐 / 午餐 / 晚餐 / 點心 / 宵夜 + 內容；食物照片；營養標示照片；「跟平常一樣」。
估算優先序與規則全照 `餐點速查.md`「規則」區：標示 > 速查 > 照片估 > 描述估；kcal 取到 50、蛋白質取到 5 g；估值標 `~`。
- **照片**：直接讀圖，辨識品項與份量，用「份量換算」估。沒有比例參考就用一般餐具尺寸。
- **營養標示照片**：讀每份熱量與蛋白質，乘上份數，標「標示」。新品項 append 到速查表「常吃品項」。
- **模糊描述**（一拳地瓜、半碗飯）：直接查換算表，不追問。
- **「跟平常一樣」**：套速查表預設餐；預設還是「待補」就問這餐是什麼，答案寫進速查表當預設。
- 每餐一行：`- 午餐 ~400 kcal / ~35 g：內容（來源）`。同一餐再補內容就累加到那行。
- 漏記的餐維持「（未記錄）」，**絕不補零、不猜**。刻意不吃寫 `0 kcal / 0 g：刻意不吃（已確認）`，算有記錄。
- 重算 frontmatter：`kcal`、`protein` 只加總有記錄的餐；`meals_logged` = 早午晚有記錄的數量（點心不算餐數但算熱量）；`diet_complete` = `meals_logged == 3`。沒有任何餐時這四個 key 都不寫。
- 更新「今日」區飲食行。

### 訓練
只記三態，不逐動作問。
- 跑步：「跑了」→ `run: done`；有數字補 `run_km`、`run_time`（mm:ss）、`run_pace`（自動算）。手錶截圖就讀數字。「今天沒跑」→ `run: skipped`。
- 徒手：「做了」「練完」→ `strength: done`；「只做兩個動作」「沒做完」→ `strength: partial` 加一句；「沒練」→ `strength: skipped`。
- 沒提到就不寫 key。**沒記 ≠ 沒做。**
- 休息日照課表判斷，不問為什麼沒練。
- 更新「今日」區訓練行；`run` 或 `strength` 為 `done` / `partial` 時呼叫 `daily-note.mark_habit("每日操練")`。

### 備註
睡眠時數（`sleep_h`）、腰圍（`waist`）、腸胃狀況、餓不餓、跑步有沒力、量測條件，一律一行寫進「備註」。腸胃與疲勞是 config「觀察項目」，有提到就記，沒提到不問。

## 寫檔原則
- 每處理完一個項目立刻寫檔，中途中斷也留紀錄。
- frontmatter 只放數值與狀態；「今日」區是給 entry 嵌的三行摘要；「飲食」「備註」是明細。三者每次都重算保持一致。
- 不建索引檔、不維護 `Workout Log.md`，聚合交給 `health.base`。

## 回覆原則
- 每次回一行，說寫了什麼、數字多少。例：`午餐 ~400 kcal / ~35 g，今日累計 ~820 / ~55（2/3 餐）`。
- 收尾總結一行：`今日 ~2515 kcal / ~100 g（2/3 餐）｜💪 skipped｜體重 X`。
- 白天不說教、不評論吃得好不好。唯一的建議時機在「體重」第 5 點。
- 全程繁體中文，不奉承，不說「你說的對」。
- 估算有明顯不確定時，括號註一個字：`（照片估）`、`（描述估）`，不長篇解釋。
