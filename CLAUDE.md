# 鷗哩手作 — 專案指令與開發紀錄

## 專案概述

單頁 HTML 網站，純 HTML/CSS/JS（Vanilla，無框架）。
托管於 GitHub Pages：`https://dean80319.github.io/dean80319_HTML/`
主檔：`index.html`

---

## 功能說明

### 水晶小學堂
水晶相關知識內容頁面。

### 手鍊 DIY 設計師
互動式 SVG 手鍊設計工具：
- **手圍調整**：動態換算 SVG 底圈半徑
- **礦石大小選擇**：支援 4mm–12mm 不同直徑珠子
- **手動拖曳**：按住珠子沿圓周自由調整位置
- **智慧插入**：「最大空隙演算法」自動找最寬位置插入新珠子
- **均勻排列**：一鍵等距分配所有珠子
- **移除珠子**：右鍵或雙擊移除（`hasDragged` flag 防止 SVG 重建破壞 dblclick）
- **物理尺寸限制**：`getUsedMm()` 累加所有珠子 mm，防止超過手圍
- **重疊偵測**：重疊珠子標紅框提示
- **復原 / 儲存圖片**：undo 堆疊，Canvas 轉 PNG 下載
- **銀珠**：`radialGradient` 銀色金屬光澤，固定選任意 mm
- **星星 / 愛心銀飾吊飾**：固定 10mm，`<path>` 渲染（非圓形）
  - SVG：動態 `radialGradient`（`gradientUnits="userSpaceOnUse"`，`fx`/`fy` 焦點偏左上）+ `clipPath` 裁切鏡面橢圓，產生立體球面感
  - CSS 選色器：`radial-gradient(circle at 35% 30%)` + `clip-path` 裁切成星星/愛心形狀

### 魔法世界（2026/07/12 新增）
側欄「🪄 魔法世界」取代原本獨立的「占卜」按鈕，`onclick="startMagicWorld(this)"`：
- **入口轉場（`#magic-overlay`）**：SVG 魔法陣（`#magic-circle-svg`）——雙圈同心圓 + 五芒星，皆用 `pathLength="100"` + `stroke-dashoffset` 做「畫線顯現」動畫；外圈符文刻度（`.mg-runes`）持續旋轉；`#magic-canvas` 粒子先向中心匯聚、開門瞬間向外爆開（金/紫兩色）；結束後 `showPage('magic-hub', navBtn)`
- **Hub 頁（`#page-magic-hub`）**：兩張選項卡（`.magic-choice`）——🔮 占卜／🧪 精油，圖示為放射漸層圓（紫色系 / 粉紫系）+ hover 上浮發光

#### 占卜（沿用舊功能，僅換入口）
Hub 頁點「占卜」卡片觸發（呼叫 `startDivination(document.getElementById('nav-magic'))`）：
- **Canvas 粒子動畫**：三層軌道環（紫/金/青色）+ 上升火花 + 能量脈衝環 + 高潮放射爆發
- **Aurora CSS 層**：兩層 `conic-gradient` 旋轉光暈疊加在水晶球外
- **水晶球（`.cb-ball`）**：彈簧入場動畫（`cubic-bezier(0.34, 1.56, 0.64, 1)`）；雙層背景漸層（左上玻璃反光 + 底部深色），`blur` 柔化 reflex 高光
- **底座（`.cb-pedestal`）**：三層結構，深紫黑色搭金色邊
  - `cb-ped-cradle`：橢圓碗架，金色發光頂緣（`::before` blur 光暈），`z-index: 2` 讓球視覺上坐入碗中
  - `cb-ped-neck`：窄頸柱，兩側細金邊
  - `cb-ped-foot`：寬底台，內嵌裝飾框線（`::before`）+ 頂緣金色微光（`::after`），強投影
- **白光閃爍轉場**：動畫結束後全螢幕白光，切換至占卜結果頁（`#page-divination`，內含「← 返回魔法世界」）

#### 精油頁內容
`#page-oil` 頂部放 `.oil-hero` 圖框，直接引用 `picture/進入精油圖_女巫調製_GEMINI生成.png`（Gemini 生成的女巫熬煮插畫，未去背/未拆件，整張圖當作情境 banner），`object-fit: cover` 置中裁切，圓角＋金色描邊＋淡紫光暈呼應卡片風格；下方保留「敬請期待」文字。

#### 精油入場動畫
Hub 頁點「精油」卡片觸發 `startOilBrewing()`，`#oil-overlay` 女巫熬煮場景：
- **女巫（`.oil-witch`）**：純 CSS 剪影（三角帽 + 帽帶 + 帽簷 + 圓形臉 + 斗篷梯形 + 手臂/攪拌棒），所有部件對齊同一垂直中心線（曾修正帽子偏移 12px 的錯位問題）；攪拌棒持續小幅擺動（`stirWiggle`）
- **能量石鍋爐（`.oil-cauldron`）**：半透明水晶質感（非不透明金屬），斜向高光切面模擬切割面 + 淡紫邊框光；**鍋爐整體背景本身**在平常（紫）與沸騰（`.boil-fast`，粉）間漸變 — 已移除獨立的「液面」圖層（`.oil-potion`）與其光暈層（`.oil-potion-glow`），改為「整顆能量石在變色」而非「容器裝液體」的概念；內部氣泡（`.oil-bubble`）保留，視為漂浮其中的能量微粒
- **魔法陣熱源（`.oil-magic-pad`）**：取代傳統柴火（已移除 `.oil-fire`/`.oil-flame`/`flicker`）；雙圈橢圓光環（`.omp-ring-outer/inner`）+ 發光核心（`.omp-core`），整體用 `filter: hue-rotate` 持續變色（`padHueCycle`），沸騰階段變色加速；**3 個光點**（`.omp-dot-*`）用 `offset-path: ellipse()` 沿內外圈軌跡跑動（取代原本一片旋轉平面光圈的做法），方向與速度交錯，沸騰階段加速
- **藥草／精油瓶**：JS 於特定時間點動態插入 `.oil-herb` 葉片元素（下墜 + 旋轉 + 淡出，動畫結束自動移除節點）；`.oil-vial` 傾斜（`vialPour`）+ 琥珀色細流（`.oil-pour-stream`，`streamGrow`）倒入鍋中
- **爆炸／滿版雲霧**：`#oil-canvas` 粒子噴發（大顆柔邊粉/紫圓斑）+ 3 層 `.oil-fog` 全螢幕模糊放射漸層，確保視覺上滿版覆蓋；結束後白閃轉場 `showPage('oil', ...)`，精油頁（`#page-oil`）內容先留空（僅標題卡 + 「敬請期待」）

### 每日塔羅（占卜結果頁）
按「✦ 宇宙引路 ✦」按鈕觸發：
- 依當日日期決定牌（`(year*10000 + month*100 + day) % 22`），每天固定同一張
- 牌面翻轉動畫（CSS 3D `rotateY(180deg)`，`backface-visibility: hidden`）
- 完整 22 張大阿爾克那，宇宙星球風 SVG 插圖，存於網頁 JS 內

---

## 技術實作重點

### 塔羅牌 SVG 寫實星球渲染（2026/06/29 更新）

`artTypes` 物件負責各牌的天體插圖渲染，主要類型：

**`planet` / `ringed`（行星）**
- 4 段光照漸層：`highlight`（左上高光）→ `mid`（中間調）→ `shadow`（暗部）→ `dark`（夜面）
- 夜面 terminator：右下角 `radialGradient` 疊加深色陰影
- 大氣層邊緣光暈：`radialGradient`（內透明、邊緣發光）
- 左上角反光高光點：白色模糊橢圓
- 外部空間輝光：大型模糊圓
- 表面帶紋（氣態巨行星）：水平 `<rect>` 裁切至行星 `clipPath`
- 隕石坑（岩石行星）：`_r()` 決定性亂數生成位置，暗色圓 + 白邊
- 環形行星（`ringed`）：多段 `linearGradient` 環帶，後半環先畫、前半環後畫，實現正確前後遮擋

**卡牌 `art` 參數（planet/ringed 類型）**
```
highlight  — 最亮處顏色（左上光源）
mid        — 中間調顏色
shadow     — 暗部顏色
dark       — 夜面最深色
atmos      — 大氣層輝光顏色
glow       — 外部空間輝光顏色
bands[]    — [{y, h, col, op}] 水平帶紋（y: SVG座標, h: 高度px）
craters    — 隕石坑數量（整數）
ring       — 環的主色（ringed 類型）
ring2      — 環的次色（ringed 類型，交替帶紋）
```

**決定性亂數 `_r(n)`**
整數雜湊函數，確保每次渲染同一張牌的星星/隕石坑位置完全一致。

### 其他類型
- `sun`：18 射線 + 3 日珥弧線 + 5 段 limb-darkening 漸層
- `moon`：SVG `mask` 月牙裁切 + 固定位置隕石坑 + 大氣環光
- `galaxy`：3 臂螺旋路徑
- `blackhole`：同心橢圓吸積盤 + 中心黑洞漸層
- `burst`：26 條放射線
- `comet`：彗星尾路徑 + 彗核
- `binary`：雙星 + 軌道虛線

### Select 下拉選單可見性
```css
select { background: #141b2e; color: rgba(255,255,255,0.92); }
select option { background: #141b2e; color: rgba(255,255,255,0.9); }
```
原因：瀏覽器原生 `<select>` 背景為白色，文字也是白色時看不見。必須設定 solid 背景色（不能用 rgba）。

---

## 檔案狀態（2026/07/12 更新）

- `index.html` — 正式版（單一主檔）
- `backup/index_new.html` — 備份，內容與合併前相同
- `picture/` — 設計參考圖，目前**未**在網頁中引用
  - `進入精油圖_1.png` / `進入精油圖_2.png` — 精油鍋爐場景截圖，用於討論鍋爐與液面對齊問題
  - `進入精油圖_女巫調製_GEMINI生成.png` — Gemini 生成的女巫熬煮插畫（可愛插畫風，含鍋爐、飄浮藥水瓶、藥草、兩側藥罐櫃、彩色魔法陣）；**已引用**於 `#page-oil` 的 `.oil-hero` banner

---

## UI 改進清單（index_new.html 帶入的項目，現已合併至 index.html）

- **Google Fonts**：`Noto Sans TC` 繁中網路字型
- **`--card: rgba(255,255,255,0.07)`**：玻璃卡片透明度提升（原 0.032）
- **17 顆閃爍星星**：`body::after` + `animation: starTwinkle 8s` alternate
- **RWD 行動版側欄**：`@media (max-width: 768px)` — `position: fixed; transform: translateX(-100%)` + 半透明 backdrop
- **Active nav 高亮**：`.nav-btn.active` CSS + `showPage(id, navBtn)` 更新 active class
- **`focus-visible` 鍵盤輪廓**：所有互動元素加 `outline`
- **SVG 空白提示**：`<text id="svg-hint">點擊上方水晶開始設計</text>`
- **aria 屬性**：`aria-expanded`、`aria-label`、`aria-controls`
- **子選單改 `<button>`**：原為 `<a href="#">`
- **水晶選項**：`role="button" tabindex="0" aria-label="..."`
- **手機自動收合側欄**：`showPage` 內呼叫 `closeSidebar()`
- **`openSidebar()` / `closeSidebar()`**：分拆自 `toggleSidebar()`

---

## 版本紀錄

| 日期 | 內容 |
|------|------|
| 2026/06/19 | 手鍊 DIY 功能：最大空隙演算法、拖曳、均勻排列 |
| 2026/06/27 | 占卜功能：水晶球動畫、轉場效果（原 index.html） |
| 2026/06/29 | 全站重設計：glassmorphism UI、Canvas 占卜動畫、每日塔羅牌、寫實星球 SVG、覆蓋至 index.html 並推送 GitHub |
| 2026/07/05 | 合併 index_new.html UI 改進至 index.html：Noto Sans TC、RWD 行動版、Active nav、閃爍星星、Select 修正、塔羅牌重置邏輯；本地與 GitHub 完全同步（commit 5870c3d） |
| 2026/07/05 | 手鍊 DIY 大幅升級：銀珠、移除珠子、物理尺寸限制（混合尺寸）、重疊偵測、復原、儲存圖片；新增星星／愛心銀飾吊飾（固定 10mm，SVG `<path>` 動態焦點漸層 + clipPath 立體感） |
| 2026/07/05 | 占卜場景重設計：水晶球加玻璃反光內漸層；底座改為三層 CSS 結構（碗架／頸柱／底台），深紫黑色 + 金色邊框光效 |
| 2026/07/12 | 新增「魔法世界」頁籤取代獨立「占卜」按鈕：魔法陣入場轉場動畫 + Hub 頁（占卜／精油兩選項）；占卜功能原封不動搬移至 Hub 底下 |
| 2026/07/12 | 新增「精油」全新女巫熬煮動畫：生火攪拌 → 藥草／精油倒入 → 沸騰爆亮 → 粉紫雲霧滿版轉場 → 精油頁（內容先留空）；女巫剪影帽子/臉部對齊問題已修正 |
| 2026/07/12 | 精油鍋爐視覺改版：柴火改為會持續變色的魔法陣光環（`hue-rotate` + 光點沿橢圓軌跡跑動，取代原本旋轉平面光圈）；鍋爐改為半透明水晶質感，移除獨立液面圖層，改成整顆能量石本體變色 |
| 2026/07/12 | 精油頁加入 `.oil-hero` 插畫 banner，引用 Gemini 生成的女巫熬煮插畫圖 |
