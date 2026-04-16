# Video Block Library — `/design --video-layout` 場景 preset

供 `--video-layout` mode 的 block-picker 顯示。8 個場景 preset 與 `~/.claude/skills/remotion/references/design-tokens-bridge.md` 同步（**single source of truth 在 Remotion skill 那側**）。

任何修改先確認 Remotion skill `design-tokens-bridge.md` Section 2 的列舉；兩邊保持一致。

---

## 8 個場景 preset

### `hero-title` — 大標 + 副標 + accent bar

```
┌──────────────────────────────────┐
│                                  │
│            ━━━━━━                │  ← accent bar 進場
│                                  │
│       歡迎來到 Remotion          │  ← title (spring-y)
│                                  │
│   用 React 寫影片 · 旁白由 …    │  ← subtitle (delayed)
│                                  │
└──────────────────────────────────┘
```
**用途**：Intro 開場。**props**：`{ title: string; subtitle?: string }`。預設 5 秒。

---

### `kinetic-type` — 逐字動態大字

```
┌──────────────────────────────────┐
│                                  │
│   M  O  V  E   F  A  S  T        │  ← 每字 stagger 進場
│   ↑  ↑  ↑  ↑   ↑  ↑  ↑  ↑       │   背景用 primary 色
│                                  │
└──────────────────────────────────┘
```
**用途**：強化 Hook、概念字、轉場過字。**props**：`{ text: string; staggerFrames?: number }`。預設 4 秒。

---

### `stagger-list` — 列表錯位進場

```
┌──────────────────────────────────┐
│                                  │
│  [01]  快速                     │  ← 由左滑入
│                                  │
│  [02]  可靠                     │  ← 延遲 8 幀
│                                  │
│  [03]  便宜                     │  ← 再延遲 8 幀
│                                  │
└──────────────────────────────────┘
```
**用途**：Features、步驟、原則列表。**props**：`{ items: { tag: string; label: string; color?: string }[] }`。預設 7 秒。

---

### `split-media` — 左影片右文字（可 flip）

```
┌──────────────┬──────────────────┐
│              │                  │
│              │   標題           │
│   📹 影片    │                  │
│              │   說明文字 …     │
│              │                  │
└──────────────┴──────────────────┘
```
**用途**：產品 demo + 說明、Before/After。**props**：`{ src: string; title: string; body?: string; flipped?: boolean }`。預設 6 秒。

---

### `fullscreen-video` — 全螢幕影片 + overlay 文字

```
┌──────────────────────────────────┐
│                                  │
│      📹 全螢幕 B-roll            │
│                                  │
│  ████████████████████            │  ← 漸層 overlay
│  Overlay 標題                    │  ← 左下文字
└──────────────────────────────────┘
```
**用途**：B-roll 過場、氛圍鏡頭。**props**：`{ src: string; overlay?: string; volume?: number }`。預設 6 秒。

---

### `quote-card` — 大引言 + 出處

```
┌──────────────────────────────────┐
│              "                   │  ← 大引號（primary 色）
│                                  │
│   "Remotion 改變了我的工作流"   │
│                                  │
│         — 王小明                 │
│           Frontend Lead          │
└──────────────────────────────────┘
```
**用途**：客戶見證、名言、概念。**props**：`{ quote: string; author: string; role?: string }`。預設 6 秒。

---

### `big-numbers` — 大數字 stats 列

```
┌──────────────────────────────────┐
│                                  │
│    99%       12K       4.8★      │  ← 巨大數字（primary）
│   滿意度    使用者     評分      │  ← 標籤（caption）
│                                  │
└──────────────────────────────────┘
```
**用途**：Stats、業績、KPI。**props**：`{ stats: { value: string; label: string }[] }`。預設 6 秒。

---

### `logo-cta` — Logo + CTA + 收尾

```
┌──────────────────────────────────┐
│                                  │
│             ●                    │  ← Logo（spring scale）
│           LOGO                   │
│                                  │
│      [  Try it now  ]            │  ← CTA pill button
│                                  │
└──────────────────────────────────┘
```
**用途**：Outro、品牌、CTA。**props**：`{ cta: string; logoSrc?: string }`。預設 4 秒。

---

## 互動 picker 範例

```
> 場景數？ 4

Scene 1 — 哪種版型？
  A) hero-title       (大標 + 副標)
  B) kinetic-type     (逐字大字)
  C) stagger-list     (列表)
  D) split-media      (左影片右字)
  E) fullscreen-video (全螢幕影片)
  F) quote-card       (引言)
  G) big-numbers      (大數字)
  H) logo-cta         (Outro)

→ A
✓ Scene 1: hero-title
  · title? "歡迎來到 Remotion"
  · subtitle? "用 React 寫影片"
  · duration? 5 秒（150 frames @ 30fps）

Scene 2 — 哪種版型？
→ C
✓ Scene 2: stagger-list
  · 列出項目（每行：tag | label | (可選) color hex）
    01 | 快速 | #22d3ee
    02 | 可靠 | #f472b6
    03 | 便宜 | #fde047
  · duration? 7 秒

Scene 3 — 哪種版型？
→ F
✓ Scene 3: quote-card
  · quote? "改變了我的工作流"
  · author? "王小明"
  · role? "Frontend Lead"
  · duration? 6 秒

Scene 4 — 哪種版型？
→ H
✓ Scene 4: logo-cta
  · cta? "立即試用"
  · logoSrc? (略，使用內建星星)
  · duration? 4 秒

→ 接著問配色（沿用 --update-colors 流程）
→ 字型？
→ 轉場？fade / wipe / slide / cube / none
```

支援指令：
- `change scene 2 to kinetic-type` — 改某 scene preset
- `swap scene 1 and 3` — 互換順序
- `extend scene 4 to 6 seconds` — 改 duration
- `recolor primary to #ff0066` — 改配色 token
- `apply` — 進入 `--apply --framework remotion` 流程

---

## 影片用途 → 場景組合建議

| 用途 | 建議組合 | 總長 |
|------|----------|------|
| **社群短影音 (15-30s)** | hero-title → kinetic-type → stagger-list → logo-cta | ~20s |
| **產品介紹 (30-60s)** | hero-title → split-media → stagger-list → big-numbers → logo-cta | ~40s |
| **客戶見證 (20-30s)** | hero-title → fullscreen-video → quote-card → logo-cta | ~25s |
| **數據簡報 (30-45s)** | hero-title → big-numbers → stagger-list → quote-card → logo-cta | ~35s |
| **B-roll 蒙太奇 (15-25s)** | kinetic-type → fullscreen-video → kinetic-type → fullscreen-video → logo-cta | ~20s |

picker 流程開始時可先問用途，建議對應組合，使用者可改。

---

## Figma Storyboard 渲染配方

`--video-layout` Step 4e 為每個選定的場景在 Figma 渲染靜態 storyboard 縮圖。整個 storyboard 是一條**橫向 flow**：`scene1 → ▶ → scene2 → ▶ → scene3 → ...`

### 全局規格

- 每個 scene frame 大小 = `meta.width × meta.height`（預設 1920×1080；portrait 1080×1920；square 1080×1080）
- frame x-position = `(K-1) × (meta.width + 120)`，y = 0
- frame name = `Scene K — <preset> (<id>)`，例如 `Scene 1 — hero-title (intro)`
- frame 上方放小標籤 `K · preset · Ns`（位置 y = -40px）
- frame 之間 120px 間隔處放 ▶ 圖示與轉場名（fade / wipe / slide / flip / none）
- 所有顏色取 DESIGN.md Section 2；字型取 Section 3（`figma.loadFontAsync` 預載）
- 不使用 auto-layout FILL（與 `--layout landing-page` 同樣慣例）

### 各 preset Figma 渲染規格

#### `hero-title`
- 背景：linear-gradient `colors.bg` → `colors.bgAccent`（135deg）
- 中央：accent bar（240×6，`colors.primary`）
- bar 下方 48px：title 文字（typography.title 規格，`colors.text`）
- title 下方 32px：subtitle 文字（typography.subtitle 規格，`colors.textMuted`），可選

#### `kinetic-type`
- 背景：`colors.primary`（整片）
- 中央：text 大字（typography.title.size + 12，weight 900，`colors.bg` 顏色）
- 不需做動畫，整段字平鋪即可

#### `stagger-list`
- 背景：`colors.bg`，內距 spacing.xl + spacing.md
- 垂直堆疊每個 item，gap = spacing.md
- 每行：左邊 80×80 圓角方塊（`item.color` 或 `colors.primary`，內含 `item.tag` 文字），右邊 `item.label`（typography.title.size / 2.5）

#### `split-media`
- 背景：`colors.bg`
- 50/50 split（flipped 時左右翻轉）
- 媒體側：placeholder 矩形（深灰，`#333`），中央放 📹 icon
- 文字側：padding spacing.xl，title（typography.title.size × 0.7）+ body（typography.body）

#### `fullscreen-video`
- 滿版深色矩形（`#0a0a0a`），中央放 📹 icon
- 底部覆蓋漸層 `linear-gradient(0deg, rgba(0,0,0,0.6), transparent 60%)`
- 左下角 padding spacing.xl 放 overlay 文字（typography.title.size × 0.6，白）

#### `quote-card`
- 背景：`colors.bg`
- 中央：大引號 `"`（typography.title.size × 1.4，`colors.primary`，weight 900）
- 引號下方：blockquote（typography.title.size × 0.5，`colors.text`，line-height 1.3）
- blockquote 下方 spacing.lg：author（body 規格，weight 700）+ role（subtitle × 0.6，`colors.textMuted`）

#### `big-numbers`
- 背景：`colors.bg`
- 中央：橫向 stats 排（gap = spacing.xl）
- 每項：垂直堆 value（typography.title.size × 1.4，`colors.primary`，weight 900，letterSpacing -6）+ label（body 規格，`colors.textMuted`，uppercase，letterSpacing 2）

#### `logo-cta`
- 背景：linear-gradient `colors.bg` → `colors.bgAccent`（135deg）
- 中央上方：280×280 圓形 placeholder（`colors.primary`，內放 ★ 大字 `colors.bg`）
- 圓形下方 spacing.lg：pill button（padding spacing.md × spacing.xl，`colors.primary` bg、`colors.bg` 文字、subtitle.size × 1.4）

### 轉場 ▶ 圖示

每兩個 frame 之間的 120px 間隔中央：
- `fade`：→ ◐ → 漸層圓圈
- `wipe`：→ ▶ → 大箭頭
- `slide`：→ ⇨ → 雙箭頭
- `flip`：→ ↻ → 旋轉箭頭
- `none`：→ |  → 直線

底下小字標註轉場名稱與時長（`fade 15f`）。

