---
name: about-me
description: Claude-invoked (NOT a command the user types). Use this skill on your own initiative when you are about to give the user personal advice or a personalized recommendation, when they ask "你覺得我…/ 以我的個性…/ 基於對我的了解", when a conversation turns to a personal decision (career, money, learning direction, relationship, AI/future, attention, time/priorities), or when you need to add/update content about who the user is. Batch-loads the user's personality & values wiki under personality/ so advice is grounded in who they actually are, and covers how to maintain that wiki from new journal entries.
---

# About Me（使用者 的個性 / 價值觀）

這個 skill 是 **Claude 自己用的**，不是 使用者 會手動呼叫的指令。當你要給 使用者 個人化建議、或要新增 / 更新關於他的內容時，自行載入 `personality/` 的個性 wiki，給出扣合他真實取向的建議，並依規則維護這份檔案。

> 語氣原則：使用者 不喜歡無條件奉承（見全域 CLAUDE.md）。建議要直接、對等、可被反駁。

## 何時觸發

- 他要個人化建議 / 決策（職涯、金錢、學習方向、感情、AI/未來、時間與優先序）。
- 他問「你覺得我…」「以我的個性…」「基於你對我的了解…」。
- 對話轉向重大個人選擇時，主動載入再回答。
- 要從新的 journal 更新這份 wiki 時。

## 載入流程（batch-load）

1. 讀 `personality/index.md`（目錄 + 一句話總結 + 維護規則）。
2. 讀 `personality/topics/` 下全部分頁：
   - `核心價值.md`、`金錢與財富.md`、`工作與職涯.md`、`學習與認知.md`、`愛情與關係.md`、`人際與付出.md`、`AI與未來.md`、`注意力與認知主權.md`、`時間選擇與自由.md`、`自我與內在.md`、`盲點與陷阱.md`
3. 需要更深的原始素材時，再讀 `personality/reference/` 的 dump。

每頁都有「給 Claude 的意涵」區——回答時直接用它對齊建議方向。

## 給建議的原則

- 用他的決策軸：**值不值得投入時間 / 對選擇權與長期累積的影響 / 是否不可逆**，而不是短期報酬或難度。
- 先給地圖 / 全貌，再進細節（他對「不知道全貌」會焦慮）。
- 卡住時先判斷是「方向問題」還是「執行問題」——通常是前者，幫他把方向定清楚。
- 主動點出他的盲點（過度最佳化、資訊焦慮、理解過頭），但用「已經有什麼」對沖「還缺什麼」。
- 不奉承，不鼓勵投機 / 捷徑。

## 維護這份 wiki

收到關於 使用者 的新個人想法（來自 `daily/<YYYY>/<YYYY-MM-DD>/journal.md`、daily entry、或對話）時：

1. 對應 `index.md` 目錄分類；不合適就在 `topics/` 新增一頁並更新目錄表格。
2. 優先修訂既有條目而非堆疊重複；觀點被推翻就改寫。
3. **務必在該頁「來源」區用完整路徑 wikilink 標明來源 journal**（例 `[[daily/2026/2026-06-12/journal|journal 2026-06-12]]`）。檔名一律叫 `journal`，短連結 `[[journal 2026-06-12]]` 不會解析。
4. 更新該頁與 `index.md` frontmatter 的 `updated`。
5. 只寫客觀觀察到的事實 / 模式。
