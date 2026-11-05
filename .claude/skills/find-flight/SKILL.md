---
name: find-flight
description: This skill should be used when the user says "幫我找機票", "查機票", "find flights", "找便宜機票", "機票怎麼訂", or provides a destination with or without dates and wants flight research. Selects the right search strategy based on how specific the user's request is, then verifies date-bound prices with browser click-through before reporting.
version: 0.2.0
---

# Find Flight Skill

Research flights using targeted strategies based on how specific the user's request is. Match the strategy level to the context — don't over-research when dates are already fixed, don't under-research when they're wide open.

Default departure: **TPE (桃園) / TSA (松山)** unless specified otherwise — 換成自己常用的出發機場。

## Step 1: Assess Request Specificity

Determine the level before picking strategies:

| Level | Context | Signals |
|-------|---------|---------|
| **1** | 目的地，無日期 | "找福岡的機票" |
| **2** | 目的地 + 日期範圍 | "5月中到6月底，找福岡機票" |
| **3** | 目的地 + 具體日期 | "5/20出發5/25回，找福岡機票" |
| **4** | 具體航班/航線，想確認費用 | "星宇 5/20 TPE-FUK，算真實費用" |

## Step 2: Select Strategies by Level

See `references/prompts.md` for the exact prompt text to use in WebSearch.

### Level 1 — 無日期，探索最佳時機
Use: **指令 02 (機率掃描)** + **指令 05&06 (替代樞紐 & 最佳時機)**
- Find which date combinations are historically cheapest
- Find if nearby hub cities offer cheaper routing
- Output: recommended travel window + booking timing advice

### Level 2 — 日期範圍，找最佳組合
Use: **指令 02 (機率掃描)** + **指令 01 (彈性套利)**
- Within the given window, rank cheapest departure-return pairs
- Check if nearby airports (HSZ, KHH) offer cheaper options
- Output: top 3 date combinations + airport comparison

### Level 3 — 日期確定，最大化性價比
Use: **指令 01 (彈性套利)** + **指令 03 (隱形成本)** + **指令 04 (分段拆解)** → **決策**
- Check nearby departure airports
- Calculate true all-in cost per airline (baggage, seat, meals)
- Test if multi-city is cheaper than direct
- End with 決策: best plan + 48-72hr backup plan

### Level 4 — 已選航班，確認真實費用
Use: **指令 03 (隱形成本)** only
- Calculate total cost with all add-ons for the specific airline/route

## Step 3: Execute WebSearch

For each selected strategy, run WebSearch using the prompt templates in `references/prompts.md`. Fill in the route, dates, and context from the user's request.

WebSearch 只能拿到候選名單和背景資訊。**任何要回報給使用者的價格與時刻,都必須經過 Step 3.5 驗證,不可直接抄 WebSearch 結果。**

## Step 3.5: 驗證(必做,不可跳過)

### 為什麼

2026-09 教訓:WebSearch 拿到的是「當前航季時刻 + 不綁日期的月低價」,整份研究錯掉重做。Google Flights 列表價也可能比官網結帳價低（實測列表價與官網結帳價可差 ~8%）。

### 怎麼驗

1. **綁日期查詢**:用 puppeteer/chrome-devtools 開 `https://www.google.com/travel/flights?q=Flights to {DST} from {SRC} on {YYYY-MM-DD} through {YYYY-MM-DD}&curr=TWD&hl=en`,確認頁面日期真的套上(檢查 Check-in/Check-out 欄位值),不能信 URL 參數一定生效。
2. **點到底**:對每組要回報的行程,點選去程 → 點選回程 → 進到 booking options 頁,記錄「Book with {航空公司}」的價格。只有這一層的價格才算數,列表層的 from 價常是混票(SEPARATE TICKETS)或過期快取。
3. **看行李條件**:booking 頁會寫「1st checked bag free / available for a fee」。廉價票種(如星宇 Basic)不含託運,要把加購費算進全含成本再比價。
4. **標定官網差**:Google booking 價 ≠ 官網結帳價,回報時標註「官網可能 +5~10%」;若使用者提供官網實測價,以官網為準並記下校正比例。
5. **航季陷阱**:3 月底/10 月底換季,flight tracker 網站(FlightAware、Trip.com status 頁)只顯示當前航季時刻,不可拿來推未來航季。冬季常有增班,「每日一班」的假設要重新驗。
6. **掃日期網格**:對主要航空套用 airline filter 後開 Date grid / Price graph,平日回程常比週末便宜 20-25%（實測可差 20-25%）。

### 多筆驗證 → 開多個 agent 平行處理

要驗證的「日期組合 × 航空公司」超過 3 組時,不要序列做:

- 每組(或每家航空)開一個 general-purpose agent,同一個 message 裡一次全部發出去讓它們並行
- 每個 agent 的 prompt 要含:完整航段(班號+日期+起降時間)、要驗證的基準價、驗證步驟(上面 1–4)、回報格式(價格綁航段)
- **瀏覽器衝突**:puppeteer / chrome-devtools 是單一共用瀏覽器實例,同時只能一個 agent 用。要嘛只派一個 agent 用瀏覽器、其他用 WebSearch/WebFetch 查官網票價頁,要嘛用 chrome-devtools 的 `new_page` + `isolatedContext` 各開獨立 context
- Agent 可能 stall:設好每個 agent 的任務要小而明確(一組行程一個結論),失敗就自己補做,不要等

## Step 4: Synthesize Results

After gathering results, present the **top 5 cheapest pairs** that meet the user's requirements (preferred airlines, direct-only, date window, etc.), sorted cheapest first.

### Output Format — One Block Per Pair

Each pair uses this structure (no extra prose between blocks):

```
YYYY/MM/DD ~ YYYY/MM/DD

去程
MM/DD HH:MM | 班機號碼 | 航空公司 | 票價

回程
MM/DD HH:MM | 班機號碼 | 航空公司 | 票價
```

Rules:
- **HH:MM** = departure time only (no arrival time)
- If flight number is unknown, write `—`
- Ticket price = per-person one-way fare (include taxes if known); mark estimated prices with `~`
- After all 5 pairs, add a one-line note on the cheapest platform and any time-sensitive warning

### Filtering Priority
1. Exact airline/route constraints the user specified (e.g., direct only, specific carriers)
2. Cheapest total round-trip cost
3. Preferred departure days (Tue/Wed tend to be cheapest)

### For Level 3+, also end with:
**決策 (The Verdict)**: best booking plan + backup if prices change within 48-72 hours
