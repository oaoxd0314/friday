# 資料來源對照表

各市場的搜尋關鍵字模板與主要資料平台。執行時用 WebSearch / WebFetch 查詢。

---

## 台股

### 主要平台
- **Goodinfo!** (`goodinfo.tw`) — 個股基本面、籌碼面、技術面綜合資料最完整
- **Yahoo 奇摩股市** (`tw.stock.yahoo.com`) — 即時報價、新聞、技術線圖
- **公開資訊觀測站** (`mops.twse.com.tw`) — 官方財報、重大訊息、月營收
- **CMoney 股市** (`cmoney.tw`) — 籌碼分析、主力進出
- **HiStock 嗨投資** (`histock.tw`) — 三大法人、融資融券
- **TradingView** (`tradingview.com`) — 國際格式技術線圖

### 搜尋查詢模板

**總金面**
```
台股 大盤 加權指數 [今日/本週] 走勢 外資買賣超
[產業類股] 類股 表現 [年/季]
台灣央行 利率 [年] 對股市影響
```

**籌碼面**
```
[代號] [公司名] 三大法人 買賣超 [近5日/20日]
[代號] 主力進出 千張大戶 持股
[代號] 融資融券 餘額 變化
[代號] 董監持股 庫藏股
```

**技術面**
```
[代號] 技術分析 KD MACD RSI 均線
[代號] 支撐 壓力 [近期]
[代號] 成交量 異常 突破
```

**基本面**
```
[代號] [公司名] 最新財報 EPS 毛利率 ROE
[代號] 月營收 年增率 [年月]
[代號] 本益比 殖利率 同業比較
[代號] [公司名] 重大新聞 [近一個月]
```

---

## 美股

### 主要平台
- **Yahoo Finance** (`finance.yahoo.com`) — 綜合資料，數據最齊全
- **Finviz** (`finviz.com`) — 視覺化篩選、籌碼、技術面
- **Seeking Alpha** (`seekingalpha.com`) — 深度分析文章
- **TradingView** (`tradingview.com`) — 技術線圖
- **MacroTrends** (`macrotrends.net`) — 長期財務指標
- **SEC EDGAR** (`sec.gov/edgar`) — 官方申報文件

### 搜尋查詢模板

**總金面**
```
S&P 500 Nasdaq [date] performance market sentiment
Fed interest rate decision [year] impact on stocks
[sector] sector rotation [year quarter]
```

**籌碼面**
```
[ticker] institutional ownership 13F filings [recent quarter]
[ticker] insider trading buying selling [recent]
[ticker] short interest float
[ticker] options unusual activity
```

**技術面**
```
[ticker] technical analysis moving average RSI MACD
[ticker] support resistance levels [recent]
[ticker] chart pattern breakout
```

**基本面**
```
[ticker] earnings report [latest quarter] EPS revenue
[ticker] PE ratio PEG forward earnings
[ticker] revenue growth YoY profit margin
[ticker] [company name] news [last 30 days]
```

---

## 港股

### 主要平台
- **AAStocks** (`aastocks.com`) — 港股綜合，繁體中文
- **東方財富網** (`eastmoney.com`) — 港股專區
- **富途牛牛** (`futunn.com`) — 港美股報價、技術分析
- **HKEX** (`hkex.com.hk`) — 官方披露文件

### 搜尋查詢模板

**總金面**
```
恆生指數 [日期] 走勢 南下資金 北上資金
港股 [產業] 板塊 [年/季]
```

**籌碼面**
```
[代號] [公司名] 機構持股 變動
[代號] 沽空 比例 港股通 持股
[代號] 大股東 增持 減持
```

**技術面 / 基本面** — 同台股模板，用繁體中文搜尋

---

## 通用備援

如果上述平台搜尋不到資料：
1. 改用 Google Finance / Bing Finance 直接查代號
2. 找該公司官方 IR 頁面
3. 公司財經 podcast / YouTube 分析（標註為次要來源）

## 注意事項

- **時效性**：股價、籌碼資料每日變動，務必標註查詢日期
- **多源交叉**：重要數據（如 EPS、營收）至少從兩個來源確認
- **官方優先**：財報數據以公開資訊觀測站 / SEC / HKEX 為準，新聞報導為輔
- **匯率提示**：美股、港股的價格附上換算成新台幣的概略值（用近期匯率）
