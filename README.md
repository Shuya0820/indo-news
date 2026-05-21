# 🇮🇩 印尼每日新聞摘要 v3（Gemini + 個人化分析）

每天自動從 Google News 抓取印尼地區新聞，**用 Google Gemini 免費 API 翻譯成中文、寫摘要，並結合「印尼手遊 PM」視角寫「對你的意義」分析**，保留最近 14 天歷史。零成本運行。

## ✨ 功能特色

- 🌏 **中文翻譯與摘要** —— 每則新聞顯示中文標題＋1 句重點摘要，原文連結仍在
- 💡 **「對你的意義」個人化分析** —— Gemini 結合印尼牌類手遊 PM 視角，為每則新聞寫 1-2 句策略意涵
- ⭐ **「今日必看」精選** —— Gemini 自動挑出當天 5-8 則最值得讀的，網頁頂端顯眼呈現
- 📅 **14 天歷史** —— 頂端日期 tabs 可切換查看過去兩週
- 🔗 **公開可分享網址** —— GitHub Pages 提供的網址直接貼給同事就能看
- 💰 **完全免費** —— Gemini 2.5 Flash 免費版每天 250 次請求，腳本只用 1 次
- 💬 **Slack 推播（選用）** —— 若有需要可開啟

## 📁 檔案結構

```
indo-news/
├── scraper.py              ← 主程式（要改關鍵字看這個）
├── requirements.txt        ← Python 套件
├── README.md               ← 本檔案
├── index.html              ← 網頁前端（不太需要改）
├── data.json               ← 14 天資料（自動產生與更新）
└── .github/workflows/
    └── daily.yml           ← 自動排程（要改時間看這個）
```

---

## 🚀 部署步驟

### 步驟 1：建 GitHub Repo 並上傳檔案

1. [github.com](https://github.com) → 右上 `+` → `New repository`
2. 名字 `indo-news`，設為 **Public**（私 repo 用 Pages 要付費），勾 README → Create
3. `Add file` → `Upload files` → 把這個資料夾**所有檔案**拖進去（含 `.github` 隱藏資料夾）→ Commit

### 步驟 2：開啟 GitHub Pages

1. Repo `Settings` → 左側 `Pages`
2. Source 選 `Deploy from a branch` → Branch 選 `main` 根目錄 → Save
3. 等 1-2 分鐘，頂端會出現你的網址：`https://你的帳號.github.io/indo-news/`
4. **這個網址就是你要分享給同事的**——公開可看、不需要 GitHub 帳號

### 步驟 3：申請 Gemini API Key（免費）

1. 用 Google 帳號登入 [aistudio.google.com](https://aistudio.google.com)
2. 左上角點 `Get API key` 按鈕
3. `Create API key` → 選 `Create API key in new project`（或選現有專案也行）
4. **複製產生的 API key**（格式類似 `AIzaSy...`）

> 💡 **完全免費，不用信用卡。** Gemini 2.5 Flash 免費版每天 250 次請求、每分鐘 10 次。你的腳本一天只用 1 次（批次翻全部新聞），永遠用不完。

### 步驟 4：把 API Key 加到 GitHub Secrets

1. Repo `Settings` → 左側 `Secrets and variables` → `Actions`
2. 綠色按鈕 `New repository secret`
3. Name: `GEMINI_API_KEY`
4. Secret: 貼上剛複製的 key
5. `Add secret`

> 💡 沒設這個的話腳本還是會跑，只是不會翻譯，標題會顯示原文印尼文。

### 步驟 5：第一次手動跑

1. Repo `Actions` 分頁 → 左側點 `每日印尼新聞更新`
2. 右側 `Run workflow` → 綠色按鈕 `Run workflow`
3. 等 30 秒～1 分鐘
4. 跑完去重整你的 Pages 網址，新聞報告就出來了

之後**每天台北時間早上 7 點**自動更新，不用再管。

---

## 💰 成本

**全部免費**：
- GitHub Actions（Public repo）：免費
- GitHub Pages：免費
- Gemini API（免費版）：每天 250 次請求，你只用 1 次

唯一成本：你的時間（設定一次約 15 分鐘）

---

## ⚙️ 自訂方式

### 改「對你的意義」的觀點背景
打開 `scraper.py`，找到 `translate_batch` 函式裡的 `prompt` 變數：

```python
讀者背景：在台灣公司做印尼市場手機遊戲產品（牌類撲克/賭場機台類），關注：
- 玩家儲值習慣與通路（GoPay/DANA/OVO/ShopeePay/Pulsa 電信儲值）
- ...
```

改成你想要的背景設定，Gemini 就會用新角度寫分析。例如：
- 想換成「行銷策略」視角 → 改成關注「在地行銷活動、KOL、檔期排程」
- 想要更技術性視角 → 強調「玩家行為數據、留存指標、A/B 測試」
- 想分享給 Noah、Savira 看 → 改成「企劃團隊視角」

### 改新聞類別/關鍵字
打開 `scraper.py`，最上面 `CATEGORIES` 字典：
```python
"新類別名": {
    "icon": "📊",
    "query": "keyword1 OR keyword2 OR \"含空格的詞\"",
    "desc": "簡短說明",
},
```

### 改執行時間
`.github/workflows/daily.yml`：
```yaml
- cron: '0 23 * * *'   # UTC 23:00 = 台北 07:00
```
格式 `分 時 日 月 週`。例如台北中午 12 點 = UTC 04:00：`'0 4 * * *'`。

### 改每類顯示則數 / 保留天數
`scraper.py`：
```python
ITEMS_PER_CATEGORY = 8    # 每類顯示則數
KEEP_DAYS = 14            # 歷史保留天數
```

### 換更高品質的翻譯模型
`scraper.py` 找 `translate_batch` 函式裡的：
```python
model="gemini-2.5-flash",
```
可以換成：
- `gemini-2.5-flash-lite`：速度更快，免費額度更高（1,000 次/天），品質稍低
- `gemini-2.5-pro`：品質最高，但免費版只能 50 次/天

對你的用量來說 `gemini-2.5-flash` 最平衡。

---

## 🔔（選用）加上 Slack 推播

如果之後想加 Slack 通知：

1. 到 [api.slack.com/apps](https://api.slack.com/apps) → `Create New App` → `From scratch`
2. App 名字隨意 → 選你的 workspace → Create
3. 左側 `Incoming Webhooks` → 右側開關打開
4. 下方 `Add New Webhook to Workspace` → 選頻道 → Allow
5. 複製產生的 Webhook URL
6. 回 GitHub repo `Settings` → `Secrets and variables` → `Actions` → 新增 Secret
   - Name: `SLACK_WEBHOOK_URL`
   - Secret: 貼上 webhook URL
7. （選用）再加一個 Variable（注意是 Variables 不是 Secrets）：
   - Name: `PAGE_URL`
   - Value: 你的 Pages 網址，這樣 Slack 訊息會有「看完整報告」連結

下次自動跑就會推播了。

---

## ❓ 常見問題

**Q：上傳檔案後 Actions 沒跑？**
A：第一次推上去後 GitHub 會詢問是否啟用 workflows，要點同意。或手動觸發一次。

**Q：翻譯出現亂碼或失敗？**
A：去 `Actions` 看執行記錄。常見原因：
- API Key 沒設或設錯 → 重新檢查 Secret
- 超過免費版限額（不太可能，因為一天只用 1 次）
- Gemini 服務暫時異常 → 隔天再跑
- 不管哪種，腳本會自動 fallback 顯示原文，不會壞掉

**Q：可以分享網址給沒 GitHub 帳號的同事嗎？**
A：可以。GitHub Pages 網址是完全公開的（除非把 repo 改成私人），任何人點開都能看。網址永久有效。

**Q：14 天以前的資料會怎樣？**
A：自動刪除。`data.json` 每次只保留最新 14 天。

**Q：Gemini 免費版有什麼限制要注意？**
A：兩個小提醒：
- **資料可能會被用於改進模型**（這是免費版的代價）。對公開新聞翻譯沒影響，但別丟公司機密資料進去
- **EU/UK/瑞士地區不能用免費版**。你在台灣沒影響

**Q：我想自己加新功能/客製化？**
A：所有邏輯都在 `scraper.py` 裡，每個函式都有註解。HTML 在 `index.html`，純 HTML+CSS+JS 沒用框架，好改。

---

## 📝 授權與致謝

- 資料來源：Google News，請尊重各原始新聞來源著作權
- 翻譯：Google Gemini（2.5 Flash 模型）
- 自由使用、修改
