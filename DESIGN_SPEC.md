# INFOFLASK 統一設計規格 v1（2026-07-22）

全站 7 頁（index/scores/holdings/realized/performance/trend/market）共用。
目標：現代、簡潔、質感。**只改設計，資料內容/JS 邏輯/連結一律不動。**

## 色彩 Tokens

| Token | 值 | 用途 |
|---|---|---|
| --bg | `#0A0C10` | 頁面背景（深近黑） |
| --surface | `#12151C` | 卡片/表格底 |
| --surface-2 | `#171B24` | 表頭/次層/chip 底 |
| --surface-3 | `#1D2330` | hover 底 |
| --border | `#232936` | 邊框（1px） |
| --border-strong | `#2E3646` | hover 邊框 |
| --text | `#E7EBF2` | 主文字 |
| --text-2 | `#9AA4B2` | 次要文字 |
| --text-3 | `#5C6675` | 弱化文字（註記/日期） |
| --accent | `#E8C158` | 金（品牌強調、active、高分）——取代舊 #ffd700 |
| --accent-soft | `rgba(232,193,88,0.10)` | 金底 |
| --link | `#6CA8FF` | 連結/藍強調——取代舊 #79b8ff/#58a6ff |
| --up | `#F0544C` | 台股紅=漲/賺（文字），亮版 `#FF7B72` |
| --up-bg | `#1D0F0E` | 賺錢列底 |
| --dn | `#3FBF7F` | 台股綠=跌/虧（文字），亮版 `#5CD99A` |
| --dn-bg | `#0C1B14` | 虧損列底 |
| --warn | `#F5A524` | 警示橘 |
| --risk | `#F0544C` | 高風險紅 |

漲跌紅綠語意（台股慣例）不可對調。舊色系（#0d1117/#161b22/#21262d/#262c36/#8b949e/#ffd700/#79b8ff/#f85149/#3fb950/#ef4444/#22c55e/#f87171/#4ade80…）全部映射到上表最接近的 token。

## 字體與數字

- `font-family: "Inter","Noto Sans TC","Microsoft JhengHei",system-ui,sans-serif;`
- 全域 `-webkit-font-smoothing: antialiased;`
- 所有數字欄位/大數字加 `font-variant-numeric: tabular-nums;`
- 標題字距 `letter-spacing: 0.02em`；表頭/標籤小字 `11px、letter-spacing 0.08em`（可 uppercase 英文）

## Header（每頁完全一致）

```html
<header>
  <div class="brand">INFOFLASK<span class="brand-page">｜{頁名}</span></div>
  <nav class="site-nav"> …7 鈕，順序：首頁/評分表/模型持股/已實現損益/績效表/排名趨勢/大盤觀測… </nav>
  …（該頁原有的功能元件：日期選單/搜尋/篩選鈕等，照舊保留，只重新上色）
</header>
```

- header：`background: rgba(10,12,16,0.88); backdrop-filter: blur(12px); border-bottom: 1px solid var(--border); position: sticky; top: 0;` padding `12px 22px`
- `.brand`：`17px / 900 / #fff / letter-spacing 0.08em`；`.brand-page`：`13px / 500 / var(--text-3); margin-left 2px`
- `.nav-link`：藥丸形 `border-radius 999px; padding 5px 14px; font-size 13px; color: var(--text-2); border: 1px solid var(--border); background: transparent;`
  - hover：`color: var(--text); border-color: var(--border-strong); background: var(--surface-2);`
  - active：`color: var(--accent); border-color: var(--accent); background: var(--accent-soft); pointer-events: none;`
- **各頁 nav 的 href/順序/active 位置不可變**，只改樣式。

## 元件

- **卡片**：`background: linear-gradient(180deg,#151A23 0%, var(--surface) 100%); border:1px solid var(--border); border-radius:16px; padding:20px 22px; box-shadow:0 10px 30px -18px rgba(0,0,0,0.7);` hover（可點卡片）`border-color: var(--border-strong); transform: translateY(-2px);`
- **表格**：外框 `border:1px solid var(--border); border-radius:14px; overflow:hidden`；表頭 `background: var(--surface-2); color: var(--text-3); 11px; letter-spacing 0.08em;`（sticky 者維持 sticky）；列 `border-bottom:1px solid #1A1F29`；hover `background: var(--surface-3)`；儲存格 padding 維持原有密度
- **按鈕/篩選鈕/切換鈕**：藥丸形、`background: var(--surface-2); border:1px solid var(--border); color: var(--text-2);` active 態用該功能原本的語意色（金/紅/綠/橘）但底用對應 10% 透明版
- **chip/統計膠囊**：同卡片語言縮小版，`border-radius 12px`
- **捲軸**：`width 8px; thumb #2A3140; hover #38424F; track transparent`
- **selection**：`rgba(108,168,255,0.28)`
- **警示 banner**（首頁）：`background: rgba(240,84,76,0.10); border:1px solid rgba(240,84,76,0.55);`
- 圖表 canvas 內的線色/柱色屬於資料語意，維持語意但可映射到本表 token（TAIEX 線→var(--link)、波動柱→var(--warn)、警戒→var(--risk)、分數金/紅/綠→--accent/--up/--dn）

## 鐵律（每個改動者必守）

1. 只動 `<style>` 區塊、inline style、與 header 品牌列 HTML；**資料載入、計算、JS 渲染邏輯、欄位、文案、連結 href、embed/modal 行為一律不碰**。
2. Python f-string 內的 CSS 大括號是 `{{ }}`，改完必須能無錯執行產生器。
3. 改完跑一次產生器，用快照比對資料（`<script>` 內 JSON 資料陣列必須逐字元相同；表格列數相同）。
4. 台股紅賺綠虧不可反。
