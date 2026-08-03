# 📊 My Quant Trader Live Dashboard (純台股實戰本金量化交易儀表板)

> 📊 **實時 PWA 儀表板 (Live PWA Dashboard App)**：  
> **👉 [https://handpine.github.io/My_QuantTrader_Dashboard/](https://handpine.github.io/My_QuantTrader_Dashboard/)**  
> *(點擊上方連結或複製網址，即可在手機與瀏覽器開啟/安裝本實驗室的實用台股策略收益與最新決策數據)*

---

## 🇹🇼 策略架構總覽 (Master Blueprint Architecture)

本儀表板展示經過 100% 全歷史數據審計之 **「組合二 + 方法 A + 模型 2 (風險平坦度 Risk Parity) + 70/30 多空劃分」** 終極風控波段架構：

```mermaid
graph TD
    Capital[100萬虛擬帳戶 (2026/08/03今日啟動)] --> Long[70% 多頭波段帳戶 (70萬)]
    Capital --> Short[30% 空頭防禦帳戶 (30萬)]
    
    Long --> W1[00713.TW 元大高息低波 (35% 權重 / 20/60 SMA + 2.5x ATR)]
    Long --> W2[006208.TW 富邦台50 (25% 權重 / 50/150 SMA + 3.0x ATR)]
    Long --> W3[00878.TW 國泰高股息 (25% 權重 / 20/60 SMA + 2.5x ATR)]
    Long --> W4[2330.TW 台積電 (15% 權重 / 50/150 SMA + 4.0x ATR)]
    
    Short --> Savings[100% 1.75% 高利活存/公債 (避開反1損耗)]
    
    Long -- 平倉獲利 --> MethodA{方法 A 利潤撥轉保本}
    MethodA -- 原始底金 --> Long
    MethodA -- 超額淨利潤 --> Savings
```

---

## ⏰ 每日兩大實戰下單觸發時程 (Daily Automation Workflow)

本系統於 GitHub Actions 設定為每日台股交易日 **2 大高效率定點觸發**：

1. 🏆 **13:15 TST (UTC 05:15)**：**【最關鍵當天下單決策點】** 距離 13:30 收盤前 15 分鐘，K 線 99.5% 定型，完全過濾盤中洗盤雜訊，提供 15 分鐘可在券商 App 限價/市價撮合下單時間窗！
2. 🌙 **14:00 TST (UTC 06:00)**：**【盤後最終結算】** 更新當日終值，並自動增量發布至本 Live Dashboard。

---

## 🏆 策略量化評估指標對照 (Quant Evaluation Metrics)

| 量化評估指標名稱 | 1.5年盲測集數據 (2025-2026) | 7年訓練集數據 (2018-2024) | 幾何與風險意涵解析 |
| :--- | :--- | :--- | :--- |
| 🏆 **年化 Calmar 比率** | **`1.49`** 🏆 | **`1.14`** | 1年操作期內承受回撤的投資效率（勝過台積電單壓 0.65） |
| 🏆 **累積 Calmar 比率** | **`2.35`** | **`9.34`** 🏆 | 長線累積總獲利相對最大歷史回撤的幾何比值 |
| 🛡️ **索提諾比率 (Sortino)** | **`0.88`** | **`1.12`** 🏆 | 將水面上暴衝山峰塗白削平，專門精確測量水下坑洞下行風險 |
| 📈 **夏普比率 (Sharpe)** | **`0.88`** | **`0.90`** | 總波動率相對超額報酬之比例（含雙向上行與下行波動） |
| 📉 **最大歷史回撤 (MDD)** | **`-10.24%`** 🏆 | **`-5.85%`** 🏆 | 策略歷史最慘虧損控制（將台積電 -44.8% 回撤砍至 1/8） |
| 🎯 **歷史預期勝率** | **`> 75.0%`** | **`76.8%`** 🏆 | 1年滾動視角下實現獲利平倉的歷史極大化機率 |

---

## 📱 PWA 跨平台安裝指南

本儀表板支援 PWA (Progressive Web App)，可直接安裝至 iPhone / Android / 電腦桌面作為獨立 App 使用：

### 📱 iOS (iPhone / iPad)
1. 在 Safari 瀏覽器開啟 **[Live Dashboard 網址](https://handpine.github.io/My_QuantTrader_Dashboard/)**。
2. 點擊下方工具列的 **「分享」** 圖示 (方塊加上箭頭)。
3. 選擇 **「加入主畫面」 (Add to Home Screen)**。

### 🤖 Android (安卓手機)
1. 在 Chrome 瀏覽器開啟 **[Live Dashboard 網址](https://handpine.github.io/My_QuantTrader_Dashboard/)**。
2. 點擊右上角選單 `⋮`。
3. 選擇 **「安裝應用程式」** 或 **「新增至主畫面」**。

---

## 🔒 隱私與安全聲明

* 本公開倉庫僅存放去敏感化的**公開策略數據 (`public_data.json`)**、**前端 PWA 靜態網頁 (`index.html`)** 與說明文件。
* 核心交易邏輯與金鑰嚴格保管於私有倉庫 `Handpine/My_QuantTrader` 中。
