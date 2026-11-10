---
name: svg-logo-generator
description: "Generate custom SVG logos with project context scanning, adaptive brainstorming, and full brand kit export (favicons, social images, PWA manifest). For the creative framework (brand strategy, AI prompts, SVG rules, motion, brand systems), see logo-design. Use when asked to generate a logo, create an SVG logo, or build a brand asset kit for an existing project."
version: 1.0.0
license: MIT
author: "the maintainers"
---
# Logo Generator

Generates SVG logos through project context scanning + adaptive brainstorming, then exports a complete brand asset kit.

**This skill handles the execution pipeline. For the creative framework — brand strategy, AI prompt formulas, SVG rules, motion principles, brand systems, and trademark awareness — see the `logo-design` skill and load it first.**

---

## Quick Reference: Creative Framework (from logo-design)

**AI Prompt Formula** — copy-paste into ImageGenerate:

```
Create a [STYLE] logo concept for [BRAND NAME], a [INDUSTRY/CATEGORY] [BRAND TYPE].

CONCEPT & VALUES
The logo should evoke [EMOTION 1] and [EMOTION 2]. [VISUAL CONCEPT DESCRIPTION].

VISUAL ELEMENTS
[DETAILED DESCRIPTION OF SHAPES, SYMBOLISM, COMPOSITION].

STYLE
[STYLE DESCRIPTORS: minimalist, geometric, flat vector-like, premium].

COLOR PALETTE
[COLOR NAME] (#HEX), [COLOR NAME] (#HEX), [COLOR NAME] (#HEX). [RATIONALE].

COMPOSITION
[CENTERED / ICON-LEFT / etc.]. Isolated on [white/dark] background.

TECHNICAL REQUIREMENTS
- Flat 2D vector, crisp edges, single icon, no mockups
- Scalable in a single color, high resolution

AVOID
- no mockup, no 3D render, no realistic shadows
- no complex gradients, no drop shadows
- no tiny details, no thin lines that disappear at small size
- no fake typography, no unreadable letters
- no background texture, no watermark
- no copied brand style, no trademarked symbols
- no stock-icon aesthetics
```

**SVG Rules** — always:
- ViewBox: `0 0 512 512`
- Min stroke: 2px
- No `<image>`, `<feGaussianBlur>`, `<feDropShadow>`, `currentColor`, CSS variables
- All colors: hex only
- One element per semantic part with stable ids (`#mark`, `#wordmark`, `#dot`, etc.)
- Paths that will animate stroke-reveal: add `pathLength="1"`

---

## Phase 1: Context Scan

Automatically scan the current working directory for branding signals. Do NOT ask the user — just read it.

```bash
ls package.json README.md CLAUDE.md tailwind.config.* manifest.json site.webmanifest 2>/dev/null
```

| Source | Extract |
|--------|---------|
| `package.json` / `Cargo.toml` / `pyproject.toml` | Project name, description, keywords |
| `README.md` | Project name, tagline, description |
| `CLAUDE.md` | Project conventions, tech stack |
| `tailwind.config.*` / `theme.config.*` | Brand colors, font choices |
| `*.css` (first 3 files) | CSS custom properties with color values |
| `public/` / `assets/` / `static/` | Existing logos, favicons |
| `.svg` / `.png` in root | Existing logo files |

```bash
# Find existing logos
ls **/*.svg **/favicon* **/logo* 2>/dev/null | head -20

# Extract brand colors
grep -h --color=no -E '(--primary|--brand|--theme|color:|#[0-9A-Fa-f]{6})' \
  $(ls *.css 2>/dev/null | head -3) 2>/dev/null
```

**Build context summary:**
```
name: [found or "unknown"]
description: [found or "none"]
colors_found: [hex codes with source]
existing_logos: [paths]
tech_stack: [detected]
has_branding: [yes/partial/no]
```

---

## Phase 2: Adaptive Brainstorming

| Context state | Action |
|--------------|--------|
| **Rich** (name + colors + description all found) | Skip questions. Present findings, jump to concepts. |
| **Moderate** (name found, missing colors/description) | Ask 2 targeted questions. |
| **Bare** (nothing found) | Full guided session. Max 3 questions total. |

---

## Phase 3: Concept Proposals

**Load `logo-design` first** for the maintainer/the org briefs. Default to **Tier-1 reduction** (Nike/Apple solid mono) — not decorative multi-element concepts. See `logo-design` → Phase 3 “the maintainer default” and `references/premium-mark-design.md`.

Prefer writing SVG immediately and sending Playwright PNGs over long text-only concepts when the user wants visuals.

**“Different designs”:** vary **structure** (pulse, negative circle, arc, hex, stamp, monolith, split…) not stroke weight on the same V. Ship 8–10 PNGs; ask for number picks. See `logo-design` Phase 3 structural table.

Present **2-3 distinct directions** (or 4–6 mono SVGs for premium briefs; 8–10 when he asks for variety):

```
### Concept [N]: [Name]

**Style:** [geometric / organic / typographic / abstract]
**Composition:** [shapes, arrangement, why it works]
**Colors:** [hex codes per element]
**Rationale:** [why this fits the brand specifically]
**Scalability:** [holds up at 16×16?]
**Motion personality:** [if animation needed — see logo-design Phase 7]
```

---

## Phase 4: SVG Generation

After user approves a concept, generate with ImageGenerate using the prompt formula above.

Output 3 variants (write to `/tmp/logo-gen/` first):
1. `logo.svg` — Primary, light background
2. `logo-dark.svg` — Dark background variant
3. `logo-mono.svg` — Single color `#000000`

For geometric/simple logos: write SVG directly (faster, cleaner, no model needed).

---

## Phase 5: Preview

```bash
mkdir -p /tmp/logo-gen/preview
node the asset generator \
  --svg /tmp/logo-gen/logo.svg \
  --svg-dark /tmp/logo-gen/logo-dark.svg \
  --svg-mono /tmp/logo-gen/logo-mono.svg \
  --output /tmp/logo-gen/preview \
  --stage all \
  --brand-name "[name]" \
  --primary-color "[hex]" \
  --bg-color "[hex]"
```

Open: `/tmp/logo-gen/preview/logo-preview.html`

Changes → regenerate affected variant → re-run preview → repeat.

---

## Phase 6: Export

Once approved:

```bash
# Detect: ./public/ > ./assets/ > ./static/ > create ./public/
TARGET="./public/"

node the asset generator \
  --svg /tmp/logo-gen/logo.svg \
  --svg-dark /tmp/logo-gen/logo-dark.svg \
  --svg-mono /tmp/logo-gen/logo-mono.svg \
  --output "$TARGET" \
  --stage all \
  --brand-name "[name]" \
  --primary-color "[hex]" \
  --bg-color "[hex]"
```

**Exported:**
```
[target_dir]/
├── logo.svg, logo-dark.svg, logo-mono.svg
├── favicon.ico, favicon-16x16.png … favicon-512x512.png
├── apple-touch-icon.png
├── og-image.png (1200×630), twitter-card.png (1200×600)
├── site.webmanifest
└── logo-preview.html
```

---

## Error Handling

| Error | Fix |
|-------|-----|
| `sharp not installed` | `cd scripts && npm install` |
| SVG rendering fails | Validate SVG syntax, check viewBox, retry |
| Output dir not writable | Ask user for alternative path |
| Preview fails (CHROME_BIN) | `export CHROME_BIN="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"` |

---

## Skill Maintenance

- **Learning new logo/design repos**: use `opensrc fetch <pkg>` — NOT `curl raw.githubusercontent.com` (frequently rate-limited, returns empty)
- **Raster→vector + animation**: Pixel2Motion at `the pixel2motion skill`
- **Full creative workflow** (brand strategy, motion, fonts, trademark): load `logo-design` skill
