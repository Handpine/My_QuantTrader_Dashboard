# My Quant Trader Lab (我的量化交易實驗室)

> 📊 **實時量化交易儀表板 (Live PWA Dashboard)**：
> **👉 [https://handpine.github.io/My_QuantTrader_Dashboard/](https://handpine.github.io/My_QuantTrader_Dashboard/)**
> *(點擊上方連結或複製網址，即可在瀏覽器查看/安裝本實驗室的實時策略收益數據)*

---

這是一個基於 Python 與 Alpaca API 的自動化美股量化交易系統。本系統支援 **「多頭與空頭策略雙向並行」**，採用 **「虛擬子帳戶」** 分離數據，並使用 GitHub Actions 實現 100% 雲端無伺服器託管，每天定時執行交易判定，並將交易數據同步發布至公開的 PWA 儀表板網頁。

系統內部時間已全數同步為 **台灣標準時間 (Asia/Taipei, UTC+8)**，確保交易日誌與儀表板上的時間戳記與您的日常生活完美對齊。

---

## 📈 終極對稱架構 (The Ultimate Symmetrical Architecture)

本系統在同一個 Alpaca 帳戶下，將資金平分為兩個獨立的虛擬子帳戶（各 $50,000 美元），並行運行以下兩套基於「各自趨勢與動態 ATR 止損」的對稱波段策略：

### 1. 🟢 多標的波段策略 (多頭子帳戶)
為了不錯過任何板塊的爆發輪動，多頭系統將資金切分為 3 份，分別投資於不同領域的龍頭 ETF。

* **交易標的**：`QQQ` (納斯達克 100), `SMH` (半導體), `DIA` (道瓊工業)
* **初始資金**：**$50,000 USD** (每個標的最多分配 $16,666)
* **進場條件 (雙重過濾)**：
  1. **各自的牛市過濾**：該標的當前價格必須**高於其 200 日均線 (SMA 200)**。
  2. **黃金交叉**：該標的的 20 日均線（SMA 20）**向上突破** 50 日均線（SMA 50）。
* **退場與防禦條件 (滿足任一即平倉)**：
  1. **動態 ATR 移動止損 (Trailing Stop)**：買入時自動計算該標的的 14 天真實波動幅度 (ATR)，並設定為 **2.5 倍 ATR** 的移動止損。確保高波動標的 (如 SMH) 有足夠的防守寬度，而低波動標的 (如 DIA) 能緊密止損。
  2. **趨勢反轉 (死亡交叉)**：當 20 日均線**向下跌破** 50 日均線時，全倉賣出平倉。

### 2. 🔴 雙重避險策略 (空頭子帳戶)
在熊市崩盤時，所有資產的相關性會趨近於 1。本策略透過 1 倍反向 ETF，集中火力做空最具代表性的大盤與科技股，提供投資組合的終極保護傘。

* **監控對象**：`SPY` (標普 500) 與 `QQQ` (納斯達克 100)
* **交易標的**：買入 `SH` (反向 SPY) 與 `PSQ` (反向 QQQ)
* **初始資金**：**$50,000 USD** (每個標的最多分配 $25,000)
* **做空進場條件 (雙重過濾)**：
  1. **各自的熊市過濾**：監控對象 (`SPY` 或 `QQQ`) 的當前價格必須**低於其 200 日均線 (SMA 200)**。
  2. **死亡交叉確認**：監控對象的 20 日均線**向下跌破** 50 日均線。
* **退場與防禦條件 (滿足任一即平倉)**：
  1. **動態 ATR 移動止損 (Trailing Stop)**：依據反向 ETF (`SH` 或 `PSQ`) 的波動率，動態設定 **2.5 倍 ATR** 的移動止損。若大盤強勢反彈觸及止損，則退回現金。
  2. **趨勢反轉 (黃金交叉)**：當監控對象出現黃金交叉 (SMA 20 突破 SMA 50)，代表空頭告一段落，立刻平倉退場。

---

## ⚡ 儀表板架構 (Dashboard UI Features)

本系統的 PWA 儀表板已升級為「數據驅動 (Data-Driven)」的專業戰情室，具備以下核心功能：

1. **實體帳戶完全同步 (Total Portfolio Sync)**：
   儀表板最上方會強制透過 Alpaca API 拉取真實的 `account.equity`，確保顯示的總帳戶淨值（如 $100,000.02）與您的 Alpaca 實體帳戶 100% 吻合，消除虛擬子帳戶與實體帳戶間的落差。
2. **實時策略績效對比 (Live Performance Matrix)**：
   儀表板中央設有「多頭」與「空頭」的 Side-by-Side 績效對比表。每日收盤結算後，系統會自動重新計算雙向策略的 **累積回報、MDD、歷史勝率、盈虧比 (PF)、夏普比率**，讓策略優劣一目了然，取代傳統無意義的靜態回測展示。
3. **動態決策日誌 (Dynamic Trade Reasons)**：
   交易紀錄不再只是死板的買賣。系統會自動辨識下單來源，明確標示 `黃金交叉 (站上年線)`、`熊市雙重過濾觸發` 或 `ATR動態止損觸發` 等決策原因，做到 AI 交易邏輯完全透明。

---

## 🎨 PWA 行動裝置安裝與圖示設計
儀表板已完全支援 PWA 規範，可在 iOS 和 Android 裝置上「加入主畫面」成為獨立 App 運行，並擁有專屬的桌面 Icon：

* **圖示設計理念**：採用日系吉卜力手繪風格。在清爽的薄荷綠與奶油白漸層背景中，一枚圓潤的金幣上方冒出一片翠綠的新芽。象徵量化交易實驗室「穩健投資、財富自然發芽茁壯」的綠色金融精神。
* **行動裝置極致體驗**：在 iPhone 下啟用了黑系全螢幕模式（Black-Translucent），加入主畫面後打開將會**隱藏 Safari 網址列與控制項**，媲美原生 App 質感。

---

## 📂 專案結構
```text
├── .github/workflows/
│   └── auto_trader.yml         # 整合型工作流：每晚 10:17 自動執行雙向策略並發布網頁
├── my_quant_lab/               # 量化實驗室主資料夾
│   ├── keys.py                 # 本地 API 金鑰 (已在 .gitignore 中忽略，防洩露)
│   ├── config.py               # 交易參數設定 (如標的、API 網址)
│   ├── backtest_runner.py      # 歷史回測與績效分析引擎
│   ├── alpaca_sma_trailing_trader.py  # 多標的做多機器人實時程式
│   ├── alpaca_conservative_short_trader.py # 多標的做空機器人實時程式
│   ├── generate_dashboard.py   # 儀表板數據整合與去敏感工具
│   ├── trader_state.json       # 多頭子帳戶狀態與歷史淨值
│   └── short_trader_state.json # 空頭子帳戶狀態與歷史淨值
├── index.html                  # 磨砂玻璃風格 PWA 儀表板網頁
├── manifest.json               # PWA 清單設定檔 (名稱、啟動模式、圖示)
├── pwa_icon.png                # 日系金幣發芽 512x512 PWA 桌面圖示
├── public_data.json            # 去敏感的公開數據 (由 actions 定時更新)
├── requirements.txt            # Python 依賴套件清單
└── README.md                   # 本說明文件
```

---

## 🛠️ 本地開發與手動執行

### 1. 安裝環境依賴
```bash
pip install -r requirements.txt
```

### 2. 配置本地 API 金鑰 (`my_quant_lab/keys.py`)
```python
LOCAL_API_KEY = "您的 Alpaca API KEY"
LOCAL_SECRET_KEY = "您的 Alpaca SECRET KEY"
```

### 3. 手動運行交易機器人與生成數據
```bash
# 運行多頭策略
python my_quant_lab/alpaca_sma_trailing_trader.py
# 運行做空策略
python my_quant_lab/alpaca_conservative_short_trader.py
# 整合數據生成 public_data.json
python my_quant_lab/generate_dashboard.py
```

---

## 🤖 雲端自動化與雙倉庫 Pages 發布

本系統採用安全隔離機制，在私人倉庫定時交易，並將去敏感的展示數據自動推送到您的公開倉庫，由 GitHub Pages 免費託管。

### 1. 配置私人倉庫 Secrets
進入您**私人倉庫**的 `Settings` -> `Secrets and variables` -> `Actions`，新增三個金鑰：
* `ALPACA_API_KEY`：您的 Alpaca API KEY。
* `ALPACA_SECRET_KEY`：您的 Alpaca API SECRET KEY。
* `QuantTrader_Dashboard` 或 `PUBLIC_REPO_TOKEN`：您的 GitHub 個人授權 Token (PAT)（需勾選 `repo` 權限，用來寫入公開倉庫）。

### 2. 公開倉庫網頁設定 (`My_QuantTrader_Dashboard`)
1. 在 GitHub 建立一個名為 `My_QuantTrader_Dashboard` 的 **Public (公開)** 倉庫。
2. 當私人倉庫的 GitHub Actions 第一次執行成功後，會自動把 `index.html`、`public_data.json`、`manifest.json` 與 `pwa_icon.png` 推送到這個公開倉庫。
3. 進入該公開倉庫的 `Settings` -> `Pages`，將 Source 設為 `Deploy from a branch`，分支選擇 `main`，點擊 `Save` 即可啟用。

您的專屬儀表板網址：
`https://<您的GitHub帳號>.github.io/My_QuantTrader_Dashboard/`
