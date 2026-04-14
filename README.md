# /design — DESIGN.md Skill for Claude Code

A Claude Code custom skill that generates, manages, and syncs design system files (`DESIGN.md`) with Figma integration. Decide all your colors, typography, spacing, and component styles **before** building any application — and update them after.

一個 Claude Code 自訂技能，用於生成、管理及同步設計系統檔案（`DESIGN.md`），支援 Figma 雙向整合。在開發任何應用程式**之前**就決定好所有顏色、字型、間距和元件樣式，開發後也能隨時更新。

---

## How It Works / 運作方式

The default flow is **Figma-first** and interactive:

預設流程以 **Figma 為核心**，互動式引導：

```
/design
  1. Claude asks about your project preferences (Q&A)
     Claude 詢問你的專案偏好（問答）

  2. Generates a proposed color palette
     生成建議的調色盤

  3. Creates a Figma file with color swatches via Figma MCP
     透過 Figma MCP 建立含有色票的 Figma 檔案

  4. You adjust colors visually in Figma
     你在 Figma 中視覺化調整顏色

  5. Claude reads back your final choices
     Claude 讀取你的最終選擇

  6. Generates complete DESIGN.md (9 sections)
     生成完整的 DESIGN.md（9 個章節）
```

## Demo / 示範

```bash
# Start guided flow with Figma / 啟動 Figma 引導流程
/design

# Skip Q&A, provide description / 跳過問答，直接描述
/design modern minimalist SaaS with indigo accents

# Text-only, no Figma / 純文字模式，不使用 Figma
/design --no-figma clean dashboard with blue tones

# Extract from a website / 從網站擷取設計
/design --from-url https://linear.app

# Extract from Figma file / 從 Figma 檔案擷取
/design --from-figma https://figma.com/design/abc123/MyDesign

# Update colors later / 之後更新顏色
/design --update-colors "warmer tones, switch to green"

# Apply tokens to project files / 套用設計到專案檔案
/design --apply --framework tailwind

# Push DESIGN.md to Figma / 將 DESIGN.md 推送到 Figma
/design --to-figma

# Bidirectional Figma sync / Figma 雙向同步
/design --sync-figma https://figma.com/design/abc123/MyApp
```

## Installation / 安裝

Copy `design.md` to your Claude Code commands directory:

將 `design.md` 複製到你的 Claude Code 指令目錄：

```bash
# macOS / Linux
cp design.md ~/.claude/commands/design.md

# Windows
copy design.md %USERPROFILE%\.claude\commands\design.md
```

That's it. The `/design` command is now available globally in all your projects.

就這樣。`/design` 指令現在在你所有的專案中都可以使用了。

## Requirements / 需求

### Required / 必要

- **Claude Code** — [claude.ai/code](https://claude.ai/code)

### Optional (for Figma integration) / 選用（Figma 整合）

The skill works without Figma (`--no-figma` mode), but the full interactive flow requires:

此技能可以在沒有 Figma 的情況下運作（`--no-figma` 模式），但完整的互動流程需要：

| Tool / 工具 | Purpose / 用途 | Setup / 設定 |
|---|---|---|
| **Figma Desktop App** | Required for local Figma MCP / 本地 Figma MCP 必需 | [Download Figma](https://www.figma.com/downloads/) |
| **Figma MCP Plugin** | Connects Claude Code to Figma Desktop / 連接 Claude Code 和 Figma | Install from Figma Community: search "MCP" in Figma plugins |
| **Figma Remote MCP** (alternative) | Cloud-based Figma access / 雲端 Figma 存取 | Configure in `~/.claude/settings.json` — endpoint: `https://mcp.figma.com/mcp` |

#### Figma MCP Setup / Figma MCP 設定

**Option A: Figma Desktop (recommended) / 選項 A：Figma 桌面版（建議）**

1. Install [Figma Desktop](https://www.figma.com/downloads/)
2. In Figma, go to **Plugins** > search for **"Claude Code"** or **"MCP"** > Install
3. Run the plugin in Figma — it starts a local MCP server
4. Add to your `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "figma": {
      "type": "sse",
      "url": "http://127.0.0.1:3845/sse"
    }
  }
}
```

**Option B: Figma Remote MCP / 選項 B：Figma 遠端 MCP**

Add to your `~/.claude/settings.json`:

在 `~/.claude/settings.json` 中加入：

```json
{
  "mcpServers": {
    "figma": {
      "type": "sse",
      "url": "https://mcp.figma.com/mcp"
    }
  }
}
```

Then authenticate when prompted by Claude Code.

然後在 Claude Code 提示時進行認證。

### Optional (for website extraction) / 選用（網站擷取）

For `--from-url` mode with JavaScript-heavy sites:

用於 `--from-url` 模式處理大量 JavaScript 的網站：

| Tool / 工具 | Purpose / 用途 | Setup / 設定 |
|---|---|---|
| **Cloudflare Browser Rendering** | Crawl JS-rendered sites / 爬取 JS 渲染的網站 | Create `~/.cloudflare/.env` with `CLOUDFLARE_ACCOUNT_ID` and `CLOUDFLARE_API_TOKEN` |

## Figma MCP Tools Used / 使用的 Figma MCP 工具

This skill uses the following Figma MCP tools:

此技能使用以下 Figma MCP 工具：

| Tool | Usage / 用途 |
|---|---|
| `mcp__figma__whoami` | Get user info and plan key / 取得用戶資訊和方案金鑰 |
| `mcp__figma__create_new_file` | Create new Figma design file / 建立新的 Figma 設計檔案 |
| `mcp__figma__use_figma` | Execute Plugin API JS to create color swatches, variables / 執行 Plugin API JS 建立色票和變數 |
| `mcp__figma__get_variable_defs` | Read design token variables (colors, spacing) / 讀取設計代幣變數（顏色、間距） |
| `mcp__figma__get_design_context` | Get code references, screenshot, metadata / 取得程式碼參考、截圖、元資料 |
| `mcp__figma__get_screenshot` | Get visual screenshot of design / 取得設計的視覺截圖 |
| `mcp__figma__search_design_system` | Search existing design system components / 搜尋現有設計系統元件 |

## All Modes / 所有模式

| Mode / 模式 | Description / 說明 |
|---|---|
| `/design` | **Default**: Guided Q&A → Figma color swatches → read back → DESIGN.md / **預設**：引導問答 → Figma 色票 → 讀取回來 → DESIGN.md |
| `/design <description>` | Same flow, skip Q&A / 相同流程，跳過問答 |
| `/design --no-figma` | Text-only generation, no Figma / 純文字生成，不使用 Figma |
| `/design --from-url <url>` | Extract design tokens from a website / 從網站擷取設計代幣 |
| `/design --from-figma <url>` | Extract from existing Figma file / 從現有 Figma 檔案擷取 |
| `/design --update-colors [desc]` | Update color palette (with optional Figma round-trip) / 更新調色盤（可選 Figma 來回調整） |
| `/design --apply` | Apply DESIGN.md tokens to project files / 套用 DESIGN.md 代幣到專案檔案 |
| `/design --to-figma [url]` | Push DESIGN.md to Figma as visual reference / 將 DESIGN.md 推送到 Figma 作為視覺參考 |
| `/design --sync-figma <url>` | Bidirectional Figma sync / Figma 雙向同步 |

### Options / 選項

| Option / 選項 | Description / 說明 |
|---|---|
| `--dark` | Include dark mode variant / 包含深色模式變體 |
| `--framework <name>` | Target framework: `tailwind`, `css-vars`, `remotion` / 目標框架 |
| `--output <path>` | Output path (default: `./DESIGN.md`) / 輸出路徑 |
| `--help` | Show help / 顯示說明 |

## DESIGN.md Format / DESIGN.md 格式

The generated DESIGN.md follows the [Google Stitch format](https://stitch.withgoogle.com/docs/design-md/overview/) with 9 sections:

生成的 DESIGN.md 遵循 [Google Stitch 格式](https://stitch.withgoogle.com/docs/design-md/overview/)，包含 9 個章節：

1. **Visual Theme & Atmosphere** — Mood, aesthetic philosophy / 視覺主題與氛圍
2. **Color Palette & Roles** — Descriptive names + hex codes + functional purposes / 描述性名稱 + 色碼 + 功能用途
3. **Typography Rules** — Font families, hierarchy, weights/sizes / 字型規則
4. **Component Stylings** — Buttons, cards, nav, inputs with states / 元件樣式
5. **Layout Principles** — Grid, spacing, breakpoints / 佈局原則
6. **Depth & Elevation** — Shadows, surface hierarchy / 深度與層次
7. **Do's and Don'ts** — Design guardrails / 設計準則
8. **Responsive Behavior** — Breakpoints, touch targets / 響應式行為
9. **Agent Prompt Guide** — Quick reference for AI agents / AI 代理快速參考

### Example / 範例

```markdown
## 2. Color Palette & Roles

### Primary Foundation
- **Midnight Indigo** (#4F46E5) -- primary brand color, CTAs, active states
- **Soft Lavender** (#EEF2FF) -- main background, breathing room
- **Cloud White** (#FAFAFA) -- card surfaces, elevated content

### Accent & Interactive
- **Electric Violet** (#7C3AED) -- secondary actions, hover highlights

### Typography & Text Hierarchy
- **Ink Black** (#1E1E2E) -- headings, primary text
- **Slate Gray** (#64748B) -- body text, descriptions
- **Silver Mist** (#CBD5E1) -- borders, dividers, placeholders
```

## Integration with CLAUDE.md / 與 CLAUDE.md 整合

After generating DESIGN.md, add this to your project's `CLAUDE.md`:

生成 DESIGN.md 後，在你專案的 `CLAUDE.md` 中加入：

```markdown
Design system rules are defined in DESIGN.md. Always reference it for colors,
typography, spacing, and component styles. Never use values not defined in DESIGN.md.
```

## Credits / 致謝

- Format inspired by [Google Stitch DESIGN.md](https://stitch.withgoogle.com/docs/design-md/overview/)
- Built for [Claude Code](https://claude.ai/code) by Anthropic
- Uses [Figma MCP](https://www.figma.com/) for visual design integration

## License / 授權

MIT
