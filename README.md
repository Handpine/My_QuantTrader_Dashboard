# My Quant Trader Lab (我的量化交易實驗室)

> 📊 **實時量化交易儀表板 (Live PWA Dashboard)**：
> **👉 [https://handpine.github.io/My_QuantTrader_Dashboard/](https://handpine.github.io/My_QuantTrader_Dashboard/)**
> *(點擊上方連結或複製網址，即可在瀏覽器查看/安裝本實驗室的實時策略收益數據)*

---

這是一個基於 Python 與 Alpaca API 的自動化美股量化交易系統。本系統支援 **「多頭與空頭策略雙向並行」**，採用 **「虛擬子帳戶」** 分離數據，並使用 GitHub Actions 實現 100% 雲端無伺服器託管，每天定時執行交易判定，並將交易數據同步發布至公開的 PWA 儀表板網頁。

系統內部時間已全數同步為 **台灣標準時間 (Asia/Taipei, UTC+8)**，確保交易日誌與儀表板上的時間戳記與您的日常生活完美對齊。

---

## 📈 雙向交易策略具體邏輯與操作

本系統在同一個 Alpaca 帳戶下，將資金平分為兩個獨立的虛擬子帳戶（各 $50,000 美元），並行運行以下兩套策略：

### 1. 🟢 多頭子帳戶策略：均線 + 6% 移動止損
* **交易標的**：`SPY` (S&P 500 指數 ETF)
* **初始資金**：**$50,000 USD**
* **買入條件 (黃金交叉)**：
  * 當 10 日均線（SMA 10）**向上突破** 30 日均線（SMA 30）時，判定趨勢轉多。
  * 機器人會以子帳戶目前可用現金的 **98%** 市價買入 `SPY`。
* **賣出條件一：趨勢反轉 (死亡交叉)**：
  * 當 10 日均線**向下跌破** 30 日均線時，全倉賣出平倉。
* **賣出條件二：崩盤避險 (6% 移動止損)**：
  * 持股期間，程式會自動監控股價的「歷史最高價」。
  * 若最新市價較最高價**回檔跌幅達 6%**，則不論均線是否交叉，**強制執行移動止損賣出**，退回現金安全避險。
* **歷史績效 (2019 - 2026)**：累積回報率 **+92.28%**，最大回撤 (MDD) **-15.44%**。

### 2. 🔴 空頭子帳戶策略：保守型熊市做空
為了在不承擔高風險借券利息與無限虧損的前提下進行做空，本策略採用買入「反向 ETF」的形式進行。
* **監控對象**：`SPY` (S&P 500 指數 ETF)
* **交易標的**：`SH` (S&P 500 **1倍反向 ETF**。當 SPY 跌 1% 時，SH 會漲 1%)
* **初始資金**：**$50,000 USD**
* **做空進場條件 (雙重過濾)**：
  1. **熊市過濾**：`SPY` 當前價格必須**低於 200 日均線 (SMA 200)**（確認大盤進入長期熊市，牛市中 100% 空倉守候）。
  2. **死叉確認**：在熊市背景下，SPY 的 10 日均線**向下跌破** 30 日均線（死亡交叉，確認跌勢啟動）。
  * **操作**：雙重條件成立時，子帳戶全額（可用現金的 98%）買入 `SH`。紀錄買入時的 `SH` 價格，並將此價格設為初始「最高價」。
* **做空退場與防禦條件 (滿足任一即平倉)**：
  1. **3% 移動止損 (Trailing Stop Loss)**：當 `SH` 價格自持股最高點**回撤達 3%**（代表大盤強勢反彈），立刻平倉止損，退回現金。
  2. **趨勢反轉**：當大盤 `SPY` 出現黃金交叉（SMA 10 突破 SMA 30），代表空頭告一段落，立刻平倉退場。
* **歷史績效 (2019 - 2026)**：長期牛市中極度克制（僅交易 4 次），成功將做空總損耗控制在僅 **-6.98%**，最大回撤僅 **-11.79%**，在大崩盤時提供極佳的對沖保護。

---

## 🎨 PWA 行動裝置安裝與圖示設計
儀表板已完全支援 PWA 規範，可在 iOS 和 Android 裝置上「加入主畫面」成為獨立 App 運行，並擁有專屬的桌面 Icon：

* **圖示設計理念**：採用日系吉卜力手繪風格。在清爽的薄荷綠與奶油白漸層背景中，一枚圓潤的金幣上方冒出一片翠綠的新芽。象徵量化交易實驗室「穩健投資、財富自然發芽茁壯」的綠色金融精神。
* **行動裝置極致體驗**：在 iPhone 下啟用了黑系全螢幕模式（Black-Translucent），加入主畫面後打開將會**隱藏 Safari 網址列與控制項**，媲美原生 App 質感。

---

## 📂 專案結構
```text
├── .github/workflows/
│   └── auto_trader.yml         # 整合型工作流：每晚 10:00 自動執行雙向策略並發布網頁
├── my_quant_lab/               # 量化實驗室主資料夾
│   ├── keys.py                 # 本地 API 金鑰 (已在 .gitignore 中忽略，防洩露)
│   ├── config.py               # 交易參數設定 (如標的、API 網址)
│   ├── sma_backtest.py         # 多頭均線策略回測腳本
│   ├── comparative_backtest.py # 多策略對比回測腳本
│   ├── conservative_short_backtest.py # 保守做空策略回測腳本
│   ├── alpaca_sma_trailing_trader.py  # 多頭交易機器人實時程式
│   ├── alpaca_conservative_short_trader.py # 空頭交易機器人實時程式
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
