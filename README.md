# 四萬筆下拉選單 · 前端全量效能實驗

機關代碼表達 6～8 萬筆的下拉選單，要讓使用者打字即搜（機關名稱、機關代碼、中間關鍵字都要命中）。這個 repo 記錄實測與可互動的展示頁。

## 線上展示（GitHub Pages）

- **首頁**：`index.html`
- **三階段載入動畫**：`loading-stages-demo.html` — 逐步拆解「先出畫面 → 背景抓全量 → 灌入可搜」，零依賴、純 CSS 動畫。
- **六萬筆互動 Demo**：`tomselect-demo.html` — 前端即時生成 60,000 筆合成資料，全量灌進 Tom Select，實際感受虛擬化搜尋（可多選）。

## 結論

| 方案 | 初始化 | 搜尋反應 | 判定 |
|---|---|---|---|
| select2 灌 data 全量 | 2,090 ms | 563 ms | 初始化凍結約 2 秒 |
| Choices.js 全量 | 32,001 ms | 60,067 ms | 瀏覽器無回應 |
| select2 ajax（server search） | 44 ms | 133 ms | ✅ 流暢 |
| **Tom Select（虛擬化 · 全量）** | **51 ms** | **134 ms** | ✅ 流暢 |

（60,000 筆合成資料 · Chrome）

**採用**：前端全量載入 + Tom Select 虛擬化（`maxOptions: 50`、`searchField: ['text','id']` 雙欄 substring 搜尋）。全量塞進 DOM 會凍結瀏覽器；虛擬化讓任何時刻最多只渲染 50 筆。

## 正式環境

展示頁的資料為前端合成、無後端。正式環境資料由 Domino XAgent 全量端點供應（Extension Library `<xe:restService>`，伺服器端 `applicationScope` 快取；第一次建置約 8 秒、之後約 0.4 秒）。後端實作與內網位址不在本 repo。

## 技術

純靜態、零建置。Tom Select 2.3 由 CDN 載入；動畫頁零依賴。
