# 🍙 飯糰碳足跡辨識系統（2026 RiceBall AR）

一套基於純前端 Web 技術與 **ArUco 標記辨識** 的食材碳足跡互動系統。使用者無須安裝任何 App，只要以手機或電腦瀏覽器開啟網頁並掃描實體食材圖卡（單張或多張皆可），即可即時在鏡頭畫面中標示食材名稱與個別碳足跡，並以顯眼的浮動面板顯示**總碳足跡當量（mg CO₂e）**與詳細成分清單。

---

## ✨ 功能特色

- **即時多卡掃描與加總**：支援同時辨識 1 張或多張食材 ArUco 圖卡，自動去重並即時計算總碳足跡當量（`mg CO₂e`，達 `1,000 mg` 以上同步換算 `g CO₂e`）與總份量（`g`）。
- **高對比畫面 HUD**：掃描到圖卡時，於鏡頭畫面下方自動彈出高對比總碳足跡面板，適合教學展示、市集體驗與活動互動。
- **高穩定度辨識引擎**：
  - 採用 **同幀多尺度自適應二值化**（3 組視窗參數共用灰階圖），改善鏡頭靜止或反光時漏偵測問題。
  - 設定 `maxHammingDistance: 5` 並搭配 `CO2e.json` 白名單過濾，有效消除背景雜訊誤判（如幽靈圖卡）。
  - 內建 **全畫面移動偵測 + 單卡局部區域比對**，靜止時穩定防閃爍、切換圖卡時即時清除舊卡不殘留。
- **行動裝置直橫向自動適配**：動態同步 `<canvas>` 與鏡頭串流寬高，手機由直向旋轉至橫向時畫面維持原比例不變形。
- **零相依、純靜態自託管**：`cv.js` 與 `aruco.js` 已本地自託管，無須編譯打包（No Build Step）、不依賴外部 CDN。

---

## 📁 專案結構

```text
2026_RiceBallAR/
├── index.html          # 主程式（鏡頭串流、ArUco 辨識、HUD 與清單介面）
├── CO2e.json           # 食材資料與碳足跡當量設定檔（對應 ArUco ID 1～15）
├── lib/
│   ├── cv.js           # 自託管 js-aruco2 影像處理核心
│   └── aruco.js        # 自託管 js-aruco2 ArUco 偵測器（內建 ARUCO_MIP_36h12 字典）
├── toPrint/
│   ├── 列印用.pdf      # 整合版食材圖卡列印檔
│   ├── 圖片1.png ～ 圖片15.png  # 個別食材圖卡圖檔
│   └── 封面─*.png      # 圖卡封面設計檔
└── README.md
```

---

## 🍱 食材碳足跡對照表（`CO2e.json`）

使用字典：**`ARUCO_MIP_36h12`**（6×6 內部編碼、36 bits）

| ArUco ID | 食材編號 | 食材名稱 | 份量（g） | 碳足跡當量（mg CO₂e） |
| :---: | :---: | :--- | :---: | :---: |
| 1 | `FM001` | 米(後壁仕安-主婦聯盟) | 15 | 616.35 |
| 2 | `FM002` | 米(西螺-三好米) | 15 | 267.24 |
| 3 | `FM003` | 牛蒡香鬆(將軍農會) | 0.5 | 12.327 |
| 4 | `FM004` | 牛蒡香鬆(台南農會) | 0.5 | 14.3815 |
| 5 | `FM005` | 鮪魚罐頭 | 1 | 70.5056 |
| 6 | `FM006` | 玉米罐頭 | 1 | 239.0234 |
| 7 | `FM007` | 鮭魚鬆 | 0.5 | 39.135 |
| 8 | `FM008` | 肉鬆(新東陽) | 0.5 | 8.218 |
| 9 | `FM009` | 雞肉丁 | 2 | 12.838 |
| 10 | `FM010` | 毛豆(新市) | 1 | 2.751 |
| 11 | `FM011` | 毛豆(高雄) | 1 | 28.763 |
| 12 | `FM012` | 炒蛋(卜蜂) | 1 | 6.419 |
| 13 | `FM013` | 滷蛋(東菜市場) | 1 | 10.566 |
| 14 | `FM014` | 海苔(日本) | 0.01 | 25.82001 |
| 15 | `FM015` | 海苔(韓國) | 0.01 | 0.76983 |

---

## 🚀 快速開始（本機測試）

> **注意**：瀏覽器安全規範要求除 `localhost` 以外，必須透過 **HTTPS** 才能存取裝置鏡頭；且透過本機伺服器開啟才能以 `fetch()` 即時載入 `CO2e.json`。

### 方法一：Python 內建伺服器
```bash
python -m http.server 8000
```
接著以瀏覽器開啟 `http://localhost:8000`。

### 方法二：VS Code Live Server
安裝 **Live Server** 擴充套件後，對 `index.html` 點右鍵選擇 **Open with Live Server**。

---

## 🌐 部署方式

本專案為純靜態網站，**不需要任何 build 或打包動作**。

### 選項 A：GitHub Pages（最快速，免費內建 HTTPS）
1. 將本專案推送至 GitHub Repository。
2. 前往 Repository 的 **Settings → Pages**。
3. **Source** 選擇 **Deploy from a branch**，分支選擇 `main`（目錄選 `/ (root)`）後儲存。
4. 稍待約 1 分鐘即可透過 `https://<你的帳號>.github.io/<專案名稱>/` 直接在手機開啟使用。

### 選項 B：AWS EC2 / 自架主機
1. 將 `index.html`、`CO2e.json` 與 `lib/` 上傳至網頁伺服器根目錄（例如 `/var/www/html/`）。
2. **務必設定網域與 HTTPS 憑證**（建議使用 **Caddy** 自動申請 Let's Encrypt 憑證，或 **Nginx + Certbot**）。
3. 確認 EC2 Security Group 已開放 Inbound `80 (HTTP)` 與 `443 (HTTPS)`。

---

## ⚙️ 自訂資料與參數微調

### 修改食材資料
直接編輯 `CO2e.json`（JSON Key 對應 ArUco 標記編號）：
```json
{
  "1": {
    "foodId": "FM001",
    "name": "米(後壁仕安-主婦聯盟)",
    "weight_g": 15,
    "carbon_mg": 616.35
  }
}
```
> 網頁透過伺服器開啟時會即時讀取最新的 `CO2e.json`；若也會以 `file://` 直接雙擊開啟，建議同步更新 `index.html` 內的 `FALLBACK_FOOD_DATABASE`。

### 調整辨識靈敏度（位於 `index.html`）
| 參數 | 預設值 | 說明 |
| :--- | :---: | :--- |
| `maxHammingDistance` | `5` | ArUco 位元容錯數（`ARUCO_MIP_36h12` 安全上限為 `5`；若背景雜訊偏多可調降為 `3`～`4`） |
| `STATIC_HOLD_MS` | `450` | 鏡頭靜止時單張圖卡的防閃爍保留時間（毫秒） |
| `GLOBAL_MOTION_THRESHOLD` | `12` | 判定鏡頭移動中的全畫面平均亮度變化門檻（`0`～`255`） |
| `LOCAL_PATCH_THRESHOLD` | `18` | 判定圖卡已離開原位置的局部區域亮度變化門檻（`0`～`255`） |

---

## 🛠️ 技術棧與致謝

- **前端技術**：HTML5、CSS3、Vanilla JavaScript、Canvas 2D API、MediaDevices (`getUserMedia`)
- **ArUco 辨識核心**：[js-aruco2](https://github.com/damianofalcioni/js-aruco2)（衍生自 Juan Mellado 的 [js-aruco](https://github.com/jcmellado/js-aruco)，採 MIT 授權）
