# Flight Search Prompt Library

Fill in `[出發地]`, `[目的地]`, `[日期/範圍]`, `[X km]` from the user's context before searching.

---

## 指令 01：彈性套利 (Flexible-Origin Arbitrage)
**用途**: 鄰近機場比價，判斷是否值得多花交通時間

```
Find cheaper routes to [目的地] using nearby departure airports within [X km] of [出發地].
Compare savings vs extra travel time + logistics.
```

**台灣出發常用備選機場**:
- TPE (桃園) ←→ TSA (松山)：同城，比直飛 vs 市區起飛
- TPE ←→ KHH (高雄)：搭高鐵約1.5hr，票價差需 > NTD 2,000 才值得

---

## 指令 02：機率掃描 (Probability Scan)
**用途**: 找歷史最低價日期組合，預測未來漲跌

```
Analyze flexible dates for [出發地] to [目的地] route within [日期範圍].
Rank departure-return pairs by historical lows and likelihood of future price drops.
```

---

## 指令 03：隱形成本 (Cost Audit)
**用途**: 算含行李/選位/餐食的真實總費用，不只看票面價

```
List airlines on [出發地] to [目的地] route on [日期] and calculate real total cost
including baggage, seat selection, meals, and mandatory fees — not just ticket price.
```

**常見廉航陷阱提醒**:
- 台灣虎航 (IT)：手提行李免費，托運需加購
- 樂桃 (MM)：選位費高，餐食需自費
- 酷航 (TR)：行李規定嚴格，超重費貴
- 星宇 (JX)：含一件 23kg 托運，但票價較高

---

## 指令 04：分段拆解 (Price Splitter)
**用途**: 測試拆成多段單程票是否更便宜

```
Test whether booking [出發地] → [目的地] on [去程日] and [目的地] → [出發地] on [回程日]
as separate one-way tickets is cheaper than a round trip.
Show segments, layovers, and savings with clear pros/cons.
```

---

## 指令 05 & 06：替代樞紐 & 最佳時機
**用途**: 找便宜中轉點 + 判斷現在該買還是等

```
Identify cheaper hub cities near [目的地]. Price partial routes + final legs that reduce total fare.

Based on seasonality for [出發地] to [目的地], tell me when to book, when to wait,
and when price spikes usually happen.
```

---

## 決策 (The Verdict)
**用途**: 綜合以上，給出最終訂票計畫 + 備案

```
Compare all options researched for [出發地] to [目的地] on [日期].
Give me the best booking plan plus a backup if prices change within 48-72 hours.
```

**輸出格式建議**:
- ✅ 主方案：航班 + 平台 + 預估總費用 + 訂票連結提示
- 🔄 備案：如果主方案漲價，改用什麼
- ⏰ 時間壓力：這個價位預計維持多久
