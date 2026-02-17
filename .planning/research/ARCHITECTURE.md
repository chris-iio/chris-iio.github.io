# Architecture Patterns

**Domain:** Jekyll blog CSS refresh (design system update for existing site)
**Researched:** 2026-02-17

## Current Architecture Assessment

The blog currently has a clean but monolithic CSS architecture:

```
assets/css/style.scss          # Entry point (imports minima + custom)
  @import "minima"             # Base theme (from gem, dark skin)
  @import "custom"             # All custom styles in one file
_sass/custom.scss              # 607 lines, every style in one file
_includes/head.html            # Critical CSS inline + stylesheet link
```

**What works well:**
- CSS custom properties (`:root` variables) already in use for theming
- Clear section comments delineating component areas
- Single file means zero import-order confusion
- Sticky header, responsive hamburger menu, card-based post list all functional

**What needs changing:**
- All 8 CSS variables are the old purple palette -- these drive everything
- Background color `#262335` (purple-black) shifting to `~#36393f` (Discord-style dark grey)
- Accent `#ff7edb` (hot pink) may shift or soften
- The `_includes/head.html` has hardcoded `#262335` in critical CSS that must stay in sync
- Some `!important` overrides fighting the Minima base theme

## Recommended Architecture

**Keep the single-file approach.** Do not split into a multi-file SCSS architecture. Here is why:

1. The site has ~600 lines of CSS. Multi-file (7-1 or 5-1 pattern) is designed for 2000+ line codebases. Splitting a 600-line file into 8 partials adds navigational overhead with zero organizational benefit.
2. The existing section-comment structure (`/* === HEADER === */`, etc.) already provides the same logical grouping that partials would.
3. Jekyll's `_sass` import system works but the developer experience of jumping between files for a small site is worse than scrolling one well-organized file.
4. The `@import "minima"` + `@import "custom"` pattern already cleanly separates base theme from customization.

**The architecture change is about the content of the CSS, not its file structure.**

### Component Boundaries

| Component | Responsibility | Lines (approx) | Communicates With |
|-----------|---------------|-----------------|-------------------|
| **Design Tokens** (`:root` variables) | Color palette, spacing constants | ~10 | Every other component reads these |
| **Critical CSS** (`head.html` inline) | Prevent FOUC (flash of unstyled content) | ~6 | Must mirror `:root` bg/text/font values |
| **Reset + Base** | Box model, html/body, typography defaults | ~35 | Sets foundation for all components |
| **Layout** (`.wrapper`, `.page-content`) | Max-width container, page padding | ~10 | Wraps all page content |
| **Header** (`.site-header`, `.site-nav`) | Sticky nav, title, tagline, hamburger menu | ~80 | Reads bg-header, border, accent tokens |
| **Hero** (`.hero`, `.hero-*`) | Hero section (currently unused on homepage) | ~20 | Reads accent, text-muted tokens |
| **Post List** (`.post-card`, `.posts-section`) | Homepage card grid | ~50 | Reads bg-surface, bg-hover, accent tokens |
| **Post Content** (`.post`, `.post-content`) | Individual article styling | ~70 | Reads all tokens; most complex component |
| **About Page** (`.about-content`, `.skills-grid`) | About page, skills tags, cert list | ~60 | Reads bg-surface, border, accent tokens |
| **Responsive** (`@media`) | Mobile breakpoint overrides | ~40 | Overrides header, cards, hero, layout |
| **Utilities** (`::selection`, scrollbar) | Selection color, scrollbar styling | ~20 | Reads accent, bg-primary tokens |

### Data Flow

```
:root variables (design tokens)
    |
    v
Minima base theme (provides defaults, dark skin)
    |
    v
custom.scss overrides (replaces most Minima styles)
    |
    v
Component sections (each reads tokens via var(--name))
    |
    v
@media responsive overrides (modifies component values at breakpoints)
```

**The critical insight:** CSS custom properties cascade. Changing a value in `:root` propagates instantly to every component that references it. This is why the color refresh is architecturally simple -- the variable system is already in place.

## Change Dependency Graph

This is the order changes MUST happen in, based on dependencies:

```
Phase 1: Design Tokens (no dependencies)
    :root { --bg-primary, --bg-header, --bg-surface, etc. }
    |
    +---> head.html critical CSS (must match --bg-primary and --text-primary)
    |
Phase 2: Base Typography + Links (depends on tokens)
    body, h1-h6, a styles
    |
Phase 3: Header Component (depends on tokens + base)
    .site-header, .site-title, .site-tagline, .nav-*
    |
Phase 4: Layout + Spacing (depends on tokens)
    .wrapper max-width, .page-content padding
    |
Phase 5: Content Components (depends on all above)
    .post-card, .post-content, .about-content
    |
Phase 6: Responsive + Utilities (depends on all above)
    @media overrides, ::selection, scrollbar
```

### Why This Order

1. **Tokens first** because every component reads them. Change tokens and 80% of the color refresh happens automatically.
2. **Critical CSS in sync** because `head.html` has hardcoded `background-color: #262335` and `color: #ffffff` that prevent FOUC. If tokens change but critical CSS does not, users see a flash of the wrong color on page load.
3. **Base typography** because heading sizes, link colors, and body font-size affect how every component looks.
4. **Header** before content because the header is always visible (sticky). Visual review starts here.
5. **Layout/spacing** because the reference site (steipete.me) uses generous whitespace -- adjusting wrapper width and padding affects all content.
6. **Content components** last because they are the bulk of the work and benefit from all prior decisions being settled.
7. **Responsive** truly last because mobile overrides must match the desktop styles they modify.

## Patterns to Follow

### Pattern 1: Token-Driven Theming
**What:** Define all colors, spacing, and sizing as CSS custom properties in `:root`. Components reference tokens, never hardcoded values.
**When:** Always. This is already partially in place.
**Why it matters for the refresh:** Changing 8 variable values updates the entire site. No hunting through 600 lines for hex codes.

Current tokens:
```scss
:root {
    --bg-primary: #262335;      // -> change to ~#36393f (Discord grey)
    --bg-header: #241b2f;       // -> change to slightly darker grey
    --bg-surface: #2a2139;      // -> change to slightly lighter grey
    --bg-hover: #34294f;        // -> change to hover-appropriate grey
    --border: #34294f;          // -> change to subtle grey border
    --text-primary: #ffffff;    // -> may soften to #e5e5e5 or keep
    --text-muted: #848bbd;      // -> adjust for grey palette contrast
    --accent: #ff7edb;          // -> keep purple or adjust
    --accent-dim: #f97e72;      // -> evaluate if still needed
}
```

Recommended new tokens (add semantic layer):
```scss
:root {
    /* Primitive palette */
    --bg-primary: #36393f;
    --bg-secondary: #2f3136;
    --bg-surface: #40444b;
    --bg-hover: #4f545c;
    --border: #4f545c;
    --text-primary: #dcddde;
    --text-secondary: #b9bbbe;
    --text-muted: #72767d;
    --accent: #9b84c9;          /* softer purple, evaluate */
    --accent-hover: #b9a3e3;

    /* Semantic aliases (optional, add if helpful) */
    --link-color: var(--text-primary);
    --link-hover: var(--accent);
    --card-bg: var(--bg-surface);
    --card-border: var(--border);
    --card-hover-bg: var(--bg-hover);
}
```

**Example -- component using tokens:**
```scss
.post-card {
    background-color: var(--card-bg);
    border: 1px solid var(--card-border);
}

.post-card:hover {
    background-color: var(--card-hover-bg);
    border-color: var(--accent);
}
```

### Pattern 2: Critical CSS Synchronization
**What:** The inline `<style>` block in `head.html` must always match the CSS variable values for `--bg-primary`, `--text-primary`, and font-family.
**When:** Every time you change design tokens.

```html
<!-- _includes/head.html -->
<style>
  html, body {
    background-color: #36393f;  /* MUST match --bg-primary */
    color: #dcddde;             /* MUST match --text-primary */
    margin: 0;
    padding: 0;
    font-family: 'JetBrains Mono', monospace;
  }
</style>
```

**Why:** Without this, the browser shows white (or the wrong color) for ~100-300ms while the external stylesheet loads. On slow connections or large pages, this is jarring.

### Pattern 3: Eliminate !important Overrides
**What:** The current CSS uses `!important` on link colors to fight Minima's defaults. Replace with higher specificity selectors instead.
**When:** During the base typography phase.

**Current (problematic):**
```scss
a, a:visited, a:active {
    color: var(--text-primary) !important;
}
```

**Better:**
```scss
body a,
body a:visited,
body a:active {
    color: var(--text-primary);
    text-decoration: none;
}
```

Adding `body` as a parent selector increases specificity enough to override Minima without `!important`. This makes future overrides (like accent-colored links in specific contexts) possible without an `!important` escalation war.

### Pattern 4: Spacing System
**What:** Use consistent spacing values rather than arbitrary pixel/rem amounts scattered throughout.
**When:** During the layout/spacing phase.

The reference site (steipete.me) uses generous whitespace. Rather than adjusting padding values ad hoc, define a spacing scale:

```scss
:root {
    --space-xs: 0.25rem;   /* 4px */
    --space-sm: 0.5rem;    /* 8px */
    --space-md: 1rem;       /* 16px */
    --space-lg: 1.5rem;     /* 24px */
    --space-xl: 2rem;       /* 32px */
    --space-2xl: 3rem;      /* 48px */
    --space-3xl: 4rem;      /* 64px */
}
```

This is optional but recommended. It makes "tighten" or "loosen" spacing changes trivial -- adjust the scale, not 30 individual values.

## Anti-Patterns to Avoid

### Anti-Pattern 1: Splitting Into Many SCSS Partials
**What:** Breaking `custom.scss` into `_variables.scss`, `_header.scss`, `_post-card.scss`, `_responsive.scss`, etc.
**Why bad:** For a ~600-line file, this adds indirection without reducing complexity. You end up with 8 files of 50-80 lines each, and finding "where is the post card hover color defined?" requires checking multiple files instead of using Ctrl+F in one.
**Instead:** Keep the single file with clear section comments. If the CSS grows past 1000+ lines, revisit this decision.

### Anti-Pattern 2: Changing Colors Without Checking Contrast
**What:** Picking new grey values that look good in isolation but fail WCAG contrast requirements.
**Why bad:** Dark grey background + grey text can easily fall below 4.5:1 contrast ratio. The current purple theme has this risk too.
**Instead:** Before finalizing token values, check text-on-background contrast using a tool like WebAIM's contrast checker. Key pairs to verify:
- `--text-primary` on `--bg-primary` (must be >= 4.5:1)
- `--text-muted` on `--bg-primary` (should be >= 4.5:1, at minimum 3:1)
- `--text-muted` on `--bg-surface` (card text readability)
- `--accent` on `--bg-primary` (link visibility)

### Anti-Pattern 3: Changing Minima Import Strategy
**What:** Removing `@import "minima"` or trying to replace it with individual Minima partials.
**Why bad:** The `github-pages` gem pins a specific Minima version. The import provides base styles that the custom.scss overrides. Removing it would require reimplementing things like syntax highlighting and RSS feed links that Minima handles.
**Instead:** Keep `@import "minima"` as the base layer. Override in `custom.scss`. The `!important` cleanup (Pattern 3) reduces friction with the base theme.

### Anti-Pattern 4: Testing Only on Desktop
**What:** Making all changes and only checking the responsive view at the end.
**Why bad:** The hamburger menu, card padding, and spacing all have mobile-specific overrides. A change to desktop card styling might not propagate correctly to mobile.
**Instead:** Test at both desktop (>640px) and mobile (<640px) breakpoints after each component change. Use `bundle exec jekyll serve` and browser DevTools responsive mode.

## Testing Strategy During Development

### Local Development Loop

```bash
# Terminal 1: Run Jekyll with live reload
bundle exec jekyll serve --livereload

# Browser: Open http://localhost:4000
# Browser DevTools: Toggle responsive mode for mobile testing
```

Jekyll's `--livereload` flag automatically refreshes the browser when SCSS files change. This gives near-instant feedback on color and spacing changes.

### Visual Verification Checklist

After each component change, verify these pages:
1. **Homepage** (`/`) -- post cards, section title, overall background
2. **Blog post** (`/blog/2026/01/10/getting-started/`) -- post content, code blocks, blockquotes, headings
3. **About page** (`/about/`) -- skills tags, cert list, text hierarchy
4. **Mobile view** (DevTools at 375px width) -- hamburger menu, card stacking, text sizing

### Contrast Verification

Use browser DevTools accessibility panel or:
```
# Quick contrast check (manual)
# bg-primary #36393f with text-primary #dcddde = ratio ~8.5:1 (passes AA and AAA)
# bg-primary #36393f with text-muted #72767d = ratio ~2.7:1 (fails -- needs adjustment)
```

The text-muted value will need careful tuning. Discord uses `#b9bbbe` for secondary text which has better contrast on dark grey.

## Build Order Implications

### What Jekyll Actually Builds

```
Source                        Build Output
------                        ------------
assets/css/style.scss    -->  _site/assets/css/style.css
_sass/custom.scss        -->  (bundled into style.css via @import)
_includes/head.html      -->  (inlined into every page's <head>)
_layouts/*.html          -->  (templates, no CSS changes needed)
```

**Key implication:** There is exactly one CSS file output. Changes to `_sass/custom.scss` and `_includes/head.html` are the only files that need editing for the visual refresh. No layout or template changes required unless the HTML structure itself changes (it should not for a color/spacing refresh).

### What Can Be Changed Independently

| Change | Affects | Safe to do alone? |
|--------|---------|-------------------|
| `:root` token values | Everything that uses `var()` | YES, but sync head.html |
| `head.html` critical CSS | FOUC prevention only | YES, must match tokens |
| Header styles | Header only | YES |
| Post card styles | Homepage only | YES |
| Post content styles | Article pages only | YES |
| About page styles | About page only | YES |
| Responsive overrides | Mobile only | YES, but test desktop too |
| `.wrapper` max-width | All pages | YES, but check all pages |

This means changes can be made and verified incrementally. There is no "big bang" deployment required.

## Confidence Assessment

| Aspect | Confidence | Rationale |
|--------|------------|-----------|
| Single-file approach | HIGH | Verified codebase is 607 lines; confirmed Jekyll `_sass` behavior via official docs |
| Token-driven theming | HIGH | CSS custom properties already in use; standard pattern verified across multiple sources |
| Critical CSS sync requirement | HIGH | Directly observed in `head.html` with hardcoded values |
| Change ordering (tokens first) | HIGH | Follows from CSS cascade rules and direct codebase analysis |
| `!important` removal via specificity | MEDIUM | Standard CSS technique; needs testing against specific Minima version output |
| Spacing system tokens | MEDIUM | Good practice but optional; adds tokens the codebase does not currently use |
| Contrast values for Discord palette | LOW | Suggested hex values need empirical verification with contrast tools |

## Sources

- [Jekyll Sass/SCSS Configuration](https://jekyllrb.com/docs/configuration/sass/) -- official Jekyll docs on `_sass` directory handling
- [Jekyll Step-by-Step: Assets](https://jekyllrb.com/docs/step-by-step/07-assets/) -- official docs on SCSS processing pipeline
- [Simple SCSS Architecture Playbook](https://matthewelsom.com/blog/simple-scss-playbook.html) -- 5-1/7-1 pattern context (why NOT to use it here)
- [CSS Custom Properties Design Tokens Guide](https://penpot.app/blog/the-developers-guide-to-design-tokens-and-css-variables/) -- token layering approach
- [CSS-Tricks Dark Mode Guide](https://css-tricks.com/a-complete-guide-to-dark-mode-on-the-web/) -- dark theme implementation patterns
- [Minima Theme README](https://github.com/jekyll/minima/blob/master/README.md) -- override strategy for Minima
- Codebase analysis: `_sass/custom.scss`, `_includes/head.html`, `assets/css/style.scss`, all layouts
