# Google Stitch - DESIGN.md Documentation

> Compiled from [stitch.withgoogle.com/docs/design-md](https://stitch.withgoogle.com/docs/design-md/overview/) and related sources.
>
> Official docs pages (requires Google authentication):
> - [Overview](https://stitch.withgoogle.com/docs/design-md/overview/)
> - [Format](https://stitch.withgoogle.com/docs/design-md/format/)
> - [Usage](https://stitch.withgoogle.com/docs/design-md/usage/)
> - [MCP Guide](https://stitch.withgoogle.com/docs/mcp/guide/)
> - [MCP Setup](https://stitch.withgoogle.com/docs/mcp/setup/)
> - [Prompting Guide](https://stitch.withgoogle.com/docs/learn/prompting/)

---

## Table of Contents

1. [Overview](#1-overview)
2. [What is DESIGN.md?](#2-what-is-designmd)
3. [Format Specification](#3-format-specification)
4. [Creating a DESIGN.md](#4-creating-a-designmd)
5. [Usage Guide](#5-usage-guide)
6. [Using with AI Coding Agents](#6-using-with-ai-coding-agents)
7. [Stitch Design-MD Skill](#7-stitch-design-md-skill)
8. [Example DESIGN.md](#8-example-designmd)
9. [Best Practices](#9-best-practices)
10. [Related Resources](#10-related-resources)

---

## 1. Overview

**Stitch** is an AI-native platform from Google Labs that enables users to create, iterate, and collaborate on high-fidelity UI designs. It uses Gemini to generate UIs for mobile and web applications, making design ideation fast and easy.

**DESIGN.md** is a plain-text design system document in Markdown format that encodes your entire design system -- colors, typography, spacing, component styles, and branding guidelines -- for AI agents to read and apply. It serves as a "design system manifest" that stores brand visual rules as persistent context for Gemini-powered UI generation.

### Key Characteristics

- **Plain Markdown** -- no special syntax, JSON schemas, or tooling required
- **Machine-readable and human-readable** -- editable in any text editor
- **Version controllable** -- works with Git for team collaboration
- **Portable** -- transferable between Stitch projects, coding agents, and tools
- **Persistent context** -- every time you prompt Stitch, it reads DESIGN.md first

### Why Markdown?

Markdown is the right format because large language models understand it natively. Structure, semantics, and human readability are all built in. No build process or schema validation is needed.

### DESIGN.md vs AGENTS.md vs CLAUDE.md

| File | Purpose |
|------|---------|
| `DESIGN.md` | Instructions for design/coding agents on how the project should **look and feel** |
| `AGENTS.md` | Instructions for coding agents on how to **build** the project |
| `CLAUDE.md` | Claude Code-specific instructions for project conventions and rules |

---

## 2. What is DESIGN.md?

DESIGN.md is a structured Markdown document that Google Stitch generates alongside its UI designs, documenting the complete design system in a format that AI coding agents can read and apply when writing code.

### What It Solves

AI-generated interfaces often fail to match brand guidelines because AI coding agents have no persistent awareness of your design system unless you give them something structured to reference. DESIGN.md provides that structured reference.

### How Gemini Uses It

DESIGN.md functions as persistent system context. When you submit a prompt to Stitch, it passes your prompt plus the full contents of DESIGN.md as context. Gemini uses these constraints to generate output applying specific hex values, fonts, and spacing rather than making arbitrary decisions.

### Important Limitation

The model applies DESIGN.md with "probabilistic judgment, not rule-based precision." Iteration remains necessary; the file guides rather than guarantees exact compliance.

### Relationship to Traditional Systems

DESIGN.md complements (rather than replaces) traditional design systems. Mature teams often maintain both Figma libraries and DESIGN.md files for different workflows.

---

## 3. Format Specification

The DESIGN.md file follows a structured format with the following major sections:

### Section 1: Visual Theme & Atmosphere

Description of the mood, density, and aesthetic philosophy of the design.

- Use evocative adjectives to describe the mood (e.g., "Airy," "Dense," "Minimalist," "Utilitarian")
- Describe the overall feeling and emotional tone
- Define key characteristics of the visual language
- Explain the design philosophy

**Example:**
```markdown
## Visual Theme & Atmosphere

The interface embodies a sophisticated, minimalist sanctuary that marries 
pristine simplicity with refined visual language. The design philosophy is 
gallery-like and photography-first, allowing each item to command attention.

**Key Characteristics:**
- Expansive whitespace creating generous breathing room
- Clean, architectural grid system
- Photography-first presentation with minimal UI interference
- Refined, understated interactive elements
```

### Section 2: Color Palette & Roles

Colors listed by descriptive name, hex code, and functional role. Semantic naming helps the model understand intent, not just appearance.

**Structure:**
- Primary foundation colors (backgrounds, surfaces)
- Accent & interactive colors (CTAs, active states)
- Typography & text hierarchy colors
- Functional states (success, error, warning, info)

**Example:**
```markdown
## Color Palette & Roles

### Primary Foundation
- **Warm Barely-There Cream** (#FCFAFA) -- Primary background color
- **Crisp Very Light Gray** (#F5F5F5) -- Secondary surface color for cards

### Accent & Interactive
- **Deep Muted Teal-Navy** (#294056) -- Primary CTA buttons, active nav links

### Typography & Text Hierarchy
- **Charcoal Near-Black** (#2C2C2C) -- Primary text, headlines
- **Soft Warm Gray** (#6B6B6B) -- Secondary text, descriptions
- **Ultra-Soft Silver Gray** (#E0E0E0) -- Borders, dividers

### Functional States
- **Success Moss** (#10B981) -- Confirmations, positive indicators
- **Alert Terracotta** (#EF4444) -- Errors, warnings, critical alerts
- **Informational Slate** (#64748B) -- Neutral system messages
```

**Key Rule:** Use specific values, not vague descriptions. Write `#1A73E8` rather than "a blue that feels trustworthy."

### Section 3: Typography Rules

Font families, type scale, weight conventions, line height, and letter spacing.

**Structure:**
- Primary font family and character description
- Complete hierarchy with weights and sizes (H1-H6, body, captions, CTAs)
- Spacing principles (letter-spacing, line-height)
- Vertical rhythm rules

**Example:**
```markdown
## Typography Rules

**Primary Font Family:** Manrope
**Character:** Modern, geometric sans-serif with gentle humanist warmth.

### Hierarchy & Weights
- **Display Headlines (H1):** Semi-bold (600), 2.75-3.5rem, letter-spacing 0.02em
- **Section Headers (H2):** Semi-bold (600), 2-2.5rem, letter-spacing 0.01em
- **Subsection Headers (H3):** Medium (500), 1.5-1.75rem
- **Body Text:** Regular (400), 1rem, line-height 1.7
- **Small Text/Meta:** Regular (400), 0.875rem, line-height 1.5
- **CTA Buttons:** Medium (500), 1rem, letter-spacing 0.01em
```

### Section 4: Component Stylings

Descriptions for UI elements including buttons, cards, inputs, navigation, and other components. Include variants, states, and visual behaviors.

**Structure per component:**
- Shape / corner style
- Colors and backgrounds
- Padding and sizing
- Hover, focus, and active states
- Transitions and animations

**Example:**
```markdown
## Component Stylings

### Buttons
- **Shape:** Subtly rounded corners (8px radius)
- **Primary CTA:** #294056 background, white text, padding 0.875rem x 2rem
- **Hover:** Subtle darkening, 250ms ease-in-out transition
- **Focus:** Soft outer glow for accessibility
- **Secondary:** Outlined style, transparent background, border in primary color

### Cards & Containers
- **Corners:** Gently rounded (12px radius)
- **Shadow:** Flat by default; whisper-soft on hover (0 2px 8px rgba(0,0,0,0.06))
- **Padding:** Generous 2-2.5rem internal spacing
- **Image:** Full-bleed at top, square or 4:3 ratio

### Navigation
- **Style:** Clean horizontal layout, 2-3rem spacing between items
- **Typography:** Medium (500), subtle uppercase, letter-spacing 0.06em
- **Active Indicator:** Thin 2px underline in primary accent color

### Inputs & Forms
- **Border:** 1px in Soft Warm Gray
- **Focus:** Border shifts to accent color with subtle glow
- **Corners:** Matching button roundness (8px) for consistency
- **Padding:** 0.875rem vertical, 1.25rem horizontal
```

### Section 5: Layout Principles

Whitespace strategy, grid system, container widths, spacing scale, breakpoints, and alignment rules.

**Structure:**
- Grid system and max content width
- Spacing scale (base unit, common values)
- Section margins and padding
- Responsive breakpoints
- Alignment and visual balance rules
- Touch target sizes

**Example:**
```markdown
## Layout Principles

### Grid & Structure
- **Max Content Width:** 1440px
- **Grid:** Responsive 12-column, fluid gutters (24px mobile, 32px desktop)
- **Product Grid:** 4 cols desktop, 3 cols tablet, 2 cols large mobile, 1 col mobile

### Whitespace Strategy
- **Base Unit:** 8px for micro-spacing, 16px for component spacing
- **Section Margins:** 5-8rem between major sections
- **Edge Padding:** 1.5rem mobile, 3rem desktop

### Breakpoints
- Mobile: <768px
- Tablet: 768-1024px
- Desktop: 1024-1440px
- Large Desktop: >1440px

### Touch & Accessibility
- Minimum touch target: 44x44px (WCAG AAA compliant)
```

### Section 6: Depth & Elevation (Optional)

Shadow systems and surface hierarchies.

### Section 7: Do's and Don'ts (Recommended)

Explicit guardrails preventing problematic AI choices.

**Example:**
```markdown
## Do's and Don'ts

### Do
- Use the primary color sparingly for maximum impact
- Maintain consistent border-radius across related components
- Use semantic color names in prompts

### Don't
- Mix rounded and sharp corners in the same view
- Use more than 3 font weights on a single screen
- Place text directly on busy images without an overlay
```

### Section 8: Responsive Behavior (Optional)

Breakpoints, touch targets, and adaptation strategies.

### Section 9: Agent Prompt Guide (Optional)

Quick color references and ready-to-use prompts for consistent generation.

**Example:**
```markdown
## Agent Prompt Guide

### Color References
- Primary CTA: "Deep Muted Teal-Navy (#294056)"
- Background: "Warm Barely-There Cream (#FCFAFA)"
- Text: "Charcoal Near-Black (#2C2C2C)"

### Component Prompts
- "Create a product card with gently rounded corners, full-bleed image, and whisper-soft shadow on hover"
- "Design a primary button in Deep Muted Teal-Navy (#294056) with subtle rounded corners"
```

---

## 4. Creating a DESIGN.md

There are three methods to create a DESIGN.md:

### Method 1: Agent-Generated (via Stitch)

Describe your aesthetic direction to Stitch:

> "Create a playful coffee shop app with warm colors, rounded corners, and a friendly feel"

Stitch translates this into a complete design system including hex color codes, typography specifications, component styles, and spacing patterns.

### Method 2: Derived from Existing Branding

Upload a brand image or provide a URL to Stitch. The tool extracts your palette, typography, and style patterns to construct DESIGN.md from your established visual language.

### Method 3: Hand-Authored

Advanced users write DESIGN.md directly in markdown with no special syntax required. Specify exact colors (hex codes), font families, button styles, card treatments, and explicit rules.

### Documentation Principles

When writing DESIGN.md content:

- **Use natural language, not technical specs** -- describe `border-radius: 50%` as "pill-shaped" and `rounded-lg` as "subtly rounded corners"
- **Use evocative adjectives** -- "whisper-soft shadow" instead of `shadow-sm`
- **Always include hex codes** alongside descriptive names: "Ocean-deep Cerulean (#0077B6)"
- **Explain functional purposes** -- don't just list values, explain when and how to use them
- **Be specific about intent** -- explain rationale to help model judgment
- **Cover edge cases explicitly** -- include do's and don'ts

---

## 5. Usage Guide

### Setting Up DESIGN.md in Stitch

1. Open Stitch and set up a DESIGN.md before you start building screens (even a quick agent-generated one from a vibe description)
2. Every time you prompt Stitch to generate a new screen or component, it reads DESIGN.md first
3. Without DESIGN.md, each screen operates independently; with it, they form a cohesive system

### Enforcement

When Stitch reads your DESIGN.md, every screen it generates follows the same visual rules:
- Your color palette
- Your typography
- Your component patterns
- Your do's and don'ts

### Exporting DESIGN.md

Once your design is finalized, export the DESIGN.md from Google Stitch. It will contain all design tokens and system specifications.

### Importing DESIGN.md

The file is portable:
- Import into other Stitch projects
- Feed to coding agents through the MCP server
- Share with your team as a single source of truth

### Updating

1. Return to Google Stitch and update the design
2. Re-export the DESIGN.md file
3. Replace the old file in your project and commit the update
4. Coding agents read the file fresh at each session start

### Credit Conservation

Direct edits to DESIGN.md and consistent styling reduce the need for full regenerations, significantly conserving daily credits throughout a project lifecycle.

---

## 6. Using with AI Coding Agents

### With Claude Code

1. **Export** DESIGN.md from Google Stitch
2. **Save** to your project root directory (keeping it at root ensures Claude Code finds it)
3. **Create/update CLAUDE.md** with explicit instructions:
   ```
   Never use any color, spacing, or font value not explicitly defined in DESIGN.md
   ```
4. **Run Claude Code** from the project root -- it loads CLAUDE.md at session start
5. **Generate components** using plain language, mentioning DESIGN.md explicitly
6. **Review outputs** against Stitch designs for accuracy

### With Other AI Coding Agents

DESIGN.md is compatible with:
- **Cursor** -- place in project root
- **Gemini CLI** -- reference via context
- **Antigravity** -- use with Stitch MCP server
- **Any LLM-based agent** -- provide as context

### Translating to CSS Frameworks

Convert DESIGN.md values into your CSS framework config:

**Tailwind Example:**
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: '#294056',
        background: '#FCFAFA',
        surface: '#F5F5F5',
        'text-primary': '#2C2C2C',
        'text-secondary': '#6B6B6B',
      },
      fontFamily: {
        sans: ['Manrope', 'sans-serif'],
      },
      borderRadius: {
        button: '8px',
        card: '12px',
      },
    },
  },
}
```

### Via Stitch MCP Server

The Stitch MCP (Model Context Protocol) server allows AI coding agents to:
- Access project design data programmatically
- Fetch screens, design tokens, and metadata
- Generate DESIGN.md files directly from Stitch projects

**Setup:** See [MCP Setup Guide](https://stitch.withgoogle.com/docs/mcp/setup/) and [MCP Guide](https://stitch.withgoogle.com/docs/mcp/guide/)

---

## 7. Stitch Design-MD Skill

The `design-md` skill is an Agent Skills tool that analyzes Stitch UI projects and automatically generates comprehensive DESIGN.md documentation.

### Installation

```bash
npx skills add google-labs-code/stitch-skills --skill design-md --global
```

### Repository

[github.com/google-labs-code/stitch-skills](https://github.com/google-labs-code/stitch-skills/tree/main/skills/design-md)

### How It Works

The skill executes a five-stage pipeline:

1. **Retrieval** -- Fetches project screens, HTML code, and design metadata using Stitch MCP Server
2. **Extraction** -- Identifies design tokens (colors, typography, spacing, component patterns)
3. **Translation** -- Converts CSS/Tailwind technical values into natural design language
4. **Synthesis** -- Creates a comprehensive DESIGN.md file using semantic design system format
5. **Alignment** -- Ensures adherence to Stitch Effective Prompting Guide principles

### Skill Structure

```
skills/design-md/
  SKILL.md       -- Core instructions and workflow logic
  examples/      -- Sample DESIGN.md output files
  README.md      -- Documentation and usage guide
```

### Example Prompt

> "Analyze my Furniture Collection project's Home screen and generate a comprehensive DESIGN.md file documenting the design system."

---

## 8. Example DESIGN.md

### Minimal Example

```markdown
## Colors
- Primary: #1A73E8
- Primary Dark: #1557B0
- Secondary: #34A853
- Background: #FFFFFF
- Surface: #F8F9FA
- Error: #EA4335
- Text Primary: #202124
- Text Secondary: #5F6368

## Typography
- Font Family: Inter, sans-serif
- Heading 1: 32px, 700 weight
- Heading 2: 24px, 600 weight
- Body: 16px, 400 weight
- Caption: 12px, 400 weight

## Spacing
- Base unit: 8px
- Values in use: 4, 8, 16, 24, 32, 48px

## Components
- Button border radius: 8px
- Card border radius: 12px
- Card shadow: 0 1px 3px rgba(0,0,0,0.12)
- Input border: 1px solid #DADCE0
```

### Full Example (Furniture Collections)

See the complete example from `google-labs-code/stitch-skills`:

```markdown
# Design System: Furniture Collections List
**Project ID:** 13534454087919359824

## 1. Visual Theme & Atmosphere

The Furniture Collections List embodies a sophisticated, minimalist sanctuary 
that marries the pristine simplicity of Scandinavian design with the refined 
visual language of luxury editorial presentation. The interface feels spacious 
and tranquil, prioritizing breathing room and visual clarity above all else.

**Key Characteristics:**
- Expansive whitespace creating generous breathing room between elements
- Clean, architectural grid system with structured content blocks
- Photography-first presentation with minimal UI interference
- Whisper-soft visual hierarchy that guides without shouting
- Refined, understated interactive elements
- Professional yet inviting editorial tone

## 2. Color Palette & Roles

### Primary Foundation
- **Warm Barely-There Cream** (#FCFAFA) -- Primary background
- **Crisp Very Light Gray** (#F5F5F5) -- Secondary surface for cards

### Accent & Interactive
- **Deep Muted Teal-Navy** (#294056) -- Primary CTA buttons, active links

### Typography & Text Hierarchy
- **Charcoal Near-Black** (#2C2C2C) -- Primary text, headlines
- **Soft Warm Gray** (#6B6B6B) -- Body copy, descriptions
- **Ultra-Soft Silver Gray** (#E0E0E0) -- Borders, dividers

### Functional States
- **Success Moss** (#10B981) -- Availability, confirmations
- **Alert Terracotta** (#EF4444) -- Low stock, errors
- **Informational Slate** (#64748B) -- Neutral system messages

## 3. Typography Rules

**Primary Font Family:** Manrope
**Character:** Modern, geometric sans-serif with gentle humanist warmth.

### Hierarchy & Weights
- **Display Headlines (H1):** Semi-bold (600), 2.75-3.5rem, letter-spacing 0.02em
- **Section Headers (H2):** Semi-bold (600), 2-2.5rem, letter-spacing 0.01em
- **Subsection Headers (H3):** Medium (500), 1.5-1.75rem
- **Body Text:** Regular (400), 1rem, line-height 1.7
- **Small Text/Meta:** Regular (400), 0.875rem, line-height 1.5
- **CTA Buttons:** Medium (500), 1rem, letter-spacing 0.01em

## 4. Component Stylings

### Buttons
- **Shape:** Subtly rounded corners (8px/0.5rem radius)
- **Primary CTA:** Deep Muted Teal-Navy (#294056) background, white text
- **Padding:** 0.875rem vertical, 2rem horizontal
- **Hover:** Subtle darkening, 250ms ease-in-out transition
- **Focus:** Soft outer glow for keyboard accessibility
- **Secondary:** Outlined style, transparent background

### Cards & Product Containers
- **Corners:** Gently rounded (12px/0.75rem radius)
- **Shadow:** Flat by default; on hover: 0 2px 8px rgba(0,0,0,0.06)
- **Padding:** Generous 2-2.5rem
- **Image:** Full-bleed top, square or 4:3 ratio
- **Hover:** Gentle lift (translateY -4px) with enhanced shadow

### Navigation
- **Style:** Clean horizontal, 2-3rem spacing between items
- **Typography:** Medium (500), subtle uppercase, letter-spacing 0.06em
- **Active:** Thin 2px underline in Deep Muted Teal-Navy
- **Mobile:** Hamburger menu with sliding drawer

### Inputs & Forms
- **Border:** 1px in Soft Warm Gray
- **Background:** Warm Barely-There Cream, transitions to Very Light Gray on focus
- **Corners:** 8px/0.5rem for consistency with buttons
- **Focus:** Border shifts to Deep Muted Teal-Navy with subtle glow
- **Padding:** 0.875rem vertical, 1.25rem horizontal

## 5. Layout Principles

### Grid & Structure
- **Max Content Width:** 1440px
- **Grid:** Responsive 12-column, fluid gutters (24px mobile, 32px desktop)
- **Product Grid:** 4 cols large desktop, 3 desktop, 2 tablet, 1 mobile

### Whitespace Strategy
- **Base Unit:** 8px micro-spacing, 16px component spacing
- **Vertical Rhythm:** 2rem (32px) between related elements
- **Section Margins:** 5-8rem (80-128px) between major sections
- **Edge Padding:** 1.5rem mobile, 3rem tablet/desktop
- **Hero Sections:** 8-12rem top/bottom padding

### Breakpoints
- Mobile: <768px
- Tablet: 768-1024px
- Desktop: 1024-1440px
- Large Desktop: >1440px

### Touch & Accessibility
- Minimum 44x44px for all interactive elements (WCAG AAA)
- Responsive images with lazy-loading

## 6. Design System Notes for Stitch Generation

### Language to Use
- **Atmosphere:** "Sophisticated minimalist sanctuary with gallery-like spaciousness"
- **Button Shapes:** "Subtly rounded corners" (not "rounded-md" or "8px")
- **Shadows:** "Whisper-soft diffused shadows on hover" (not "shadow-sm")
- **Spacing:** "Generous breathing room" and "expansive whitespace"

### Color References
- Primary CTA: "Deep Muted Teal-Navy (#294056)"
- Backgrounds: "Warm Barely-There Cream (#FCFAFA)" or "Crisp Very Light Gray (#F5F5F5)"
- Text: "Charcoal Near-Black (#2C2C2C)" or "Soft Warm Gray (#6B6B6B)"

### Component Prompts
- "Create a product card with gently rounded corners, full-bleed square image, and whisper-soft shadow on hover"
- "Design a primary CTA button in Deep Muted Teal-Navy (#294056) with subtle rounded corners"

### Incremental Iteration
1. Focus on ONE component at a time
2. Be specific about what to change
3. Reference design system language consistently
```

---

## 9. Best Practices

### Writing DESIGN.md

1. **Start with colors and typography** -- these have the highest visual impact on consistency
2. **Add component guidelines** for your most frequently used elements (buttons, inputs, cards)
3. **Spacing and layout rules** can follow
4. **Be specific about intent, not just accurate** -- explain rationale to help model judgment
5. **Use clean formatting** for model parsing
6. **Cover edge cases explicitly** in Do's and Don'ts section

### Maintaining DESIGN.md

1. **Version it in repositories** with meaningful commit messages
2. **Commit to version control** alongside your code
3. **Mention the design system** in major component prompts
4. **Run periodic consistency audits** to identify drift
5. **Update and re-export** when the design evolves

### Common Mistakes to Avoid

- Using vague descriptions instead of specific hex values
- Forgetting to explain functional purposes for each token
- Using technical CSS terminology instead of natural language
- Not including Do's and Don'ts section
- Making DESIGN.md too complex -- focus on what matters most

---

## 10. Related Resources

### Official Documentation
- [Stitch - Design with AI](https://stitch.withgoogle.com/)
- [DESIGN.md Overview](https://stitch.withgoogle.com/docs/design-md/overview/)
- [DESIGN.md Format](https://stitch.withgoogle.com/docs/design-md/format/)
- [DESIGN.md Usage](https://stitch.withgoogle.com/docs/design-md/usage/)
- [MCP Guide](https://stitch.withgoogle.com/docs/mcp/guide/)
- [MCP Setup](https://stitch.withgoogle.com/docs/mcp/setup/)
- [Prompting Guide](https://stitch.withgoogle.com/docs/learn/prompting/)

### GitHub
- [google-labs-code/stitch-skills](https://github.com/google-labs-code/stitch-skills/tree/main/skills/design-md) -- Official design-md skill
- [VoltAgent/awesome-design-md](https://github.com/VoltAgent/awesome-design-md) -- Collection of 66+ DESIGN.md files for popular brands

### Articles & Guides
- [What Is Google Stitch's Design.md File?](https://www.mindstudio.ai/blog/what-is-google-stitch-design-md-file) -- MindStudio
- [How to Use Design.md with Claude Code](https://www.mindstudio.ai/blog/google-stitch-design-md-claude-code-consistent-ui) -- MindStudio
- [What Is the Design.md File in Google Stitch?](https://www.mindstudio.ai/blog/what-is-design-md-google-stitch) -- MindStudio
- [What Is Google Stitch's DESIGN.md?](https://www.designwhine.com/what-the-hell-is-google-stitchs-design-md/) -- DesignWhine
- [Design UI with Stitch from Google Labs](https://blog.google/innovation-and-ai/models-and-research/google-labs/stitch-ai-ui-design/) -- Google Blog
- [Design-to-Code with Antigravity and Stitch MCP](https://codelabs.developers.google.com/design-to-code-with-antigravity-stitch) -- Google Codelabs

### Community
- [awesome-design-md-jp](https://github.com/kzhrknt/awesome-design-md-jp) -- Japanese DESIGN.md collection with CJK typography support
