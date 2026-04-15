---
description: Generate, update, or apply a DESIGN.md design system file for any project
---

Generate, update, or apply a DESIGN.md design system file.

User input: $ARGUMENTS

## Handle `--help`

If user input contains `--help` or `-h`, do NOT execute any actions. Instead, display the following help text and stop:

```
Usage: /design [description] [options]

Create a design system for your project. Claude guides you through preferences,
generates a color palette, creates a Figma file with visual swatches you can
adjust, reads back your final choices, and generates DESIGN.md.

Default Flow (Figma-guided):
  /design                    Start guided flow: Q&A → palette → Figma → DESIGN.md
  /design <description>      Skip Q&A, use description as direction for palette
                             Example: /design modern minimalist SaaS with indigo accents

  The default flow:
  1. Asks about your project, mood, color preferences, and fonts
  2. Generates a proposed color palette
  3. Creates a Figma file with color swatches and preview mockups (dark, light, components)
  4. Waits for you to fine-tune colors in Figma
  5. Reads back your final colors and generates DESIGN.md

Other Modes:
  --from-url <url>           Extract design tokens from a live website.
                             Example: /design --from-url https://linear.app

  --from-figma <url>         Extract design tokens from an existing Figma file.
                             Example: /design --from-figma https://figma.com/design/abc123/MyDesign

  --update-colors [desc]     Update the color palette in an existing DESIGN.md.
                             Offers to push changes to Figma for visual fine-tuning.
                             Example: /design --update-colors "warmer tones, switch to green"

  --apply                    Apply DESIGN.md tokens to project source files.
                             Auto-detects Tailwind config, CSS files, theme files.
                             Example: /design --apply

  --to-figma [url]           Push DESIGN.md to Figma as a visual design system page.
                             Creates color swatches, typography samples, and variables.
                             Example: /design --to-figma

  --sync-figma <url>         Interactive sync: read from Figma, refine, push back.
                             Example: /design --sync-figma https://figma.com/design/abc/MyApp

  --layout [project-type]    Interactively assemble page layout from block variants.
                             Section-by-section Q&A (Hero style? Features layout?),
                             renders in Figma, updates DESIGN.md sections 1, 4, 5, 9.
                             Example: /design --layout landing-page

Options:
  --no-figma                 Skip Figma integration, generate DESIGN.md as text only.
  --dark                     Include a dark mode color variant section.
  --framework <name>         Target framework hint: tailwind, css-vars, remotion.
  --output <path>            Output path (default: ./DESIGN.md).
  --help, -h                 Show this help message.

Examples:
  /design                                    Full guided flow with Figma
  /design bold playful app with orange        Guided flow, skip Q&A
  /design --no-figma clean dashboard          Text-only, no Figma
  /design --from-url https://stripe.com       Extract from website
  /design --from-figma https://figma.com/...  Extract from Figma file
  /design --update-colors "more muted"        Update existing palette
  /design --apply --framework tailwind        Apply tokens to project
  /design --to-figma                          Push DESIGN.md to Figma
  /design --sync-figma https://figma.com/...  Bidirectional Figma sync
  /design --layout landing-page               Interactive layout block-picker
```

## Instructions

Parse user input to detect the active mode:
- If input contains `--from-url`, extract the URL after it. Mode = **from-url**.
- If input contains `--from-figma`, extract the Figma URL after it. Mode = **from-figma**.
- If input contains `--update-colors`, Mode = **update-colors**. Any text after it (that is not another flag) is the change description.
- If input contains `--apply`, Mode = **apply**.
- If input contains `--to-figma`, optionally extract a Figma URL after it. Mode = **to-figma**.
- If input contains `--sync-figma`, extract the Figma URL after it. Mode = **sync-figma**.
- If input contains `--layout`, Mode = **layout**. Optional argument after it (that is not another flag) is the project type (e.g., `landing-page`, `dashboard`, `e-commerce`, `blog`, `portfolio`, `mobile`).
- If input contains `--no-figma`, Mode = **generate-text-only**. Any remaining text (excluding other flags) is the description.
- If input contains `--interactive`, treat as default mode (same as no flags). Mode = **guided**.
- If none of the above flags are present, Mode = **guided**. If input contains text, use it as a description to skip Q1-Q2 of the Q&A. If input is empty, start the full Q&A.

Also parse these options from any mode:
- `--dark` (boolean) — include dark mode variant
- `--framework <name>` — target framework hint
- `--output <path>` (default: `./DESIGN.md`) — output file path

---

## Mode: Guided Flow (default)

This is the primary mode. It runs when the user provides no flags, a description, or `--interactive`.

### Step 1: Check for existing DESIGN.md

Check if a DESIGN.md already exists at the output path. If it does, ask the user:
> "A DESIGN.md already exists. Would you like to overwrite it, or update specific sections?"

If they want to overwrite, proceed. If update, ask which sections to regenerate.

### Step 2: Gather design preferences (Q&A)

If the user provided a description (e.g., `/design modern SaaS with blue accents`), use it as the creative direction and skip Q1 and Q2. Go straight to Q3.

If no description was provided, ask these questions one at a time. Wait for each answer before asking the next.

**Q1: Project type**
> "What kind of project is this? (e.g., web app, e-commerce, landing page, dashboard, mobile app, video/Remotion, portfolio, blog)"

**Q2: Mood & atmosphere**
> "Describe the visual mood in 3-5 words. Examples: 'clean professional minimal', 'bold playful energetic', 'luxurious dark elegant', 'warm friendly approachable'"

**Q3: Color preferences**
> "Any specific colors you want to use or avoid? Any existing brand colors to include? (e.g., 'I love deep blues and warm grays', 'brand color is #FF6B35', 'avoid bright reds')"

**Q4: Light or dark mode?**
> "Light mode, dark mode, or both?"

**Q5: Typography style**
> "What typography style fits your project? (serif = traditional/elegant, sans-serif = modern/clean, monospace = technical/developer) Any specific fonts you'd like?"

**Q6: Reference sites (optional)**
> "Any websites or brands whose visual style you admire? I can extract design cues from them. (optional, press Enter to skip)"

If the user provides a URL, use WebFetch to extract design tokens from it as supplementary input.

**Q7: Target framework**
> "What framework are you building with? (Next.js/Tailwind, plain CSS, Remotion, other)"

### Step 3: Generate proposed palette

Based on the Q&A answers (or description), generate a harmonious color palette. Show it to the user as a text preview:

> "Here's the proposed palette based on your preferences:
>
> **Primary Foundation**
> - Midnight Indigo (#4F46E5) -- primary brand color, CTAs
> - Soft Lavender (#EEF2FF) -- background
> - Cloud White (#FAFAFA) -- surface
>
> **Accent & Interactive**
> - Electric Violet (#7C3AED) -- accent, hover states
>
> **Text Hierarchy**
> - Ink Black (#1E1E2E) -- headings
> - Slate Gray (#64748B) -- body text
> - Silver Mist (#CBD5E1) -- borders, dividers
>
> **Functional States**
> - Success Green (#10B981)
> - Warning Amber (#F59E0B)
> - Error Red (#EF4444)
>
> I'll now create a Figma file with these as visual swatches so you can fine-tune them."

### Step 4: Create Figma file with color swatches

**4a. Get Figma user info:**
Call `mcp__figma__whoami` to get the user's plan key.
If the user has multiple plans, ask which one to use.

**4b. Create a new Figma file:**
Call `mcp__figma__create_new_file` with:
- `fileName`: "Design System - [current project folder name]"
- `planKey`: from whoami result
- `editorType`: "design"

Save the returned `fileKey` and file URL.

**4c. Create the color palette page:**
Call `mcp__figma__use_figma` with the `fileKey` to create the visual color swatches. The JavaScript code should:

1. Create a main frame titled "Color Palette" with auto-layout (vertical, spacing 40)
2. For each color category (Primary Foundation, Accent, Text, Functional States), create a section frame with:
   - A section title text node (category name, 24px, bold)
   - A horizontal auto-layout frame containing color swatches
3. For each color, create a swatch frame (auto-layout, vertical, spacing 8) containing:
   - A rectangle (80x80px) filled with the color
   - Text: descriptive name (14px, bold)
   - Text: hex code (12px, regular)
   - Text: functional role (11px, gray)
4. Set the page background to white for clarity

**4d. Create Figma variables:**
Call `mcp__figma__use_figma` again to create variables:
1. Create a variable collection named "Design Tokens"
2. For each color, create a COLOR variable with the hex value
3. Bind each color rectangle's fill to its corresponding variable

**4e. Create product preview mockup pages:**
Create **3 separate Figma pages** that together showcase **every single color** in the palette. Each page targets a different color group so no color goes unused. Use multiple `mcp__figma__use_figma` calls (one per page) to keep code manageable.

The goal: the user should be able to flip through the pages and see every color from the palette applied in a realistic UI context — not just as abstract swatches.

**IMPORTANT:** Before building each page, cross-check the palette and verify that every color (primary, secondary, accent, backgrounds, surfaces, all text hierarchy levels, and all functional state colors) appears on at least one page. If any color is missing, find a natural place to include it.

---

**Page 1: "Preview - [project type] (Dark)"**

The main page mockup using the dark/primary color scheme. Based on the project type from Q1.

Use `mcp__figma__use_figma` to create a new page and build the layout. Use absolute positioning (not auto-layout with FILL) to avoid layout errors. The frame should be 1440px wide.

**Colors that MUST appear on this page:**
- Dark background color → page background
- Dark surface color → nav bar, card backgrounds, footer
- Primary color → CTA buttons, key highlights
- Accent color → decorative elements, hover indicators, number accents
- Secondary accent → card borders, dividers, subtle lines
- Primary text color (light) → headings, titles
- Secondary text color → body text, descriptions
- Tertiary text color → muted text, captions, footer text

**Layout by project type:**

*Landing page:*
- Top nav bar (surface bg) with logo text, 3-4 nav links, CTA button (primary color bg, dark text)
- Hero section (dark bg) with accent decorative line, small label (primary color), large heading (primary text), subheading (secondary text), CTA button
- Features section (surface bg) with section label (primary color), 3 feature cards (dark bg, secondary accent border) each with number accent (accent color, low opacity), title (primary text), description (secondary text)
- Testimonial section (dark bg) with decorative line (primary color), quote text (primary text), attribution (tertiary text)
- Footer (surface bg) with logo (primary color), copyright (tertiary text), subtle top border (secondary accent)

*Dashboard:*
- Left sidebar (dark bg) with logo, menu items (secondary text), active item (primary color highlight)
- Top bar (surface bg) with page title (primary text), avatar circle (accent color)
- Main area (dark bg) with 4 stat cards (surface bg, secondary accent border): label (secondary text), value (primary text), indicator dot (accent color)
- Larger card below (surface bg) with chart/table placeholder, section title (primary text)
- Status indicators using tertiary text for timestamps

*E-commerce:*
- Top nav (surface bg) with logo, search bar placeholder (dark bg, secondary accent border), cart icon placeholder (accent)
- Hero banner area (primary or accent color gradient/solid)
- Product grid: product cards (surface bg) with image placeholder (dark bg rect), product name (primary text), price (primary color), description (secondary text), add-to-cart button (primary color bg)

*Blog / Portfolio:*
- Minimal top nav (surface bg) with logo (primary color), links (secondary text)
- Featured post hero: large image placeholder (dark bg), overlay heading (primary text), date/meta (tertiary text)
- Content cards grid (surface bg) with thumbnails (dark bg rects), titles (primary text), excerpts (secondary text)

*Mobile app:*
- Frame sized 390x844
- Status bar, app header (surface bg) with title (primary text), icon circles (accent)
- Content cards stacked (surface bg, secondary accent border), titles (primary text), descriptions (secondary text)
- Bottom tab bar (surface bg) with icon circles, active tab (primary color)

---

**Page 2: "Preview - [project type] (Light)"**

The same page concept as Page 1, but using the light mode palette. This ensures light background, light surface, and dark text colors are all showcased.

Use `mcp__figma__use_figma` to create a new page and build a layout similar to Page 1 but with inverted color mapping.

**Colors that MUST appear on this page:**
- Light background color → page background
- Light surface color → nav bar, card backgrounds, footer
- Dark text color (e.g., Ink Walnut) → headings, titles
- Primary color → CTA buttons (same as dark mode)
- Accent color → decorative elements, highlights
- Secondary accent → card borders, dividers
- Secondary text color → body text, descriptions
- Tertiary text color → muted text, placeholders

**Layout:** Same structure as Page 1, but:
- Page background uses the lightest background color
- Cards/surfaces use the light surface color
- Headings use the darkest text color
- Body text uses secondary text or tertiary text as appropriate
- CTA buttons still use the primary color
- Borders and dividers use secondary accent at low opacity

If the user chose dark mode only (not "both"), still create this page but note: "Light mode preview — for reference even if your project is dark-mode-only."

---

**Page 3: "Preview - Components & States"**

A component showcase page that focuses on **functional state colors** (success, warning, error) and interactive element states. This page uses a neutral background (dark or light depending on the primary mode) and displays various UI components.

Use `mcp__figma__use_figma` to create a new page and build the component showcase.

**Colors that MUST appear on this page:**
- Success color → success alert banner, confirmation button, positive badge, check indicator
- Warning color → warning alert banner, caution badge, attention indicator
- Error color → error alert banner, destructive/danger button, error badge, validation message
- Primary color → primary buttons (default state), active input borders
- Accent color → highlighted badges, toggle active state
- Primary text color → component labels and titles
- Secondary text color → component descriptions
- Tertiary text color → placeholder text in inputs, helper text
- Background color → page background
- Surface color → component container backgrounds
- Secondary accent → default borders, dividers between sections

**Components to include:**

*Buttons row:*
- Primary button (primary color bg, dark/light text)
- Secondary button (outlined with primary color border, primary color text)
- Success button (success color bg)
- Danger button (error color bg)
- Disabled button (tertiary color bg, muted text)

*Alert banners:*
- Success alert: success color left border or bg tint, success icon placeholder circle, title (primary text), message (secondary text)
- Warning alert: warning color left border or bg tint, warning icon placeholder circle, title, message
- Error alert: error color left border or bg tint, error icon placeholder circle, title, message

*Form inputs:*
- Default input (surface bg, secondary accent border, placeholder in tertiary text)
- Focused input (surface bg, primary color border/ring)
- Error input (surface bg, error color border, error message text in error color below)
- Filled input with success indicator (surface bg, success color check mark or border)

*Badges / Tags:*
- A row of small badge rectangles: primary badge (primary color), accent badge (accent color), success badge (success color), warning badge (warning color), error badge (error color) — each with white or dark text inside

*Notification cards:*
- A small card with success color accent showing a positive notification
- A small card with warning color accent showing a warning
- A small card with error color accent showing an error

*Status indicators:*
- A row of small circles: success (success color), warning (warning color), error (error color), neutral (tertiary color) — with labels

**Layout:** Organize components in clearly labeled sections with section titles (primary text), arranged vertically with comfortable spacing. Use a neutral background. Each section should have a subtle container (surface bg) grouping related components.

---

**General rules for all 3 pages:**
- Use absolute positioning for frames within the viewport to avoid auto-layout FILL errors
- Use the chosen font family for all text (load with `figma.loadFontAsync`)
- Use placeholder text that suggests real content, not lorem ipsum
- Keep mockups simple — they are color/mood previews, not full designs
- Frame width: 1440px for desktop pages, 390px for mobile app pages
- Each `mcp__figma__use_figma` call should handle one page completely

**4f. Show the Figma URL:**
Display the Figma file URL to the user as a clickable markdown link:
> "Your color palette and preview pages are ready in Figma: [Design System - ProjectName](figma-url)
>
> The file has 4 pages:
> - **Color Palette** — color swatches with variables you can adjust
> - **Preview - [project type] (Dark)** — dark mode page mockup
> - **Preview - [project type] (Light)** — light mode page mockup
> - **Preview - Components & States** — buttons, alerts, forms, and badges showing all functional colors
>
> Every color in the palette appears on at least one preview page.
>
> After adjusting colors, say **'refresh preview'** to rebuild the preview pages with your updated palette.
> Say **'ready'** when you're done to generate DESIGN.md."

### Step 5: Wait for user to adjust in Figma

Wait for the user to respond. They might say:
- "ready" / "done" / "looks good" → proceed to Step 6
- "I changed the primary to..." → acknowledge and proceed to Step 6
- "can you change X to Y" → update the Figma file using `mcp__figma__use_figma`, then wait again
- "refresh preview" / "update preview" / "refresh" → **rebuild the preview pages with updated colors** (see below), then wait again

**Refreshing preview pages:**

When the user asks to refresh/update the previews, follow this process:

1. **Read current color variables from Figma:**
   Call `mcp__figma__get_variable_defs` with the `fileKey` and `nodeId: "0:1"`.
   Parse the returned variables into a color map (variable name → hex value).

2. **Compare with original palette:**
   Show the user which colors changed:
   > "I see these color changes:
   > - primary/desert-sand: #C4A882 → #B8956E
   > - accent/burnished-gold: #D4A54A → #E8B84A
   > Rebuilding preview pages..."

3. **Delete existing preview page content:**
   For each preview page, use `mcp__figma__use_figma` to find the page by name and remove all its children:
   ```javascript
   const page = figma.root.children.find(p => p.name === "Preview - ...");
   await figma.setCurrentPageAsync(page);
   for (const child of [...page.children]) child.remove();
   ```

4. **Rebuild all 3 preview pages:**
   Re-run the same logic as Step 4e, but using the **updated color map** read from Figma variables instead of the original proposed palette. Each page should be rebuilt in its own `mcp__figma__use_figma` call.

5. **Take screenshots and show to user:**
   Call `mcp__figma__get_screenshot` for each preview page and display them.
   > "Preview pages updated with your new colors. Here's how they look now:"

6. **Wait again:**
   Return to Step 5 and wait for the user's next action. They can adjust more, refresh again, or say "ready".

### Step 6: Read back final colors from Figma

**6a. Read updated variables:**
Call `mcp__figma__get_variable_defs` with the `fileKey` and `nodeId: "0:1"`.

**6b. Take a screenshot:**
Call `mcp__figma__get_screenshot` with the `fileKey` and `nodeId: "0:1"`.

**6c. Show what changed:**
Compare the original proposed palette with the values read back from Figma.
If there are differences, display them:
> "Here's what changed:
> - Primary: Midnight Indigo (#4F46E5) → Deep Ocean (#2563EB)
> - Background: Soft Lavender (#EEF2FF) → Cool Mist (#F0F4FF)
>
> All other colors stayed the same."

If nothing changed:
> "Looks like you kept the original palette. Proceeding with these colors."

Ask for final confirmation:
> "Ready to generate DESIGN.md with these colors?"

### Step 7: Generate DESIGN.md

Using the confirmed palette and Q&A answers, generate a complete DESIGN.md following the **DESIGN.md Template** defined below.

If `--dark` was specified or user chose "both" in Q4, include a dark mode variant in the color section.

Write the file to the output path.

### Step 8: CLAUDE.md integration

Check if a `CLAUDE.md` file exists in the project root. If it does, suggest the user add this line:
```
Design system rules are defined in DESIGN.md. Always reference it for colors, typography, spacing, and component styles. Never use values not defined in DESIGN.md.
```
If no `CLAUDE.md` exists, mention that they can create one with this instruction for best results.

### Step 9: Display summary and next steps

Show:
- Final color palette (names + hex codes)
- Font choices
- Figma file URL
- DESIGN.md output path
- Next steps:
> "Your design system is ready! Next you can:
> - `/design --apply` to apply tokens to your project files
> - `/design --update-colors` to change colors later (with Figma round-trip)
> - `/design --to-figma` to update the Figma reference page"

### Figma MCP not available

If any Figma MCP call fails in Step 4 (e.g., Figma desktop not running, MCP not connected), fall back gracefully:
> "Figma MCP is not connected. I'll generate DESIGN.md directly from the proposed palette.
> To use the Figma integration, make sure Figma desktop is running with the MCP plugin enabled.
> You can push to Figma later with `/design --to-figma`."

Then skip Steps 4-6 and go straight to Step 7 (generate DESIGN.md from the proposed palette).

---

## Mode: Text-Only Generation (`--no-figma`)

Generates DESIGN.md directly without any Figma integration. This is the fallback for users without Figma MCP.

### Step 1: Check for existing DESIGN.md

Same as Guided Flow Step 1.

### Step 2: Gather design preferences

If a description was provided, use it directly. Otherwise, run the same Q&A as Guided Flow Step 2.

### Step 3: Generate DESIGN.md

Using the description or Q&A answers, generate a complete DESIGN.md following the **DESIGN.md Template**.
Write to output path.

### Step 4: CLAUDE.md integration and summary

Same as Guided Flow Steps 8-9, but without the Figma URL in the summary.

---

## Mode: Generate from URL (`--from-url`)

### Step 1: Check for existing DESIGN.md

Same as Generate mode Step 1.

### Step 2: Extract design tokens from the URL

Use `WebFetch` with this prompt:
> "Extract all visual design characteristics from this webpage. I need: (1) All colors used with hex codes -- identify primary, secondary, accent, background, surface, text, and border colors. (2) Font families used for headings and body text. (3) Font sizes and weights observed. (4) Spacing patterns and padding values. (5) Border radius values. (6) Shadow/elevation styles. (7) Overall visual mood and atmosphere in 2-3 sentences. Return structured data, not prose."

### Step 3: Fallback if needed

If WebFetch returns insufficient color data (fewer than 3 distinct colors identified), attempt a fallback:
1. Check if `~/.cloudflare/.env` exists with credentials.
2. If yes, use the Cloudflare Browser Rendering `/json` endpoint:
   ```bash
   source ~/.cloudflare/.env
   curl -s -X POST \
     "https://api.cloudflare.com/client/v4/accounts/${CLOUDFLARE_ACCOUNT_ID}/browser-rendering/json" \
     -H "Authorization: Bearer ${CLOUDFLARE_API_TOKEN}" \
     -H "Content-Type: application/json" \
     -d '{
       "url": "<URL>",
       "prompt": "Extract all design tokens: colors (with hex codes and roles), font families, font sizes, spacing values, border-radius values, shadow definitions, and overall visual mood"
     }'
   ```
3. If credentials don't exist or the API returns empty, proceed with whatever WebFetch returned and fill in gaps with reasonable defaults inspired by the site's mood.

### Step 4: Generate DESIGN.md

Using the extracted tokens, generate a complete DESIGN.md following the **DESIGN.md Template**.

Map extracted hex values to evocative descriptive names (e.g., `#0A0A0A` becomes "Ink Black" not "color-1").
Fill in any sections where extraction was insufficient with reasonable defaults that match the site's aesthetic.

### Step 5: Write file, CLAUDE.md check, display summary

Same as Generate mode Steps 3-4.

---

## Mode: Generate from Figma (`--from-figma`)

### Step 1: Parse Figma URL

Extract `fileKey` and `nodeId` from the Figma URL:
- `figma.com/design/:fileKey/:fileName?node-id=:nodeId` — convert `-` to `:` in nodeId
- `figma.com/design/:fileKey/branch/:branchKey/:fileName` — use branchKey as fileKey
- If no `node-id` in URL, use `0:1` as default (root node)

If the URL doesn't match these patterns, show an error:
> "Please provide a valid Figma URL (e.g., https://figma.com/design/abc123/MyDesign?node-id=0-1)"

### Step 2: Check for existing DESIGN.md

Same as Generate mode Step 1.

### Step 3: Extract design tokens from Figma

Call these Figma MCP tools to gather comprehensive design data:

**3a. Get design variables (colors, spacing tokens):**
Call `mcp__figma__get_variable_defs` with `fileKey` and `nodeId`.
This returns structured variables like `{'icon/default/secondary': '#949494'}`.
Extract all color variables, spacing variables, and any other design tokens.

**3b. Get visual design context:**
Call `mcp__figma__get_design_context` with `fileKey` and `nodeId`.
This returns reference code, a screenshot, and contextual metadata including:
- Colors used in the design
- Font families, sizes, and weights
- Spacing and layout patterns
- Component structures

**3c. Get a screenshot for visual reference:**
Call `mcp__figma__get_screenshot` with `fileKey` and `nodeId`.
Use this to visually confirm the design's mood and atmosphere.

**3d. Search for design system components (optional):**
Call `mcp__figma__search_design_system` with `query: "color"` and `fileKey` to find existing design system color definitions.
Then search for `query: "button"`, `query: "input"`, `query: "card"` to find component patterns.

### Step 4: Show extracted tokens to user

Before generating DESIGN.md, display the extracted tokens to the user:
> "I found these design tokens in your Figma file:
>
> **Colors:** [list hex codes and their Figma variable names]
> **Fonts:** [list font families found]
> **Components:** [list component types detected]
>
> Would you like me to generate DESIGN.md with these, or would you like to adjust anything first?"

Wait for user confirmation. If they want to adjust, take their feedback before proceeding.

### Step 5: Generate DESIGN.md

Synthesize the Figma data into a complete DESIGN.md following the **DESIGN.md Template**.
- Map Figma variable names to evocative descriptive names (e.g., `primary/blue-500` becomes "Ocean Depth (#2563EB)")
- Preserve exact hex values from Figma -- do NOT approximate
- Use the screenshot to inform the Visual Theme & Atmosphere section
- Fill in any sections not covered by Figma data with reasonable defaults matching the design's mood

### Step 6: Write file, CLAUDE.md check, display summary

Same as Generate mode Steps 3-4.

### Error handling

If any Figma MCP tool fails, show:
> "Could not access Figma file. The Figma MCP may not be connected.
> Make sure Figma desktop is running with the MCP plugin, or configure Figma remote MCP.
> Alternatively, try `/design --from-url` with the live site URL, or use `/design --interactive`."

---

## Mode: Update Colors (`--update-colors`)

### Step 1: Verify DESIGN.md exists

Check if DESIGN.md exists at the project root (or `--output` path). If not, show:
> "No DESIGN.md found in the project root. Run `/design` first to generate one."

### Step 2: Read and display current palette

Read the existing DESIGN.md. Extract and display the "Color Palette & Roles" section in a clean, readable format showing each color name, hex code, and role.

### Step 3: Determine changes

If a change description was provided after `--update-colors` (e.g., "warmer tones, switch to green primary"), use that as direction.

If no description was provided, ask:
> "What changes would you like to make to the color palette? (e.g., 'warmer tones', 'switch primary to green', 'add a purple accent', 'make it darker')"

### Step 4: Generate new palette

Generate a new Color Palette section that:
- Respects the change request
- Maintains color harmony across the full palette
- Preserves the same functional roles (primary, secondary, accent, text, background, etc.)
- Keeps the same evocative naming style

### Step 5: Show before/after comparison

Display a clear before/after comparison of the palette:
```
Before:                          After:
Primary: Deep Indigo (#4F46E5)   Primary: Forest Emerald (#059669)
...                              ...
```

### Step 6: Offer Figma round-trip (optional)

Before confirming, offer:
> "Would you like me to push these colors to Figma so you can visually fine-tune them first?"

If yes:
1. If a Figma Design System file already exists (check if DESIGN.md has a Figma URL comment at the top), update the existing file's color variables using `mcp__figma__use_figma`.
2. If no existing Figma file, create a new one (same as Guided Flow Step 4).
3. If the Figma file has existing preview pages, rebuild them with the new colors (same refresh logic as Guided Flow Step 5 "Refreshing preview pages").
4. Show the Figma URL and wait for user to adjust. The user can say "refresh preview" to rebuild previews after further adjustments.
5. Read back updated colors from Figma (same as Guided Flow Step 6).
6. Update the before/after comparison with Figma-adjusted values.

If no (or Figma MCP not available), proceed directly to Step 7.

### Step 7: Confirm and write

Ask the user to confirm the final changes. If confirmed, update ONLY the Color Palette section in DESIGN.md, leaving all other sections unchanged.

### Step 8: Optionally update source files

Ask:
> "Would you also like to update colors in your project source files to match? This will scan for CSS files, Tailwind config, and theme files."

If yes:
1. Build a mapping of old hex values to new hex values from the before/after comparison.
2. Scan the project for files containing the old hex values:
   - `tailwind.config.*`
   - `**/*.css`
   - `**/*.tsx`, `**/*.jsx` (inline styles only)
   - `theme.*`, `src/theme.*`
3. For each file with matches, show the proposed diff.
4. Ask for confirmation before modifying each file.
5. Apply the changes and show a summary.

---

## Mode: Apply to Project (`--apply`)

### Step 1: Verify DESIGN.md exists

Same as Update Colors Step 1.

### Step 2: Parse design tokens

Read and parse DESIGN.md to extract all design tokens:
- Colors (name, hex, role)
- Typography (font families, sizes, weights)
- Spacing scale
- Border radii
- Shadows
- Breakpoints

### Step 3: Detect framework and target files

Scan the project to detect the framework and find design-relevant files:

1. **Tailwind** — look for `tailwind.config.{js,ts,mjs}`. If found:
   - Update `theme.extend.colors` with color tokens
   - Update `theme.extend.fontFamily` with typography tokens
   - Update `theme.extend.spacing` with spacing tokens
   - Update `theme.extend.borderRadius` with radii tokens
   - Update `theme.extend.boxShadow` with shadow tokens

2. **CSS Custom Properties** — look for `**/globals.css`, `**/variables.css`, `**/theme.css`. If found:
   - Generate or update `:root { }` block with CSS custom properties
   - If `--dark` or dark mode section exists, generate `[data-theme="dark"] { }` or `@media (prefers-color-scheme: dark) { }` block

3. **Theme files** — look for `theme.{js,ts}`, `src/theme.{js,ts}`. If found:
   - Update theme object with design tokens

4. **Remotion** — look for `remotion.config.ts` or check `package.json` for remotion dependency. If found:
   - Suggest a constants file `src/design-tokens.ts` exporting all tokens as JS constants
   - Suggest inline style patterns using these constants

Use `--framework` flag as an override if provided.

### Step 4: Show proposed changes

For each file to be modified, display:
- The file path
- A clear diff showing current vs. proposed content
- An explanation of what each change does

### Step 5: Confirm and apply

Ask the user to confirm before applying changes to each file.
Apply the confirmed changes and show a summary of all modifications.

### Error handling

If no design-relevant files are found:
> "No CSS, Tailwind config, or theme files found in this project. Would you like me to create a CSS custom properties file or a Tailwind config?"

---

## Mode: Push to Figma (`--to-figma`)

Creates a visual design system reference page in Figma from your DESIGN.md file. This lets you see your design tokens as visual swatches and share them with designers.

### Step 1: Verify DESIGN.md exists

Check if DESIGN.md exists at the project root (or `--output` path). If not, show:
> "No DESIGN.md found. Run `/design` first to generate one, then use `--to-figma` to push it to Figma."

### Step 2: Parse DESIGN.md tokens

Read and parse DESIGN.md to extract all design tokens:
- Colors (name, hex, role)
- Typography (font families, sizes, weights)
- Spacing scale values
- Border radii
- Shadows

### Step 3: Determine target Figma file

If a Figma URL was provided after `--to-figma`:
- Parse `fileKey` and optional `nodeId` from the URL
- The design system page will be added to this existing file

If no URL was provided:
- Call `mcp__figma__whoami` to get user info and available plans
- Call `mcp__figma__create_new_file` with:
  - `fileName`: "Design System - [project folder name]"
  - `planKey`: from whoami result (if multiple plans, ask user which one)
  - `editorType`: "design"
- Show the new file URL to the user

### Step 4: Create design system page in Figma

Use `mcp__figma__use_figma` to execute Figma Plugin API JavaScript that creates a structured design system page. The code should:

**4a. Create a new page called "Design System":**
```javascript
const page = figma.createPage();
page.name = "Design System";
await figma.setCurrentPageAsync(page);
```

**4b. Create Color Palette section:**
For each color in DESIGN.md, create a frame containing:
- A rectangle filled with the color (80x80px)
- Text label with the descriptive name
- Text label with the hex code
- Text label with the functional role
Group colors by category (Primary, Accent, Text, Functional States).
Arrange in a grid layout with proper spacing.

**4c. Create Typography section:**
For each typography level in DESIGN.md, create:
- A text node showing "The quick brown fox jumps over the lazy dog"
- Styled with the correct font family, size, and weight
- A label showing the level name and specs

**4d. Create Component Specs section:**
For each component style in DESIGN.md, create:
- A visual representation (e.g., a rectangle with the specified border-radius and shadow)
- Labels showing the specs (radius, shadow, padding values)

**4e. Create Spacing Scale section:**
Create a row of rectangles showing each spacing value in the scale, with labels.

Organize everything with auto-layout frames, consistent spacing, and clear section headers.

**4f. Create product preview mockup pages:**
Create 3 additional preview pages that together showcase **every color** in DESIGN.md. Determine the project type from DESIGN.md's "Visual Theme & Atmosphere" section (look for keywords like "landing page", "dashboard", "e-commerce", "blog", "portfolio", "mobile app"). If the project type cannot be determined, default to a landing page layout.

Follow the same multi-page mockup rules as described in the Guided Flow Step 4e:
- **Page 1: "Preview - [project type] (Dark)"** — main page mockup using dark/primary background, surface, primary, accent, secondary accent, and all text hierarchy colors
- **Page 2: "Preview - [project type] (Light)"** — same layout in light mode using light background, light surface, dark text colors
- **Page 3: "Preview - Components & States"** — UI component showcase using all functional state colors (success, warning, error) plus primary/accent in buttons, alerts, form inputs, badges, and status indicators

Cross-check the palette before building: every color must appear on at least one page.

### Step 5: Create Figma variables (optional)

Ask the user:
> "Would you also like me to create Figma variables from your DESIGN.md tokens? This makes them reusable across your Figma file."

If yes, use `mcp__figma__use_figma` to create variables:
```javascript
// Create a variable collection
const collection = figma.variables.createVariableCollection("Design System");
// Add color variables
const colorVar = figma.variables.createVariable("primary", collection.id, "COLOR");
colorVar.setValueForMode(collection.defaultModeId, {r: R/255, g: G/255, b: B/255, a: 1});
// Repeat for all colors...
```

### Step 6: Display summary

Show:
- Figma file URL (as a clickable link)
- Number of color swatches created
- Number of typography samples created
- Number of variables created (if applicable)
- Remind user they can now use these in Figma designs

---

## Mode: Layout Picker (`--layout [project-type]`)

Interactive block-picker flow for assembling page layout. Users answer section-by-section Q&A (Hero style? Features layout? Pricing?) and Claude renders the assembled layout in Figma using the colors from DESIGN.md. This mirrors the color-picking workflow but for composition instead of color.

### Step 1: Determine context

**1a. Check for existing DESIGN.md:**
- If DESIGN.md exists at the output path:
  - Parse **colors** from Section 2 (use these in the mockup)
  - Parse **font family** from Section 3
  - Parse **project type** from Section 1 (look for "landing page", "dashboard", "e-commerce", "blog", "portfolio", "mobile app" keywords)
  - Extract the Figma file URL if present as a comment (e.g., `<!-- Figma: https://... -->`)
- If DESIGN.md does not exist:
  - Tell the user: "No DESIGN.md found. I'll use placeholder colors for the layout preview. For best results, run `/design` first to establish your color system."
  - Use neutral placeholder palette: `#0F0F0F` bg, `#1A1A1A` surface, `#F5F0E8` text, `#C4A882` accent

**1b. Determine project type:**
- If user provided a type after `--layout` (e.g., `--layout landing-page`), use it
- If DESIGN.md was parsed and contained a type, use it (confirm with user first)
- Otherwise, ask: "What type of page are we designing the layout for? (landing-page, dashboard, e-commerce, blog, portfolio, mobile)"

**1c. Check Figma MCP availability:**
- Call `mcp__figma__whoami` to verify connection
- If it fails, show: "⚠️ Layout picker requires Figma MCP. Ensure Figma Desktop with the MCP plugin is running, or set up Figma Remote MCP. See README for setup."

### Step 2: Block-picker Q&A

Use the block library for the chosen project type. Ask one slot at a time. After each answer, acknowledge briefly and move to the next. Accept letter answers (A, B, C), variant names, free-text descriptions, or "skip" where skippable.

#### Block library

**Landing page:**
| Slot | Variants |
|---|---|
| **Navigation** | A: Minimal (logo + 3 links)  ·  B: Full (logo + links + CTA)  ·  C: Sticky transparent  ·  D: Centered (logo center, links sides) |
| **Hero** | A: Centered (label + H1 + sub + CTA)  ·  B: Split left/right (text ↔ media)  ·  C: Media background (full-bleed + overlay)  ·  D: Product showcase (text + product mockup) |
| **Features** | A: 3-column cards  ·  B: 2×2 grid  ·  C: Alternating rows (zigzag)  ·  D: Horizontal scroll |
| **Social proof** | A: Single testimonial quote  ·  B: 3-quote grid  ·  C: Logo bar  ·  D: Stats row  ·  E: Skip |
| **Pricing** | A: 3-tier cards  ·  B: Comparison table  ·  C: Skip |
| **FAQ** | A: Accordion  ·  B: 2-col grid  ·  C: Skip |
| **CTA Section** | A: Full-width banner  ·  B: Centered card  ·  C: Skip |
| **Footer** | A: Minimal  ·  B: Columns (grouped links)  ·  C: Centered (logo + social + copyright) |

**Dashboard:**
| Slot | Variants |
|---|---|
| **Sidebar** | A: Collapsed icons only  ·  B: Full with labels  ·  C: Right-side panel  ·  D: Skip (top-nav only) |
| **Top bar** | A: Search-forward (large search + avatar)  ·  B: Title + actions (breadcrumb + CTAs)  ·  C: Minimal (avatar only) |
| **Stat cards** | A: 4 cards row  ·  B: 2×2 grid  ·  C: Single featured + 3 small  ·  D: Skip |
| **Main content** | A: Chart + table split  ·  B: Full-width chart  ·  C: Data table only  ·  D: Card grid |
| **Secondary panels** | A: Right sidebar activity feed  ·  B: Bottom tabs  ·  C: Skip |

**E-commerce:**
| Slot | Variants |
|---|---|
| **Navigation** | A: Mega menu with categories  ·  B: Simple nav + search  ·  C: Sidebar categories + top nav |
| **Hero banner** | A: Full-width campaign image  ·  B: Slider carousel  ·  C: Split (product + text CTA) |
| **Categories** | A: Circle icons row  ·  B: Image tile grid  ·  C: Skip |
| **Product grid** | A: 4-col grid  ·  B: 3-col grid  ·  C: 2-col (larger tiles) |
| **Promo banner** | A: Full-width accent strip  ·  B: Side-by-side dual promo  ·  C: Skip |
| **Footer** | A: Columns (shop / help / about)  ·  B: Minimal + newsletter  ·  C: Centered brand |

**Blog / Portfolio:**
| Slot | Variants |
|---|---|
| **Navigation** | A: Minimal (logo + links)  ·  B: Centered brand  ·  C: Sidebar (left) |
| **Hero** | A: Featured post with image  ·  B: Text-only intro  ·  C: Project/post grid teaser |
| **Content grid** | A: 3-col cards  ·  B: 2-col magazine layout  ·  C: Single column long-form  ·  D: Masonry grid |
| **About / bio** | A: Right-side sidebar  ·  B: Full-width section  ·  C: Skip |
| **Footer** | A: Minimal  ·  B: Columns  ·  C: Centered |

**Mobile app (390×844):**
| Slot | Variants |
|---|---|
| **Status bar** | A: Standard time/battery  ·  B: Hidden |
| **Header** | A: Title + menu icon  ·  B: Back + title + action  ·  C: Search-integrated  ·  D: Large title (iOS style) |
| **Content cards** | A: List with dividers  ·  B: Card grid 2-col  ·  C: Vertical story feed  ·  D: Horizontal card scroll |
| **Bottom nav** | A: 4-tab with icons  ·  B: 5-tab with center action (FAB)  ·  C: Skip |

#### Example Q&A interaction

```
Navigation — which style?
  A) Minimal (logo + 3 links)
  B) Full (logo + links + CTA button)
  C) Sticky transparent
  D) Centered

Your choice? → user replies "B"
✓ Full navigation with CTA.

Hero — which style?
  A) Centered
  B) Split left/right
  C) Media background
  D) Product showcase

Your choice? → user replies "split"
✓ Split hero with text ↔ media.

...
```

Maintain an internal `layout_plan` object as you collect answers:
```
{
  "project_type": "landing-page",
  "slots": [
    { "slot": "nav", "variant": "B" },
    { "slot": "hero", "variant": "B" },
    { "slot": "features", "variant": "A" },
    ...
  ]
}
```

### Step 3: Preview in Figma

**3a. Determine target Figma file:**
- If DESIGN.md has a Figma URL comment, use that file (extract `fileKey`)
- Otherwise, call `mcp__figma__create_new_file` with `fileName: "Design System - [project folder name]"` (same as Guided Flow Step 4b)

**3b. Create the layout preview page:**
Use `mcp__figma__use_figma` to:
1. Create a new page: `Preview - [project type] (Custom Layout)`
2. Switch to it via `await figma.setCurrentPageAsync(page)`
3. Create a main viewport frame (1440px wide for desktop, 390px for mobile, height grows with content) with the DESIGN.md background color
4. For each slot in the `layout_plan`, render the chosen variant as a named child frame (e.g., frame name `Nav (Full)`, `Hero (Split)`, `Features (2x2 Grid)`). Name format: `[Slot] ([Variant])`
5. Stack frames vertically using absolute positioning (same pattern as existing preview pages — do NOT use auto-layout FILL, it errors inside the MCP)
6. Apply DESIGN.md colors throughout (background, surface, primary, accent, text hierarchy)
7. Use the font family from DESIGN.md (load with `figma.loadFontAsync`)
8. Call `figma.viewport.scrollAndZoomIntoView([viewport])`

**3c. Show to user:**
- Optionally call `mcp__figma__get_screenshot` to grab the page
- Display the Figma URL and summarize the assembled sections:
  > "Your layout preview is ready: [Figma URL]
  >
  > Assembled sections:
  > 1. Navigation (Full)
  > 2. Hero (Split)
  > 3. Features (3-column cards)
  > 4. Testimonial (3-quote grid)
  > 5. Pricing (3-tier cards)
  > 6. Footer (Columns)
  >
  > Options:
  > - Say **'change [slot] to [variant]'** to swap a section (e.g., 'change hero to centered')
  > - Say **'swap [slot] and [slot]'** to reorder (e.g., 'swap features and pricing')
  > - Say **'remove [slot]'** to hide a section
  > - Say **'refresh layout'** to re-read the current Figma structure (useful if you manually rearranged frames)
  > - Say **'ready'** to update DESIGN.md with the final layout"

### Step 4: Refinement loop

Wait for the user's next message. Handle these instructions:

**`change [slot] to [variant]`** — swap one slot's variant:
1. Update `layout_plan.slots` — find the slot, change variant
2. Call `mcp__figma__use_figma` to delete the old frame (match by slot name prefix) and render the new variant at the same Y-position
3. Shift subsequent frames' Y-positions if the new variant has different height
4. Confirm change to user

**`swap [slotA] and [slotB]`** — reorder two sections:
1. Swap their positions in `layout_plan.slots`
2. Call `mcp__figma__use_figma` to clear the viewport and re-render all frames in the new order
3. Confirm change

**`remove [slot]`** or **`skip [slot]`** — hide a section:
1. Remove from `layout_plan.slots`
2. Clear viewport and re-render remaining frames stacked continuously
3. Confirm

**`refresh layout`** — reconcile with user's manual Figma edits:
1. Use `mcp__figma__use_figma` to list children of the preview page (read their names and Y positions)
2. Parse each frame name `[Slot] ([Variant])` into a slot/variant pair
3. Sort by Y-position to get the new order
4. Rebuild `layout_plan.slots` from the parsed frames
5. Show user the inferred plan:
   > "Read current Figma layout:
   > 1. Nav (Full)
   > 2. Hero (Split)
   > 3. Features (2x2 Grid)   ← you swapped this in
   > 4. Footer (Minimal)
   > Pricing removed. Keep this plan?"
6. On confirm, the internal `layout_plan` is now synced; wait for next instruction

**`ready`** — exit loop:
Proceed to Step 5.

### Step 5: Update DESIGN.md

Update four sections of DESIGN.md to reflect the confirmed `layout_plan`. Leave other sections untouched.

**Section 1 (Visual Theme & Atmosphere):**
Append 1-2 sentences describing the compositional feel. Examples based on chosen variants:
- Split hero + 2×2 features: "The composition leads with a split hero that balances text and imagery, resolving through a dual-column feature grid — a rhythm of paired symmetry throughout."
- Centered hero + zigzag features: "A centered opening anchors the page, yielding to alternating zigzag content sections that create a gentle, breathing cadence."
- Media background hero + horizontal scroll features: "An immersive media-led hero gives way to horizontally scrolling feature tiles — cinematic and exploratory."

**Section 4 (Component Stylings):**
For each chosen variant, add or replace the relevant subsection with concrete measurements. Examples:

```markdown
### Hero (Split)
- **Structure:** 50/50 text-column ↔ media-column
- **Section padding:** 120px vertical, 80px horizontal
- **Column gap:** 60px
- **Text column:** max-width 560px, left-aligned
- **Media column:** aspect ratio 4:3, border-radius 8px

### Features (3-column cards)
- **Grid:** 3 equal columns, 32px gap
- **Card:** 400px max-width, 6px border-radius, 40px internal padding
- **Card background:** surface color
- **Card border:** 1px secondary accent at 30% opacity

### Pricing (3-tier cards)
- **Grid:** 3 cards, 24px gap
- **Recommended tier:** primary-color 2px border, subtle accent tint
- **Card padding:** 48px vertical, 36px horizontal
- **Tier name:** H3, 20px semibold
- **Price:** 48px bold, primary color
```

Cover Buttons, Cards, Navigation, Inputs as before, but tailor specs to the chosen variants.

**Section 5 (Layout Principles):**
Replace generic layout content with the plan-specific structure:

```markdown
### Grid & Structure
- **Max content width:** 1440px
- **Viewport padding:** 80px horizontal on desktop, 24px on mobile
- **Grid system:** 12-column with 32px gutters

### Section Order
1. Navigation (Full)
2. Hero (Split)
3. Features (3-column cards)
4. Social Proof (3-quote grid)
5. Pricing (3-tier cards)
6. CTA Section (Centered card)
7. Footer (Columns)

### Section Spacing
- **Between major sections:** 120-140px vertical
- **Within section content:** 60px between title row and content rows
- **Card/component gap:** 32px

### Rhythm
[Description matching the variants chosen — e.g., "The split hero sets a horizontal balance that the 3-col features continue, before pricing anchors back with vertical symmetry."]
```

**Section 9 (Agent Prompt Guide):**
Add a layout-specific ready-to-use prompt to the prompts list:
```markdown
- "Build a [project type] with the following sections in order: [section order from plan]. Use the DESIGN.md colors, typography, and the exact variant specs in Section 4. Apply the section spacing rules from Section 5."
```

Write the updated DESIGN.md to disk.

### Step 6: Summary

Show:
- Final section order (numbered list)
- Figma URL with the layout preview page highlighted
- DESIGN.md path and which sections were updated (1, 4, 5, 9)
- Next steps:
  > "Your layout is locked in. Next you can:
  > - `/design --apply` to push the tokens and layout specs to your project's CSS/Tailwind config
  > - `/design --layout` again to experiment with a different composition (previous preview page will be reused)
  > - `/design --update-colors` to tweak colors against this new layout"

### Error handling (specific to `--layout`)

- `--layout` and Figma MCP not available → "Layout picker requires Figma MCP. Either set up Figma (see README) or describe your layout preferences manually — I can still update DESIGN.md sections 4 and 5 based on your description."
- `--layout <invalid-type>` → "Supported project types: landing-page, dashboard, e-commerce, blog, portfolio, mobile. Which matches your project?"
- `refresh layout` but no preview page exists → "No layout preview page found. Let's start with Step 2 Q&A to assemble one."
- User input that matches no slot or variant → ask them to clarify, suggest the closest match

---

## Mode: Interactive Figma Sync (`--sync-figma`)

This is the most interactive mode. It creates a bidirectional workflow where you pick/adjust colors in Figma, Claude reads them back, and generates an accurate DESIGN.md. Great for designers who want to visually explore colors before committing.

### Step 1: Parse Figma URL

Extract `fileKey` and `nodeId` from the provided Figma URL.
- `figma.com/design/:fileKey/:fileName?node-id=:nodeId` — convert `-` to `:` in nodeId
- If no `node-id` in URL, use `0:1` as default
- If URL is missing, show error: "Please provide a Figma URL: `/design --sync-figma <figma-url>`"

### Step 2: Read current state from Figma

Call these tools in parallel to gather the current state:

**2a. Get design variables:**
Call `mcp__figma__get_variable_defs` with `fileKey` and `nodeId`.

**2b. Get visual context:**
Call `mcp__figma__get_design_context` with `fileKey` and `nodeId`.

**2c. Get screenshot:**
Call `mcp__figma__get_screenshot` with `fileKey` and `nodeId`.

### Step 3: Present findings and ask for direction

Display to the user:
> "Here's what I found in your Figma file:
>
> **Colors detected:**
> - [list each color with hex code and where it appears]
>
> **Fonts detected:**
> - [list font families and sizes]
>
> **Screenshot:** [show the screenshot]
>
> What would you like to do?
> 1. **Use these colors as-is** — I'll generate DESIGN.md from what's in Figma
> 2. **Adjust in Figma first** — Go adjust colors in Figma, then tell me when ready and I'll re-read
> 3. **Let me suggest changes** — Tell me what you want to change and I'll update both DESIGN.md and Figma
> 4. **Start fresh** — Describe your desired aesthetic and I'll create a palette, push it to Figma, then generate DESIGN.md"

### Step 4: Handle user choice

**Choice 1: Use as-is**
Generate DESIGN.md from the extracted Figma data, same as `--from-figma` mode Steps 5-6.

**Choice 2: Adjust in Figma first**
Tell the user:
> "Go ahead and adjust your colors in Figma. When you're done, just say 'ready' or 'done' and I'll re-read your file."

When the user says they're ready:
- Re-call `mcp__figma__get_variable_defs` and `mcp__figma__get_design_context` to get updated values
- Show the updated colors and ask for confirmation
- Generate DESIGN.md from the updated data

**Choice 3: Suggest changes**
Ask the user what they want to change (e.g., "warmer tones", "darker background", "add a purple accent").
- Generate a new color palette based on the request, using existing Figma colors as a starting point
- Show the proposed palette to the user
- If approved, push the new colors to Figma using `mcp__figma__use_figma`:
  - Find existing color rectangles/fills and update them
  - Or if the file has Figma variables, update the variable values:
    ```javascript
    const collections = figma.variables.getLocalVariableCollections();
    // Find and update color variables
    const variable = figma.variables.getVariableById(variableId);
    variable.setValueForMode(modeId, {r: R/255, g: G/255, b: B/255, a: 1});
    ```
- If preview pages exist in the file, rebuild them with the new colors (same refresh logic as Guided Flow Step 5 "Refreshing preview pages")
- Re-read from Figma to confirm changes took effect
- Generate DESIGN.md from the confirmed state

**Choice 4: Start fresh**
Ask for an aesthetic description, then:
1. Generate a complete color palette
2. Show the palette to the user for approval
3. Push the approved palette to Figma using `mcp__figma__use_figma`:
   - Create color swatches on a new page (same as `--to-figma` Step 4b)
   - Create Figma variables for each color
4. Generate DESIGN.md from the pushed palette
5. Show Figma file URL and DESIGN.md summary

### Step 5: Iterative refinement loop

After generating DESIGN.md, ask:
> "DESIGN.md has been generated. Would you like to:
> - **Refine** — adjust specific colors or sections
> - **Push updates to Figma** — sync any DESIGN.md changes back to Figma
> - **Done** — finish the sync workflow"

If **Refine**: Go back to Step 3, but this time show the DESIGN.md palette instead of raw Figma data.

If **Push updates to Figma**: Use `mcp__figma__use_figma` to update Figma variables/swatches to match the current DESIGN.md palette.

If **Done**: Show final summary and exit.

### Error handling

- If Figma MCP is not available, show: "Figma MCP is not connected. Make sure Figma desktop is running with the MCP plugin enabled, or set up Figma remote MCP. You can use `/design --from-url` or `/design --interactive` instead."
- If reading from Figma returns no colors, show: "No colors found in this Figma file. Would you like to start fresh with `/design --interactive` or push a new palette with `/design --to-figma`?"

---

## DESIGN.md Template

When generating a DESIGN.md, produce a file with EXACTLY these sections in this order. Follow these rules strictly:

### Naming rules
- Every color entry MUST use this format: `**Descriptive Name** (#HEXCODE) -- functional purpose`
- Use evocative, natural language names: "Midnight Ink", "Sun-kissed Coral", "Whisper Gray" -- NOT "color-primary", "bg-1", "gray-300"
- Use natural language for design descriptions: "whisper-soft shadow" NOT "shadow-sm", "subtly rounded corners" NOT "8px" or "rounded-md"
- Always explain the functional purpose: when and where to use each token

### Color harmony rules
- Generate a coherent, harmonious palette -- colors should work together
- Minimum required: 1 primary, 1 secondary, 1 accent, background, surface, 3+ text hierarchy colors, success, warning, error
- Ensure sufficient contrast between text and background colors (WCAG AA minimum)
- If `--dark` flag is set, include a parallel dark mode palette subsection

### Template sections

```markdown
# DESIGN.md

## 1. Visual Theme & Atmosphere

[2-4 sentences capturing the mood, energy, and aesthetic philosophy. Use evocative language
that conveys the emotional tone. Mention design density (airy vs. compact), visual rhythm,
and the intended feeling for users.]

**Key Characteristics:**
- [4-6 bullet points defining the visual language]

## 2. Color Palette & Roles

### Primary Foundation
- **[Name]** (#HEXCODE) -- [functional purpose: when/where to use]
- **[Name]** (#HEXCODE) -- [functional purpose]

### Accent & Interactive
- **[Name]** (#HEXCODE) -- [functional purpose: CTAs, active states, links]

### Typography & Text Hierarchy
- **[Name]** (#HEXCODE) -- [primary text, headlines]
- **[Name]** (#HEXCODE) -- [secondary text, descriptions]
- **[Name]** (#HEXCODE) -- [tertiary: borders, dividers, placeholders]

### Functional States
- **[Name]** (#HEXCODE) -- [success: confirmations, positive indicators]
- **[Name]** (#HEXCODE) -- [warning: caution states, attention needed]
- **[Name]** (#HEXCODE) -- [error: failures, destructive actions, critical alerts]

[If --dark flag: ### Dark Mode Variant with parallel palette]

## 3. Typography Rules

**Primary Font Family:** [Font Name]
**Character:** [1-2 sentence description of the font's personality]

[Optional: **Secondary Font Family:** for headings or accents]

### Hierarchy & Weights
| Level | Weight | Size | Line Height | Letter Spacing | Usage |
|-------|--------|------|-------------|----------------|-------|
| Display / H1 | [weight] | [size] | [value] | [value] | [where to use] |
| H2 | [weight] | [size] | [value] | [value] | [where to use] |
| H3 | [weight] | [size] | [value] | [value] | [where to use] |
| H4 | [weight] | [size] | [value] | [value] | [where to use] |
| Body | [weight] | [size] | [value] | normal | [where to use] |
| Small / Caption | [weight] | [size] | [value] | [value] | [where to use] |
| CTA / Button | [weight] | [size] | [value] | [value] | [where to use] |

## 4. Component Stylings

### Buttons
- **Shape:** [description of corner treatment]
- **Primary:** [background color name + hex], [text color], padding [values]
- **Secondary:** [style description -- outlined, ghost, etc.]
- **Hover state:** [transition description]
- **Focus state:** [accessibility description]
- **Disabled state:** [visual treatment]

### Cards & Containers
- **Corners:** [description + radius value]
- **Background:** [color name + hex]
- **Shadow:** [default and hover states]
- **Padding:** [internal spacing description]
- **Border:** [if applicable]

### Navigation
- **Style:** [layout description]
- **Active indicator:** [how active state is shown]
- **Typography:** [weight, casing, spacing for nav items]

### Inputs & Forms
- **Border:** [color, width]
- **Background:** [default and focus states]
- **Focus state:** [border/ring treatment]
- **Error state:** [visual treatment]
- **Corners:** [should match buttons for consistency]
- **Padding:** [comfortable touch target sizing]

### [Additional components as relevant to the project type]

## 5. Layout Principles

### Grid & Structure
- **Max content width:** [value]
- **Grid system:** [columns, gutters]
- **Common layouts:** [typical column arrangements]

### Spacing Scale
- **Base unit:** [value, typically 4px or 8px]
- **Scale:** [list of common spacing values with names]
- **Section margins:** [space between major sections]
- **Component gaps:** [space between related elements]

### Alignment & Rhythm
- [Text alignment rules]
- [Visual weight distribution]
- [Whitespace philosophy]

## 6. Depth & Elevation

### Shadow Scale
- **Subtle:** [shadow definition] -- [when to use]
- **Medium:** [shadow definition] -- [when to use]
- **Pronounced:** [shadow definition] -- [when to use]
- **Overlay:** [shadow definition] -- [when to use]

### Surface Hierarchy
- [Description of how surfaces stack and relate]

## 7. Do's and Don'ts

### Do
- [Rule with brief rationale]
- [Rule with brief rationale]
- [Rule with brief rationale]
- [Rule with brief rationale]

### Don't
- [Anti-pattern with brief rationale]
- [Anti-pattern with brief rationale]
- [Anti-pattern with brief rationale]
- [Anti-pattern with brief rationale]

## 8. Responsive Behavior

### Breakpoints
| Name | Width | Columns | Notes |
|------|-------|---------|-------|
| Mobile | <768px | [cols] | [adaptation notes] |
| Tablet | 768-1024px | [cols] | [adaptation notes] |
| Desktop | 1024-1440px | [cols] | [adaptation notes] |
| Large | >1440px | [cols] | [adaptation notes] |

### Touch & Accessibility
- Minimum touch target: 44x44px
- [Typography scaling strategy]
- [Component adaptation rules]

## 9. Agent Prompt Guide

### Quick Reference
| Token | Value | Use |
|-------|-------|-----|
| Primary | #HEXCODE | [brief use] |
| Secondary | #HEXCODE | [brief use] |
| Accent | #HEXCODE | [brief use] |
| Background | #HEXCODE | [brief use] |
| Text | #HEXCODE | [brief use] |

### Ready-to-Use Prompts
- "[prompt for generating a typical page/component using this design system]"
- "[prompt for generating another common element]"
- "[prompt for generating a third element]"
```

---

## Error handling

- If no arguments and no flags are provided, enter the **guided** mode (Figma-first flow). Do NOT show help text -- start the Q&A.
- If `--from-url` is provided without a URL, show: "Please provide a URL after --from-url."
- If `--from-url` URL is not valid (doesn't start with http:// or https://), show: "Please provide a valid URL starting with http:// or https://"
- If `--from-figma` URL is not a Figma URL, show: "Please provide a valid Figma URL (e.g., https://figma.com/design/abc123/MyDesign)"
- If `--from-figma` and Figma MCP fails, show: "Could not access Figma file. Make sure Figma desktop is running with MCP plugin. Try `/design --from-url` or `/design --interactive` instead."
- If `--update-colors` or `--apply` but no DESIGN.md exists, show: "No DESIGN.md found in the project root. Run `/design` first to generate one."
- If `--apply` but no design-relevant files found, show: "No CSS, Tailwind config, or theme files found. Would you like me to create one?"
- If DESIGN.md already exists when generating a new one, ask before overwriting.
- If `--to-figma` but no DESIGN.md exists, show: "No DESIGN.md found. Run `/design` first to generate one, then use `--to-figma` to push it to Figma."
- If `--to-figma` or `--sync-figma` and Figma MCP is not available, show: "Figma MCP is not connected. Make sure Figma desktop is running with the MCP plugin enabled, or set up Figma remote MCP."
- If `--sync-figma` is provided without a URL, show: "Please provide a Figma URL: `/design --sync-figma <figma-url>`"
- If `--layout` is used with an unsupported project type, show: "Supported types: landing-page, dashboard, e-commerce, blog, portfolio, mobile. Which matches your project?"
- If `--layout` is used and Figma MCP is not available, show: "Layout picker requires Figma MCP. Either set up Figma (see README) or describe your layout preferences manually — I can still update DESIGN.md sections 4 and 5 from your description."
- If user says `refresh layout` but no layout preview page exists, show: "No layout preview page found. Let's start with the block-picker Q&A to build one."
