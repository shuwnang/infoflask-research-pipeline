# INFOFLASK 統一設計規格 v2（2026-09-02）

全站 10 頁（index/scores/holdings/sub/short3d/shortbk/performance/market/reports/news*）共用。
**真相源：`holdings_common.py`** —— `TOKENS_CSS`（色彩+捲軸+selection）、`MEDIA_CSS`（≤768px 響應式層）、`FOOTER_TEXT`（標準免責）。各產生器以 `__COMMON_CSS__`/`__MEDIA_CSS__` 佔位符（或直接 import）注入；新聞線 `news/edition/web.py` 亦 import 同一份。**改色一律改 holdings_common，禁止在各頁散寫 hex。**

## 色彩 Tokens（完整集，全頁一致）

v1 基礎集不變（--bg #0A0C10 / --surface #12151C / --surface-2 #171B24 / --surface-3 #1D2330 / --border #232936 / --border-strong #2E3646 / --text #E7EBF2 / --text-2 #9AA4B2 / --text-3 #5C6675 / --accent #E8C158 / --accent-soft / --link #6CA8FF / --up #F0544C / --dn #3FBF7F …），v2 收編新增：

| Token | 值 | 用途 |
|---|---|---|
| --surface-grad | `#151A23` | 卡片漸層頂（原裸 hex） |
| --row-line | `#1A1F29` | 表格列分隔線（原裸 hex） |
| --sb-thumb / --sb-thumb-h | `#2A3140` / `#38424F` | 捲軸 |
| --grp-hv / --grp-bk / --grp-hf / --grp-hb | `#FF9E97` / `#E8C158` / `#B48CFF` / `#4FD8C8` | 四模型組識別色（評分表表頭/首頁帳本卡） |
| --up-bright / --dn-bright | `#FF7B72` / `#5CD99A` | 亮版漲跌（新聞情緒沿用） |
| --up-bg / --dn-bg / --warn / --risk | 同 v1 | 全頁皆備（v1 時代每頁配備不一） |

台股紅漲綠跌不可對調。新聞線情緒色已歸隊（bull=--up-bright／bear=--dn-bright）。

## 全站統一規範（v2 定案）

- **viewport**：`initial-scale=1.0`；**body**：字體堆疊同 v1＋`-webkit-font-smoothing:antialiased`＋`font-variant-numeric:tabular-nums`＋`min-height:100dvh`＋藍暈背景 `radial-gradient(1200px 600px at 100% -10%, rgba(108,168,255,0.05), transparent 60%)`（scores 為 app-shell 例外：`height:100dvh; overflow:hidden`）
- **Header（每頁）**：sticky top-0 z-20、`rgba(10,12,16,0.88)+blur(12px)`；`.brand`「INFOFLASK」+`.brand-page`「｜{頁名}」（nowrap）；`.site-nav`>10 顆 `.nav-link` 藥丸，順序：首頁/評分表/高波動能/籌碼趨勢/高頻動能/高頻籌碼/績效表/大盤觀測/研究報告/新聞（=holdings_common.NAV_ITEMS，改導覽只改那裡）
- **統計卡**：`.stats-row > .stat > (.lbl/.val/.sub)`；val `1.35rem/800`、padding `12px 20px`、min-width 130px、漸層底（market 舊 `.chips/.chip/.lab` 已收編）
- **表格**：`.table-wrap`（overflow-x:auto、圓角 14、外框）；sticky thead（surface-2 / 11px / 0.08em）；列線 `var(--row-line)`；首頁 mini-table 外包 `.table-scroll`
- **Footer（每頁必備）**：`<footer>` 元素、11.5px、`--text-3`、上邊線 `1px solid var(--border)`，內容含免責句（`FOOTER_TEXT`）；reports/news 已由 div 升級為 `<footer>`
- **響應式**：`MEDIA_CSS`（≤768px：header/nav 縮距、stat 兩欄、表格 padding 縮、`.date-badge` 換行）＋各頁一行頁專屬補充；v1 時代全站 0 條 @media 的缺口已補
- **容器寬**：閱讀頁（reports/news）880px；儀表頁 index/performance 1180px、market 1280px；資料表頁（scores/四持股）全幅
- **`<title>`**：與導覽標籤一致（首頁=INFOFLASK；不得帶日期）

## 鐵律（沿用 v1 並追加）

1. 只動樣式/模板結構；資料載入、計算、JS 邏輯、欄位、連結 href 一律不碰。
2. f-string 模板內 CSS 大括號 `{{ }}`；共用 CSS 一律走佔位符 replace（佔位符本身無大括號）。
3. 改完必跑產生器＋資料快照比對（可見文字與 `<script>` JSON 須等值，白名單差異除外）。
4. 排名/名次類排序必須決定性（tie-break 末位加代號；2026-09-02 修正評分表平手隨機序）。
5. 台股紅賺綠虧不可反。
