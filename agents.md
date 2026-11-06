# Agent Development Guidelines: 魏晉弘醫師部落格互動知識庫

本文件為 AI Agent（如 Claude, ChatGPT, Cursor 等）參與本專案開發、內容擴充與樣式維護時必須嚴格遵守的規範與指令集。

---

## 1. 專案目的與架構 (Project Purpose & Overview)

* **專案目的**：將魏晉弘醫師部落格 ([Chinhungwei Blog](https://chinhungwei.blogspot.com/)) 的所有小兒外科衛教文章、臨床經驗與醫學內容結構化，建立一個具備「小兒外科四大主題分類動態過濾」與「全文搜尋功能」的單頁互動式 Web 應用庫。
* **核心哲學**：模組化、資料與 UI 分離。AI 新增文章或改版 UI 時，必須確保舊有文章索引與前端渲染邏輯不中斷。

---

## 2. 專案目錄結構與檔案命名規範 (Directory & Naming Conventions)

所有 AI 在新增、修改檔案時，必須遵守以下目錄層級與命名邏輯：

```text
/
├── AGENTS.md                  # 本開發規範文件
├── index.html                 # 主頁面（四大主題分類展示與搜尋 UI 入口）
├── css/
│   └── style.css              # 全局樣式（醫療溫和衛教風格）
├── js/
│   └── app.js                 # 頁面互動邏輯、JSON 資料讀取與動態過濾
├── data/
│   └── articles.json          # 爬取並結構化後的文章主資料庫
├── scripts/
│   └── crawler.py             # 爬取 Blogspot 文章並自動分類的腳本
└── README.md                  # GitHub 專案說明與部署說明頁
```

### 檔案命名與變數規範
* **JSON 資料 key 命名**：使用小寫底線 (`snake_case`)，如 `article_id`, `publish_date`, `category`。
* **CSS 類別命名**：採用 BEM 規範或語意化命名（如 `.article-card`, `.nav-btn.active`, `.search-box`）。

---

## 3. 資料結構與四大領域特定分類規範 (Data Schema & Domain Classification)

當 AI 被要求「新增文章」、「重新分類」或修改爬蟲規則時，必須嚴格依照 `data/articles.json` 的 JSON Schema 格式進行擴充：

### `data/articles.json` Schema
```json
[
  {
    "id": "post-001",
    "title": "文章標題",
    "url": "https://chinhungwei.blogspot.com/202X/XX/example.html",
    "date": "YYYY-MM-DD",
    "category": "小兒微創與一般手術", 
    "tags": ["小兒疝氣", "單孔腹腔鏡", "鈕扣型胃造瘻", "術後照顧"],
    "summary": "120字以內的文章核心摘要",
    "content": "純文字內文（已去除 HTML 標籤），供關鍵字檢索使用"
  }
]
```

### 醫療領域四大專屬分類規範 (Medical Domain Categories)
根據魏晉弘醫師（小兒外科主治醫師）部落格內容，所有文章必須精準歸類至以下**四大領域主題**（若文章涉及特定疾病或細項，可於 `tags` 中延伸新增）：

1. **`小兒微創與一般手術`**（包含：腹腔鏡腹股溝疝氣手術、單孔腹腔鏡、總膽管囊腫、鈕扣型胃造瘻、舌繫帶處理等）
2. **`無肛症及泄殖腔異常`**（包含：無肛症、馬龍式順行性灌腸手術、腸道衛教與照護等）
3. **`臨床案例與衛教隨筆`**（包含：衛教問與答、家長常見疑慮說明、診間衛教心得等）
4. **`醫學研究與演講記錄`**（包含：學術研討會分享、醫學生與住院醫師教學、個人經歷與演講紀錄等）

---

## 5. 網頁視覺與 UI 風格指引 (UI/UX & Styling Guidelines)

當 AI 生成或修改 HTML/CSS 時，必須遵循以下風格規則：

* **設計風格**：專業、溫暖且易於閱讀的醫療衛教風格 (Clean Medical & Humanistic Style)。
* **配色方案 (Color Palette)**：
  * 主色 (Primary)：`#0284C7` (溫和醫療藍 Sky Blue)
  * 輔色 (Secondary)：`#0D9488` (安心綠 Teal)
  * 背景色 (Background)：`#F8FAFC` (柔和淺灰白)
  * 卡片背景 (Card BG)：`#FFFFFF` (純白)
  * 文字主要色 (Text Primary)：`#0F172A` (深藍黑)
  * 文字次要色 (Text Muted)：`#64748B` (灰)
* **版面配置**：
  * **Header**：魏晉弘醫師部落格知識庫標題與「關鍵字搜尋列 (Search Input)」。
  * **Category Navigation**：橫向主題切換按鈕，支援「全部文章」及「四大主題分類」過濾。
  * **Main Content**：以網頁卡片 (Grid Cards) 展示文章標題、發布日期、分類標籤、摘要與「閱讀全文」原始連結。
  * **Footer**：顯示版權聲明與醫療免責文字。
* **響應式設計 (RWD)**：必須支援 Mobile First，確保家長使用手機時能順暢讀取與切換分類。

---

## 6. AI 修改與新增程式碼規範 (AI Coding Rules)

當 AI 助手協助更新本專案時，**必須**檢查並遵守以下條款：

1. **零依賴/靜態託管優先**：前端使用 Native JavaScript (ES6+) 與原生 CSS，無須過重的打包工具（如 Webpack/Vite），確保專案能直接透過 GitHub Pages 進行靜態託管。
2. **單一事實來源 (Single Source of Truth)**：網頁上所有呈現的文章與分類資料，必須動態由 `data/articles.json` Fetch 載入，禁止寫死 (Hardcode) 於 `index.html` 中。
3. **語意化 HTML 結構**：使用 `<header>`, `<main>`, `<nav>`, `<section>`, `<article>`, `<footer>` 等語意化標籤。
4. **醫療免責警示**：頁尾必須保留警語：*「本網站衛教內容僅供參考，不能替代專業醫師現場診斷與治療。」*

---

## 7. 自動化擴充與維護 SOP (Workflow for Next Pages/Updates)

當使用者未來輸入「*請幫我抓取最新的部落格文章*」或「*請新增一個文章排序功能*」時，AI 應執行以下 SOP：

1. **運行爬蟲腳本**：執行 `python scripts/crawler.py` 更新 `data/articles.json`。
2. **自動類別校對**：確認新文章符合第三章定義的四大主題分類。
3. **樣式與元件複用**：維持 `style.css` 已定義的色彩與 UI Class，確保全站視覺體驗一致。
4. **版控與說明更新**：同步更新 `AGENTS.md` 的版本紀錄與 `README.md`。