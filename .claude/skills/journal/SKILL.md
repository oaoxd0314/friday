---
name: journal
description: Internal module of `daily` (the user does not call it directly; if they type "/journal", "寫日記", "記日記", "日記模式", load `daily` instead). Owns daily/<YYYY>/<YYYY-MM-DD>/journal.md - silent collection of the user's free-form thoughts (each acknowledged only with "#####"), compiling them into a first-person diary on "結束日記", then giving synthesized advice via `about-me` and updating the personality wiki.
---

# Journal（日記模組）

> 使用者 直接呼叫時：不要在這裡開始，改載入 `daily` skill，由它開場與分流。這份文件只定義「想法」這條線的規則。

## 檔案
- 路徑由 `daily-note` 給：`<day_dir>/journal.md`（例 `daily/2026/2026-09-18/journal.md`）。
- **若已存在不要覆蓋**：讀進來，新內容接在後面，用 `---` 分隔，加一個帶時間的小標題（`## 21:30 補記`）作為本次 session。
- 檔案第一行是 `# journal <YYYY-MM-DD>`（沿用既有慣例，方便在 Obsidian 標題區辨識日期，因為檔名只叫 `journal`）。

## 收集規則（進入後嚴格遵守）
1. **完整收集**：忠實記下每一個想法、隨筆、零碎靈感，不遺漏細節。原始內容暫存在 context 或 scratchpad，整理時才動它。
2. **只回 `#####`**：每次收到想法**只回 `#####`**，沒有其他文字、解釋、追問。（`daily` 分流時若同一則訊息還有身體 / 待辦內容，那一行回覆就是 ack，不再多回 `#####`。）
3. **架構偵測**：他開頭若提到架構（「今天要說的大概有…」），記下來，整理時據此分類；沒提就依主題自行歸類。
4. **暫停 / 繼續**：「暫停」停止記錄與整理、等待；「繼續」才恢復。暫停期間不記。
5. **結束**：只有「結束日記」（或 `daily` 判定的收尾指令）才開始整理。沒有收集到任何想法就不產生檔案。

## 整理輸出（收尾）
寫檔前對照四點，全部滿足才輸出：
1. 完整記錄所有分享內容（不遺漏）。
2. **第一人稱**，不改原意下優化表達、流暢貼切。
3. 依他給的架構或內容主題分類，層次分明。
4. 最後附條列式簡單回顧。

格式：
- Obsidian flavored markdown，主題用 `##` 分段。
- 結尾回顧用 callout：
  ```
  > [!summary] 今日回顧
  > - 重點一
  > - 重點二
  ```
- 不加 frontmatter；概念可用 `[[wikilink]]`。引用別天日記一律完整路徑：`[[daily/2026/2026-07-29/journal|journal 2026-07-29]]`。

## 收縮想法、給建議（寫檔後）
1. **收縮想法給建議，在對話裡回長評**：載入 `about-me`，用他的決策軸（值不值得投入時間 / 對選擇權與長期累積的影響 / 可逆與否）給個人化建議，點出盲點。直接給結論，不攤推理過程。**長評只回在對話裡，不寫進檔案**（檔案裡只留 `## review` 節錄）。形式：第二人稱、散文、每個主題一段，可以長；引用他過去日記的具體日期與句子當證據，對等、可被反駁、不奉承。
2. **把建議落到隔天**（對話裡的建議沒辦法讓隔天的自己受益，要落到檔案）：在 `journal.md` 最後 append 一區 `## review`，它是對話長評的**節錄**——只留動作，不重複論證。**動作優先**——第一條一定是「明早 5 分鐘內可結束的第一步」，其餘 2–3 條是從建議收斂出的具體做法，每條粗體開頭、一兩句，不重複上面的推理。寫完呼叫 `daily-note.ensure_review_embed(next_day)`，讓隔天 entry 的 `## Yesterday Review` 嵌入 `![[<day_dir>journal#review]]`。`## review` 必須是檔案最後一個 `##` 區塊，embed 才只抓到它。
3. **更新認知檔案**：若透露了關於 使用者 的新認知，依 `about-me` 的維護規則更新 `personality/topics/` 對應分頁，「來源」區用 `[[daily/<YYYY>/<YYYY-MM-DD>/journal|journal <YYYY-MM-DD>]]` 標明。更新動作不需要解釋給他聽。

## 不做的事
- 不直接編 entry 檔與 `health.md`。entry 的日記連結與 `## Yesterday Review` embed 由模板 / `daily-note` 負責，journal 只透過 `daily-note.ensure_review_embed()` 觸發。
- 不在白天回覆內容、不安慰、不追問。
