# 📊 My Quant Trader Lab (純台股實戰本金量化交易系統)

> 📊 **實時量化交易儀表板 (Live PWA Dashboard)**：  
> **👉 [https://handpine.github.io/My_QuantTrader_Dashboard/](https://handpine.github.io/My_QuantTrader_Dashboard/)**  
> *(點擊上方連結或複製網址，即可在瀏覽器查看/安裝本實驗室的實用策略收益與最新決策數據)*

---

## 📂 專屬回測與白皮書目錄 (Master Blueprints & Knowledge Guides)

本專案所有的定稿實戰白皮書、全決策量化數據審計、盲測集驗證報告與指標幾何指南，集中存放於 **`backtest_reports/`** 資料夾：

* 🏆 **[純台股實戰本金量化策略全決策白皮書](file:///c:/Users/USER/finished_works/My_QuantTrader/backtest_reports/real_capital_strategy_plan.md)**  
  **【全策略核心藍圖】** 包含 300 萬總本金與 100 萬虛擬帳戶（今日 2026/08/03 正式啟動）之設定、選股池標的篩選、70/30 多空劃分、模型 2 風險平坦度個股權重、方法 A 利潤撥轉活存保本、組合二狀態延續型分別參數，以及 7 年訓練集 (2018-2024) 與 1.5 年盲測集 (2025-2026) 全對比與大盤 Benchmark 全審計。
* 📘 **[量化評估指標與算式指南](file:///c:/Users/USER/finished_works/My_QuantTrader/backtest_reports/quant_evaluation_metrics_guide.md)**  
  收錄 Calmar, Sharpe, Sortino Ratio 的權威數學算式、天空山峰 vs 水下坑洞幾何圖像模型、Gaussian Z 表誤區導正，以及 **Cornish-Fisher 非對稱偏態展開勝率修正法**。

---

## 📈 實戰定稿策略架構 (Master Quant Strategy Architecture)

本系統採用經過 100% 全數據審計之 **「組合二 + 方法 A + 模型 2 (風險平坦度 Risk Parity) + 70/30 多空劃分」** 終極風控波段架構：

### 1. 🟢 70% 多頭波段帳戶 (100萬虛擬帳戶配給 70 萬底金)
* **交易標的與 Risk Parity 風險平坦度權重**：
  * **`00713.TW` 元大高息低波**：**`35%`** (24.5 萬 / 防禦主心骨 / 20/60 SMA + 2.5x ATR)
  * **`006208.TW` 富邦台50**：**`25%`** (17.5 萬 / 大盤引擎 / 50/150 SMA + 3.0x ATR)
  * **`00878.TW` 國泰高股息**：**`25%`** (17.5 萬 / 巨型護城河 / 20/60 SMA + 2.5x ATR)
  * **`2330.TW` 台積電**：**`15%`** (10.5 萬 / 權值箭頭 / 50/150 SMA + 4.0x ATR)
* **進場邏輯 (狀態延續型 State-Based)**：
  1. **牛熊濾網**：標的價格高於 200 日年線 (`Price > SMA 200`)。
  2. **多頭狀態**：短均線高於長均線 (`SMA_Fast > SMA_Slow`)。
  3. **狀態建倉**：只要市場處於多頭狀態且當前帳戶未持倉，即時建倉。
* **退場與風控條件**：
  1. **個股 Tailored ATR 移動止損**：依據個股波動特性分別設定 `2.5x ~ 4.0x ATR` 移動止損。
  2. **均線死叉**：短均線向下跌破長均線時強制平倉。

### 2. 🛡️ 30% 空頭防禦與獲利撥轉帳戶 (100萬虛擬帳戶配給 30 萬底金)
* **100% 保持 1.75% 高利活存/公債**：避開反向 ETF (00632R) 的轉倉死穴與內扣損耗 (-69% 虧損)。
* **方法 A (利潤撥轉保本機制)**：當多頭個股獲利平倉時，超過初始配給本金的**所有超額利潤，強制轉存至 1.75% 活存鎖死**；股票池僅保留原始底金繼續交易，為 1 年操作期提供 100% 強保本防護。

---

## 🤖 GitHub Actions 自動化流程與抗洗盤保護機制

本專案於 `.github/workflows/auto_trader.yml` 部署了全自動化 GitHub Actions 流程，具備 **抗洗盤時間閘門保護 (Anti-Whipsaw Time-Gated Protection)**：

```mermaid
graph TD
    Cron[GitHub Actions 定時觸發] --> TimeCheck{時間閘門判定}
    
    TimeCheck -- 09:30 / 11:30 --> Monitor[🔍 純行情數據監控時段]
    Monitor --> UpdatePrice[更新現價與持倉現值 / 不觸發交易訊號 / 避免盤中洗盤甩種]
    
    TimeCheck -- 13:15 尾盤 --> Decision[🏆 實戰決策下單時段 (收盤前15分鐘)]
    Decision --> CalcSignal[計算均線與 Tailored ATR 止損 / 99.5% K線定型]
    CalcSignal --> Alert[發出實戰下單建議 / 長輩有 15 分鐘撮合下單]
    
    TimeCheck -- 14:00 盤後 --> PostMarket[🌙 盤後總結算與 Dashboard 自動發布]
    PostMarket --> Sync[同步數據 public_data.json 與 PWA 至 GitHub Pages]
```

### 每日 4 大自動定點運算時程：
1. 🌅 **09:30 TST (UTC 01:30)**：早盤趨勢觀察與持倉現值更新（純觀測，鎖死進出判斷）。
2. ☀️ **11:30 TST (UTC 03:30)**：午盤量價觀察與持倉現值更新（純觀測，鎖死進出判斷）。
3. 🏆 **13:15 TST (UTC 05:15)**：**【最關鍵實戰下單訊號】** 距離 13:30 收盤僅 15 分鐘，當日 K 線 99.5% 定型，完全過濾盤中洗盤雜訊，提供當天現場在券商 App 撮合下單時間窗！
4. 🌙 **14:00 TST (UTC 06:00)**：盤後結算，自動將最新數據同步推送到 `Handpine/My_QuantTrader_Dashboard`。

---

## 📱 PWA Live 儀表板設計與數據結構

儀表板採用 Glassmorphism 毛玻璃視覺設計，版面結構經過舒適化重構：

1. **頂部極簡 KPI 三卡片**：
   * **100萬虛擬帳戶總淨值**：顯示首日 (2026/08/03) 啟動淨值與精確手續費扣除現況。
   * **70% 多頭波段部位總現值**：顯示個股持倉市值與零股殘餘現金。
   * **30% 防禦活存與撥轉池**：顯示 30 萬防禦底金與方法 A 鎖定活存利潤。
2. **資產淨值歷史曲線圖 (Chart.js)**：實時繪製 100 萬帳戶淨值累積動態。
3. **70% 多頭部位與即時訊號總表**：包含 00713, 006208, 00878, 2330 之最新現價、持倉股數、部位現值、策略參數配置與當前狀態。
4. **交易紀錄 Log**：記錄今日 (2026/08/03) 首日建倉及後續平倉交易詳情。
5. **底部量化指標對照總表**：在頁面最下方以簡明表格呈現 **Calmar Ratio (年化/累積)、Sortino Ratio、Sharpe Ratio、盲測 MDD (-10.24%) 與 預期勝率 (> 75%)**。

---

## 📂 專案目錄結構

```text
├── .github/workflows/
│   └── auto_trader.yml                 # 每日 4 次自動執行與抗洗盤下單訊號更新
├── backtest_reports/                   # 📊 專屬白皮書與指標指南資料夾
│   ├── real_capital_strategy_plan.md   # 🏆 純台股實戰本金量化策略全決策白皮書
│   └── quant_evaluation_metrics_guide.md # 📘 量化指標與偏態修正算式指南
├── my_quant_lab/                       # 量化實驗室核心交易引擎
│   ├── master_strategy_executor.py     # 🏆 實戰定稿 Master 策略執行器
│   ├── generate_dashboard.py           # 100萬虛擬帳戶與 Dashboard JSON 生成器
│   └── backtest_runner.py              # 回測與績效分析工具
├── index.html                          # PWA 磨砂玻璃風實戰儀表板 (含刷新按鈕與底部指標表)
├── manifest.json                       # PWA 設定檔
├── public_data.json                    # 公開去敏感績效數據
├── requirements.txt                    # Python 套件依賴
└── README.md                           # 本說明文件
```

---

## 🛠️ 本地開發與手動執行

### 1. 安裝環境依賴
```bash
pip install -r requirements.txt
```

### 2. 執行實戰定稿策略全量化回測與驗證
```bash
python my_quant_lab/master_strategy_executor.py
```

### 3. 生成最新儀表板數據
```bash
python my_quant_lab/generate_dashboard.py
```
