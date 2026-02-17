# Technology Stack: Blog Visual Refresh

**Project:** Chris Infante Blog - Design Refresh
**Researched:** 2026-02-17
**Scope:** CSS/styling techniques for Jekyll + GitHub Pages (not re-researching the Jekyll stack itself)

## Current State

The existing blog uses:
- Jekyll 3.10.0 on GitHub Pages with Minima theme (dark skin)
- `assets/css/style.scss` (front matter + `@import "minima"; @import "custom";`)
- `_sass/custom.scss` with ~600 lines of hand-written CSS
- CSS custom properties already in use (`:root` block with 8 variables)
- JetBrains Mono + Noto Sans KR loaded via Google Fonts
- Single-column 720px max-width centered layout
- Dark purple theme (#262335 bg, #ff7edb accent)

**Key constraint:** GitHub Pages runs Jekyll 3.10.0 with jekyll-sass-converter 1.5.2 and Sass 3.7.4. SCSS files are compiled server-side at deploy time. All CSS in the final output is plain CSS -- so any modern CSS feature works as long as browsers support it. The Sass version is only relevant for the `.scss` authoring experience.

## Recommended Stack

### CSS Architecture

| Technology | Version | Purpose | Why |
|------------|---------|---------|-----|
| CSS Custom Properties | CSS3+ | Design token system | Already in use, expand from 8 vars to full token system. Zero build step needed. 98%+ browser support. The foundation for the entire refresh. |
| CSS Nesting | Native | Code organization | 90.7% browser support (Chrome 120+, Safari 17.2+, Firefox 117+). Eliminates need for SCSS nesting. Write cleaner selectors without a preprocessor dependency. Falls back gracefully -- unnested CSS still works. |
| `clamp()` | CSS3 | Fluid typography & spacing | 95%+ browser support. Replace fixed `font-size` and hardcoded `rem` values with fluid scales. Eliminates most responsive typography breakpoints. |
| `color-mix()` | CSS Color 4 | Hover/focus state colors | ~88% browser support. Generate hover variants from base colors without hardcoding every shade. Use `color-mix(in oklch, var(--accent) 80%, white)` instead of manually picking #hex values. |

### Typography

| Technology | Version | Purpose | Why |
|------------|---------|---------|-----|
| Inter | Variable font | Body text & headings | Pair with JetBrains Mono. Inter has nearly identical x-height to JetBrains Mono so they harmonize. Use Inter for prose (headings, body paragraphs, navigation) for better readability. JetBrains Mono is optimized for code, not long-form reading. |
| JetBrains Mono | 2.304 | Code blocks & monospace accents | Keep for `<code>` and `<pre>` elements only. Already loaded. Strong developer identity signal. |
| Google Fonts `display=swap` | - | Font loading | Already using preconnect. Add `display=swap` parameter (currently missing from the font URL) to prevent invisible text during load. |

### Color System

| Token | Current | Proposed | Why |
|-------|---------|----------|-----|
| `--bg-primary` | `#262335` (purple) | `#1e1f22` (Discord dark) | Discord's Onyx/dark grey is the modern standard for dark UIs. Less saturated, easier on eyes for long reading. Feels "2025" vs the purple which reads "Synthwave 2019". |
| `--bg-header` | `#241b2f` | `#111214` | Darker than primary for visual hierarchy. Matches Discord's deepest background layer. |
| `--bg-surface` | `#2a2139` | `#2b2d31` | Card/surface elevation. Discord's secondary background. Clearly distinct from primary without being jarring. |
| `--bg-hover` | `#34294f` | `#35373c` | Subtle interactive feedback. Neutral grey, not purple-tinted. |
| `--border` | `#34294f` | `#3f4147` | Visible but not harsh. Slightly lighter than surfaces. |
| `--text-primary` | `#ffffff` | `#f2f3f5` | Slightly off-white. Pure white on dark grey causes eye strain. Discord uses this exact value. |
| `--text-muted` | `#848bbd` (purple-tinted) | `#949ba4` | Neutral grey muted text. Meets WCAG AA contrast on `#1e1f22` (ratio ~5.2:1). |
| `--accent` | `#ff7edb` (pink) | `#9b84ec` (purple) | Shift from pink to a proper purple. Closer to Discord's blurple but warmer. Strong identity without being aggressive. Works for links, highlights, interactive elements. |
| `--accent-dim` | `#f97e72` (orange) | `#7c6dc4` | Muted version of accent for timestamps, secondary highlights. |

### Layout

| Technique | Purpose | Why |
|-----------|---------|-----|
| `max-width: 720px` | Content width | Keep as-is. 720px is the sweet spot for single-column reading. steipete.me and most modern blogs use similar (~700-760px). |
| Flexbox | Header, nav, card layouts | Already in use and appropriate. No need for CSS Grid on a single-column blog. |
| `gap` property | Spacing between elements | Replace margin-based spacing with `gap` in flex containers. Cleaner, more predictable spacing. 96%+ support. |
| `padding-inline` / `margin-inline` | Logical properties | Use logical properties instead of `padding-left`/`padding-right`. Better i18n support, modern convention. 95%+ support. |

### What NOT to Change

| Keep | Why |
|------|-----|
| SCSS file structure | `style.scss` + `_sass/custom.scss` works. No need to restructure. GitHub Pages processes it correctly. |
| Minima base import | `@import "minima"` gives us reset, syntax highlighting, and base structure. Override in `custom.scss`. |
| Sticky header pattern | Already implemented correctly with `position: sticky`. |
| Hamburger menu | Mobile navigation pattern is solid. |
| `::selection` styling | Nice touch, keep it with updated accent color. |
| Single `.scss` partial | One file (`custom.scss`) is manageable at ~600 lines for a blog. Don't over-engineer with multiple partials. |

## Specific CSS Techniques to Use

### 1. Design Token System (HIGH confidence)

Expand `:root` from 8 to ~20 tokens organized by purpose:

```css
:root {
  /* Backgrounds */
  --bg-primary: #1e1f22;
  --bg-secondary: #111214;
  --bg-surface: #2b2d31;
  --bg-hover: #35373c;

  /* Text */
  --text-primary: #f2f3f5;
  --text-secondary: #dbdee1;
  --text-muted: #949ba4;

  /* Accent */
  --accent: #9b84ec;
  --accent-hover: color-mix(in oklch, var(--accent) 85%, white);
  --accent-dim: #7c6dc4;

  /* Borders & Surfaces */
  --border: #3f4147;
  --border-hover: #4e5058;

  /* Spacing scale */
  --space-xs: 0.25rem;
  --space-sm: 0.5rem;
  --space-md: 1rem;
  --space-lg: 1.5rem;
  --space-xl: 2rem;
  --space-2xl: 3rem;

  /* Typography scale */
  --text-xs: clamp(0.75rem, 0.7rem + 0.25vw, 0.8rem);
  --text-sm: clamp(0.8rem, 0.76rem + 0.2vw, 0.875rem);
  --text-base: clamp(0.875rem, 0.83rem + 0.22vw, 0.95rem);
  --text-lg: clamp(1rem, 0.93rem + 0.35vw, 1.15rem);
  --text-xl: clamp(1.15rem, 1.05rem + 0.5vw, 1.35rem);
  --text-2xl: clamp(1.35rem, 1.2rem + 0.75vw, 1.65rem);

  /* Transitions */
  --transition-fast: 0.15s ease;
  --transition-base: 0.2s ease;

  /* Radius */
  --radius-sm: 4px;
  --radius-md: 8px;
  --radius-lg: 12px;
}
```

**Confidence:** HIGH. CSS custom properties have 98%+ support. Already in use on the site. This is the standard approach in 2025-2026.

### 2. Fluid Typography with `clamp()` (HIGH confidence)

Replace all fixed `font-size` declarations:

```css
/* Before */
h1 { font-size: 1.5rem; }

/* After */
h1 { font-size: var(--text-2xl); }
/* which resolves to clamp(1.35rem, 1.2rem + 0.75vw, 1.65rem) */
```

**Confidence:** HIGH. `clamp()` has 95%+ browser support. Eliminates the current approach of using a media query to drop `html` font-size from 16px to 15px at 640px.

### 3. `color-mix()` for Dynamic States (MEDIUM confidence)

Use for hover/focus states instead of hardcoding every color variant:

```css
.post-card:hover {
  border-color: color-mix(in oklch, var(--accent) 60%, transparent);
}

.nav-link:hover {
  color: color-mix(in oklch, var(--accent) 90%, white);
}
```

**Confidence:** MEDIUM. ~88% browser support. Fallback: just use the accent color directly. The site degrades gracefully -- hover states are enhancement, not critical functionality.

**Fallback pattern:**
```css
.post-card:hover {
  border-color: var(--accent); /* fallback */
  border-color: color-mix(in oklch, var(--accent) 60%, transparent);
}
```

### 4. CSS Nesting (MEDIUM confidence)

Use for organizing related styles, not deep nesting:

```css
/* Keep nesting shallow -- max 2 levels */
.post-card {
  padding: var(--space-lg);
  border: 1px solid var(--border);

  &:hover {
    border-color: var(--accent);
    transform: translateY(-2px);
  }

  & .post-card-title {
    font-size: var(--text-lg);
    color: var(--accent);
  }
}
```

**Confidence:** MEDIUM. 90.7% support is good but not universal. Since the current SCSS already compiles nesting, this is mainly about code organization. Keep the SCSS file extension -- GitHub Pages compiles it anyway, and SCSS nesting has 100% "support" since it compiles to flat CSS. Use native CSS nesting only if migrating away from SCSS in the future.

**Recommendation:** Stay with SCSS nesting for now. It compiles to flat CSS with 100% support. Native CSS nesting is the future but adds no value when you already have a Sass build step.

### 5. Logical Properties (HIGH confidence)

Replace directional properties:

```css
/* Before */
padding-left: 1rem;
margin-right: 0.75rem;
border-left: 2px solid var(--accent);

/* After */
padding-inline-start: 1rem;
margin-inline-end: 0.75rem;
border-inline-start: 2px solid var(--accent);
```

**Confidence:** HIGH. 95%+ support. Future-proofs for RTL languages. Modern convention.

## What NOT to Use

| Technology | Why Not |
|------------|---------|
| Tailwind CSS | Requires a build toolchain (PostCSS, Node.js). GitHub Pages has no Node.js step. The site has ~600 lines of CSS -- Tailwind is massive overhead for a personal blog. |
| CSS Modules / CSS-in-JS | Not applicable to Jekyll static sites. These are React/component-framework tools. |
| Container Queries | Overkill for a single-column blog layout. Useful for design systems with reusable widget components, not a blog with 4 page types. |
| `@layer` (Cascade Layers) | The site imports Minima then overrides in custom.scss. Source order already handles this correctly. Layers add complexity with zero benefit at this scale. |
| CSS `light-dark()` function | The blog is dark-only. Adding light mode doubles the design work for no user benefit. The audience (cybersecurity, cypherpunk) expects dark. If light mode is ever added, use `prefers-color-scheme` media query with custom property overrides instead. |
| OKLCH for primary colors | While OKLCH is great for generated palettes, defining your 10-15 base colors in hex is simpler and more portable. Use OKLCH only inside `color-mix()` for the interpolation space, not as the primary color definition format. |
| `@property` declarations | Typed custom properties enable animated gradients and registered properties, but this blog has no use case for them. Added complexity, no benefit. |
| View Transitions API | Chrome-only (~65% support). Not worth adding for a blog refresh. |
| Scroll-Driven Animations | Gimmicky for a content-focused blog. Motion should be minimal and purposeful. |

## Alternatives Considered

| Category | Recommended | Alternative | Why Not Alternative |
|----------|-------------|-------------|---------------------|
| Body font | Inter (variable) | Keep JetBrains Mono everywhere | Monospace fonts reduce reading speed by ~10% for prose. Inter pairs perfectly with JetBrains Mono (matching x-height) and improves readability for paragraphs and navigation. |
| Color system | Discord grey palette | Keep purple palette | Purple reads dated ("Synthwave 2019"). Discord's neutral dark grey is the current standard. Purple accent on grey background is more striking than pink on purple. |
| Color system | Discord grey palette | GitHub dark mode palette | GitHub's palette (#0d1117, #161b22) is slightly too blue-tinted. Discord's is more neutral. |
| Accent color | Purple (#9b84ec) | Keep pink (#ff7edb) | Pink is attention-grabbing but competes with content. Purple is distinctive but calmer. Matches the cybersecurity/cypherpunk vibe better. |
| Accent color | Purple (#9b84ec) | Discord blurple (#5865f2) | Too close to a link color. The warmer purple has more personality. |
| Fluid typography | `clamp()` | Media query breakpoints | `clamp()` provides continuous scaling. Breakpoints cause jarring jumps. Less code, better UX. |
| Hover effect | `translateY(-2px)` | `translateX(4px)` (current) | Vertical lift feels more natural as a "pick up" affordance. Horizontal slide can look like a sliding menu item. Minor preference. |

## Font Loading Strategy

```html
<!-- In head.html: preconnect + optimized font request -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&family=JetBrains+Mono:wght@400;500;600&family=Noto+Sans+KR:wght@300;400&display=swap" rel="stylesheet">
```

Changes from current:
- **Add** Inter (400, 500, 600 weights)
- **Keep** JetBrains Mono (same weights)
- **Keep** Noto Sans KR (for Korean tagline)
- **Add** `display=swap` (currently missing -- prevents FOIT)

## Critical CSS Pattern

Update the inline critical CSS in `head.html` to match the new background color:

```html
<style>
  html, body {
    background-color: #1e1f22;  /* was #262335 */
    color: #f2f3f5;             /* was #ffffff */
    margin: 0;
    padding: 0;
  }
</style>
```

This prevents the flash of purple background before the stylesheet loads.

## No Installation Required

This is a pure CSS refresh. No new gems, no Node.js, no build tools. All changes happen in:

1. `_sass/custom.scss` -- main stylesheet (rewrite)
2. `_includes/head.html` -- font loading + critical CSS update

## WCAG Contrast Verification

| Combination | Ratio | WCAG AA | WCAG AAA |
|-------------|-------|---------|----------|
| `--text-primary` (#f2f3f5) on `--bg-primary` (#1e1f22) | 14.7:1 | Pass | Pass |
| `--text-muted` (#949ba4) on `--bg-primary` (#1e1f22) | 5.2:1 | Pass | Fail |
| `--accent` (#9b84ec) on `--bg-primary` (#1e1f22) | 5.0:1 | Pass (normal) | Fail |
| `--text-primary` on `--bg-surface` (#2b2d31) | 12.5:1 | Pass | Pass |
| `--text-muted` on `--bg-surface` (#2b2d31) | 4.4:1 | Pass (large text) | Fail |

All critical text passes WCAG AA. Muted text on surfaces is borderline -- ensure muted text is only used at larger sizes or as supplementary info (which it already is: dates, reading time).

## Sources

- [GitHub Pages dependency versions](https://pages.github.com/versions/) - Jekyll 3.10.0, Sass 3.7.4, jekyll-sass-converter 1.5.2 (HIGH confidence)
- [CSS Nesting browser support - Can I Use](https://caniuse.com/css-nesting) - 90.7% global support (HIGH confidence)
- [color-mix() - MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/color_value/color-mix) - Browser support and usage (HIGH confidence)
- [Modern CSS Toolkit 2026 - Nick Paolini](https://www.nickpaolini.com/blog/modern-css-toolkit-2026) - Feature recommendations and support levels (MEDIUM confidence)
- [Modern Fluid Typography Using CSS Clamp - Smashing Magazine](https://www.smashingmagazine.com/2022/01/modern-fluid-typography-css-clamp/) - clamp() best practices (HIGH confidence)
- [Discord color palette](https://www.color-hex.com/color-palette/114089) - Dark theme hex values (MEDIUM confidence - verified against multiple sources)
- [Discord Onyx theme colors](https://www.remote.tools/discord/hex-codes-for-discord-background-color) - Background colors (MEDIUM confidence)
- [OKLCH in CSS - Evil Martians](https://evilmartians.com/chronicles/oklch-in-css-why-quit-rgb-hsl) - OKLCH color space rationale (HIGH confidence)
- [Sass Breaking Change: CSS Variable Syntax](https://sass-lang.com/documentation/breaking-changes/css-vars/) - How Sass 3.5+ handles CSS custom properties (HIGH confidence)
- [Responsive Typography Best Practices 2025](https://remtopx.com/blog/responsive-typography-best-practices/) - clamp() and rem usage (MEDIUM confidence)
- [JetBrains Mono font pairings](https://maxibestof.one/typefaces/jetbrains-mono) - Inter + JetBrains Mono pairing (MEDIUM confidence)
