# 巨量下拉選單 · 前端全量效能實驗

近 4 萬筆真實政府機關的下拉選單，要讓使用者打字即搜（機關名稱、機關代碼、中間關鍵字都要命中）。這個 repo 記錄實測與可互動的展示頁。

## 線上展示（GitHub Pages）

**➜ https://bryanhsiao.github.io/tomselect-massive-dropdown/**

- **首頁**：<https://bryanhsiao.github.io/tomselect-massive-dropdown/>
- **三階段載入動畫**：<https://bryanhsiao.github.io/tomselect-massive-dropdown/loading-stages-demo.html> — 逐步拆解「先出畫面 → 背景抓全量 → 灌入可搜」，零依賴、純 CSS 動畫。
- **真實資料互動 Demo**：<https://bryanhsiao.github.io/tomselect-massive-dropdown/tomselect-demo.html> — 直接 `fetch` 近 4 萬筆真實政府開放資料再前端解析，全量灌進 Tom Select，實際感受虛擬化搜尋（可多選）。

## 結論

| 方案 | 初始化 | 搜尋反應 | 判定 |
|---|---|---|---|
| select2 灌 data 全量 | 2,090 ms | 563 ms | 初始化凍結約 2 秒 |
| Choices.js 全量 | 32,001 ms | 60,067 ms | 瀏覽器無回應 |
| select2 ajax（server search） | 44 ms | 133 ms | ✅ 流暢 |
| **Tom Select（虛擬化 · 全量）** | **51 ms** | **134 ms** | ✅ 流暢 |

（合成壓測 · 60,000 筆 · Chrome，刻意比真實約 4 萬筆更嚴苛。真實資料互動 demo 用 39,553 筆。）

**採用**：前端全量載入 + Tom Select 虛擬化（`maxOptions: 50`、`searchField: ['text','id']` 雙欄 substring 搜尋）。全量塞進 DOM 會凍結瀏覽器；虛擬化讓任何時刻最多只渲染 50 筆。

## 正式環境

展示頁直接 `fetch` 本 repo 內的 `agencies-active.txt`（模擬正式環境的全量端點）。正式環境資料由 Domino XAgent 全量端點供應（Extension Library `<xe:restService>`）。

**快取策略**：主線走「三階段背景載入 ＋ 前端 `localStorage` 快取」——同一瀏覽器第二次起免重抓、秒開。伺服器端 `applicationScope` 共用快取（第一次建置約 8 秒、之後約 0.4 秒，全 app 共用一份）則留給「同時多人、在意伺服器負載」時再加。後端實作與內網位址不在本 repo。

## 資料來源

`agencies-active.txt`：政府開放資料「地址簿公開資訊」現行有效機關，39,553 筆，格式為每行「機關代碼＜TAB＞機關名稱」。
來源：<https://www.good.nat.gov.tw/regcenter/download>

## 技術

純靜態、零建置。Tom Select 2.3 由 CDN 載入；動畫頁零依賴。
