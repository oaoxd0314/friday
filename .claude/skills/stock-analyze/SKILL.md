---
name: stock-analyze
description: This skill should be used when the user says "/stock-analyze", "分析 [代號]", "幫我看 [代號] 值不值得買", or provides a market + ticker symbol asking for buy/sell recommendation. Researches a stock from four facets — 總金面 (macro + capital flow), 籌碼面 (institutional positioning), 技術面 (technical), 基本面 (fundamental) — and produces a structured Obsidian note with a buy decision saved to `reference/stocks/`.
version: 0.1.0
---

# Stock Analyze Skill

Research a single stock across four dimensions and produce an Obsidian note ending with an actionable buy/hold/avoid recommendation. Output goes to `reference/stocks/[市場]-[代號]-[公司名].md` in the vault.

## Step 1: Parse Input

Expected formats:
- `/stock-analyze 台股 2330`
- `/stock-analyze 美股 AAPL`
- `/stock-analyze 港股 00700`
- Free-form: "幫我分析台股 2330"

Extract:
- **市場** (台股 / 美股 / 港股)
- **代號** (ticker)
- **公司名** (resolve via WebSearch — needed for note filename)

If market is missing, infer from ticker format:
- 4 digits → 台股
- All English letters → 美股
- 5 digits starting with 0 → 港股

If still ambiguous, ask the user.

## Step 2: Research Four Facets

Run WebSearch in parallel for the four facets. See `references/data-sources.md` for per-market URL patterns and search query templates. See `references/analysis-framework.md` for which indicators to look for in each facet.

The four facets:

1. **總金面** (macro + capital flow combined)
   - 大盤指數走勢、Fed/央行政策、利率匯率
   - 外資、投信、自營商買賣超與資金流向
   - 融資融券餘額、產業類股輪動

2. **籌碼面** (institutional positioning)
   - 三大法人買賣超（最近 5/20 日）
   - 主力進出、千張大戶持股變化
   - 董監持股、庫藏股、現金增資/減資公告

3. **技術面** (technical)
   - 均線排列（5/20/60 日）、KD、MACD、RSI
   - 支撐壓力位、成交量變化、型態（突破/盤整/跌破）
   - 近期高低點、波動率

4. **基本面** (fundamental)
   - 最新財報：EPS、毛利率、營益率、ROE
   - 月營收年增率、季營收趨勢
   - 本益比 (PER)、股價淨值比 (PBR)、殖利率
   - 產業地位、護城河、近期重大新聞

## Step 3: Score and Decide

Apply the scoring rubric in `references/decision-rubric.md`:
- 每個面向打分 1-5（1=極差，5=極佳）
- 加總四面向得分（4-20）並對應：
  - 17-20 → 強烈建議買進
  - 13-16 → 可考慮買進，分批進場
  - 9-12 → 觀望，等更好進場點
  - 4-8 → 不建議買進

Write reasoning that connects facts to the score — never just give a number without justification.

## Step 4: Write Obsidian Note

Use the template at `assets/note-template.md`. Save to:

```
reference/stocks/[市場]-[代號]-[公司名].md
```

Examples:
- `reference/stocks/台股-2330-台積電.md`
- `reference/stocks/美股-AAPL-Apple.md`

Note conventions:
- Frontmatter `tags:` = `[stock, [市場], [代號]]`
- Frontmatter `analyzed_at:` = today's date (YYYY-MM-DD)
- All content in Traditional Chinese (繁體中文) — except company name in English markets
- Use Tasks plugin checkboxes (`- [ ]`) for action items (e.g., "等回測 200 日線再進場")
- Cite data sources at the end so future-you can verify

If the file already exists:
- Read it first
- Add a new dated section under `## 歷次分析` rather than overwriting
- Update frontmatter `analyzed_at` to today

## Step 5: Summarize in Chat

After writing the note, output a concise summary in chat:
- 一行總結：「[公司名]（[代號]）— [建議]，得分 X/20」
- 各面向一句話評論
- 最關鍵的 1-2 個風險或催化劑
- 連結：`[[reference/stocks/[檔名]]]`

Keep chat output short — the full analysis lives in the note.

## Conventions

- **不預測股價** — 提供分析，不給目標價（避免誤導）
- **註明資料時效** — 每個指標標註查詢日期
- **承認不確定性** — 找不到資料時明說，不要編造
- **分散風險提醒** — 任何「強烈買進」建議都要附上「分批進場、設停損」的提醒
- **免責聲明** — 筆記末尾固定加一行：「本分析僅供參考，投資決策請自行判斷」
