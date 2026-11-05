# Travel Plan Templates

## Draft File Template

```markdown
- 住
   - [推薦區域或飯店]

- 景點
   - [區域 1]
      - [景點名稱] [備註]
   - [區域 2]
      - [景點名稱] [備註]

- 美食
   - [餐廳或料理名稱] [備註]

- 購物
   - [店名或地點] [備註]
```

No frontmatter, no tags — it's a scratch pad. Organize into sub-sections by area or meal type as needed. Add reservation links and caveats inline.

---

## Main Overview Template

```markdown
---
tags:
  - [trip-slug]   ← e.g. 2026東京
---
# [Trip Title]   ← e.g. 2026 東京

> Plan: [日期區間]，共 [N] 天 [N-1] 夜

# 預定資訊

+ ### 住宿資訊

   - [城市] **[飯店名稱]**
      - [地址或 Google Maps 連結]

+ ### 餐廳清單

   - Day 1 [MM/DD]
      - 早餐: (待確認)
      - 午餐: (待確認)
      - 晚餐: (待確認)

   - Day 2 [MM/DD]
      - 早餐: (待確認)
      - 午餐: (待確認)
      - 晚餐: (待確認)

+ ### 票卷清單

   - [ ] 機票
   - [ ] 住宿
   - [ ] (其他票券)

## 行程

- Day 1 - [theme]
   - [[MM-DD Day 1 - theme]]

- Day 2 - [theme]
   - [[MM-DD Day 2 - theme]]
```

---

## Day File Template

```markdown
---
tags:
  - [trip-slug]
---
# [MM/DD] Day [N] - [theme]

## 時間表

| 時間 | 地點 | 備註 |
|------|------|------|
| (待確認) | (待確認) | (待確認) |
```

---

## 班機資訊 Section (add to Day file when applicable)

```markdown
## 班機資訊

- 航班: [航班號]
- 出發: [機場] [時間]
- 抵達: [機場] [時間]

- 出發前 Check List
   - [ ] 護照
   - [ ] 信用卡
   - [ ] 行動電源
   - [ ] eSIM 啟用
```

---

## Check List Template (scope file)

```markdown
---
tags:
  - [trip-slug]
---
# Check List

## 訂票

- [ ] 機票
- [ ] 住宿
- [ ] (景點門票)

## 行前準備

- [ ] 護照效期確認
- [ ] eSIM 或網卡
- [ ] 換日幣
- [ ] 旅遊保險

## 出發當天

- [ ] 護照
- [ ] 信用卡
- [ ] 行動電源充飽
- [ ] 確認飯店 check-in 時間
```

---

## Scope File Template (generic research note)

```markdown
---
tags:
  - [trip-slug]
---
# [Topic Title]

## [Sub-section]

- [item]
   - [detail]
   - [link or note]
```
