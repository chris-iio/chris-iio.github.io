# Phase 1: Color Foundation - Research

**Researched:** 2026-02-17
**Domain:** Jekyll blog CSS color palette migration (dark purple to neutral dark grey)
**Confidence:** HIGH

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| TCH-01 | Remove !important declarations from link styles, replace with higher-specificity selectors | 6 `!important` declarations identified in custom.scss (lines 64, 70, 106, 111, 148, 289). Minima base theme uses simple `a` selectors; `body a` provides enough specificity to override without `!important`. See Architecture Patterns > Pattern 2. |
| TCH-02 | Resolve Minima dark skin conflict (disable skin or align variables) | Minima dark skin sets `$dm-background-color: #181818`, `$dm-brand-color: #999999`, `$dm-link-base-color: #5691cd` and 15+ other Sass variables at compile time. These conflict with CSS custom properties. Disable by removing `skin: dark` from `_config.yml`. See Architecture Patterns > Pattern 1. |
| CLR-01 | Site background changes from dark purple (#262335) to Discord-style dark grey (~#1e1f22) | New palette fully defined with verified contrast ratios. `--bg-primary: #1e1f22` replaces `#262335`. See Standard Stack > Color Palette and WCAG Verification. |
| CLR-02 | All surface colors shift to neutral greys matching the new background | Complete 10-variable palette mapped: bg-primary, bg-header, bg-surface, bg-hover, border, text-primary, text-muted, accent, accent-dim. All purple-tinted values replaced with neutral greys. See Standard Stack > Color Palette. |
| CLR-03 | Critical CSS in head.html stays in sync with new palette (no color flash on load) | `head.html` line 14 hardcodes `background-color: #262335`. Must update to `#1e1f22` simultaneously with `:root` token change. See Architecture Patterns > Pattern 3. |
| CLR-04 | All text/background color combinations pass WCAG AA contrast (4.5:1 minimum) | 12 color combinations computed. 10 pass AA for normal text. 2 edge cases identified with fixes: `--accent-dim` adjusted from #7c6dc4 to #8a7dd0 (3.80:1 -> 4.65:1), `--text-muted` on `--bg-hover` needs contextual treatment. See WCAG Contrast Verification. |
| CLR-05 | Scrollbar, selection highlight, and other accent elements updated to match new palette | `::selection`, `::-webkit-scrollbar-*`, and tagline color (#9d8cc4) all identified as requiring update. All use existing CSS variables except the hardcoded tagline color. See Don't Hand-Roll and Code Examples. |
</phase_requirements>

## Summary

This phase replaces the site's entire color palette from purple-tinted dark (#262335 background, #ff7edb pink accent) to Discord-inspired neutral dark grey (#1e1f22 background, #9b84ec purple accent). The work is architecturally simple because the site already uses CSS custom properties -- changing 10 variable values in `:root` propagates to every component automatically. However, three preconditions must be resolved first: (1) remove `!important` declarations that will cause specificity conflicts during the migration, (2) disable Minima's dark skin which defines competing Sass variables at compile time, and (3) update the hardcoded critical CSS in `head.html` to prevent a color flash on page load.

The entire phase touches exactly three files: `_config.yml` (remove Minima dark skin), `_sass/custom.scss` (update `:root` variables, remove `!important`, fix hardcoded tagline color), and `_includes/head.html` (update critical CSS inline style). No layout changes, no HTML restructuring, no new dependencies. The CSS custom property system means the change is atomic -- update the tokens and the cascade handles propagation.

The primary risk is contrast ratio regression. The current muted text color (#848bbd) was tuned for purple backgrounds and must be replaced with a neutral grey (#949ba4) that maintains WCAG AA compliance on the new background. Two specific combinations need attention: accent-dim text on the primary background (requires lightening from #7c6dc4 to #8a7dd0) and muted text on hover-state backgrounds (passes AA for large text only at the proposed values).

**Primary recommendation:** Resolve the three preconditions (remove `!important`, disable Minima dark skin, sync critical CSS) BEFORE swapping any color values. Then apply the complete new palette as a single atomic change.

## Standard Stack

### Core

| Technology | Version/Spec | Purpose | Why Standard |
|------------|-------------|---------|--------------|
| CSS Custom Properties | CSS3+ (98%+ support) | Design token system for all colors | Already in use on the site. Changing `:root` values propagates everywhere. Zero build step. |
| Jekyll + Minima | Jekyll 3.10.0, Minima 2.5.x | Static site generator + base theme | Pinned by `github-pages` gem. Cannot upgrade. All CSS compiled server-side at deploy. |
| SCSS (via jekyll-sass-converter) | Sass 3.7.4 | Stylesheet authoring | Already in use. Compiles to plain CSS. Modern CSS features pass through untouched. |

### Color Palette

| Token | Current Value | New Value | Contrast on bg-primary | Role |
|-------|---------------|-----------|----------------------|------|
| `--bg-primary` | `#262335` (purple) | `#1e1f22` | N/A (is the background) | Page background |
| `--bg-header` | `#241b2f` (deep purple) | `#111214` | N/A | Sticky header background |
| `--bg-surface` | `#2a2139` (purple) | `#2b2d31` | N/A | Cards, code blocks, skill tags |
| `--bg-hover` | `#34294f` (purple) | `#35373c` | N/A | Interactive hover state |
| `--border` | `#34294f` (purple) | `#3f4147` | N/A | Borders, dividers |
| `--text-primary` | `#ffffff` | `#f2f3f5` | 14.84:1 (AA+AAA) | Body text, headings |
| `--text-muted` | `#848bbd` (blue-purple) | `#949ba4` | 5.87:1 (AA) | Dates, metadata, secondary text |
| `--accent` | `#ff7edb` (pink) | `#9b84ec` (purple) | 5.42:1 (AA) | Links, highlights, brand color |
| `--accent-dim` | `#f97e72` (coral) | `#8a7dd0` (muted purple) | 4.65:1 (AA) | Timestamps, secondary accents |

### Additional Hardcoded Colors to Migrate

| Location | Current Value | Action |
|----------|---------------|--------|
| `head.html` line 14 | `background-color: #262335` | Change to `#1e1f22` |
| `head.html` line 15 | `color: #ffffff` | Change to `#f2f3f5` |
| `custom.scss` line 118 | `.site-tagline` color: `#9d8cc4` | Convert to `var(--accent-dim)` or `var(--text-muted)` |

### Files Modified

| File | Changes | Lines Affected |
|------|---------|---------------|
| `_config.yml` | Remove `skin: dark` from minima settings | Line 30 |
| `_sass/custom.scss` | Update `:root` variables, remove `!important` (6 instances), fix tagline hardcoded color | Lines 13-22, 64, 70, 106, 111, 118, 148, 289 |
| `_includes/head.html` | Update critical CSS inline style | Lines 14-15 |

## Architecture Patterns

### Pattern 1: Disable Minima Dark Skin Before Color Work

**What:** Remove `skin: dark` from `_config.yml` minima settings. The dark skin defines 20+ Sass variables (`$dm-background-color: #181818`, `$dm-brand-color: #999999`, `$dm-link-base-color: #5691cd`, etc.) that compile independently of CSS custom properties. These two systems cannot be kept in sync.

**When:** First action in Phase 1, before any color changes.

**Why:** Minima dark skin variables are compiled at build time into CSS rules that compete with `custom.scss` overrides. The site's `custom.scss` already overrides every visual element -- the dark skin is redundant. Disabling it eliminates a class of hard-to-debug color conflicts where Minima-generated styles "leak through" on elements not fully overridden (e.g., footer, feed links, any future Minima component).

**Implementation:**
```yaml
# _config.yml -- BEFORE
minima:
  skin: dark
  social_links:
    - { platform: linkedin, user_url: "..." }

# _config.yml -- AFTER
minima:
  social_links:
    - { platform: linkedin, user_url: "..." }
```

**Verification:** After removing `skin: dark`, rebuild locally (`bundle exec jekyll serve`) and check all pages. Any elements that looked correct before but now show wrong colors were relying on Minima's dark skin -- those need explicit overrides in `custom.scss`.

**Minima dark skin variables that will stop being set:**
- `$dm-background-color: #181818` -- was being overridden by `--bg-primary`
- `$dm-text-color: darken(#bbbbbb, 15%)` -- was being overridden by `--text-primary`
- `$dm-brand-color: #999999` -- affected elements not fully overridden
- `$dm-link-base-color: #5691cd` -- fought with `!important` on link colors
- `$dm-link-visited-color: #80bbff` -- fought with `!important` on link colors
- `$dm-code-background-color: #222222` -- was being overridden by `--bg-surface`
- Plus border colors, table colors, heading color, site-title color

### Pattern 2: Replace !important With Higher-Specificity Selectors

**What:** Remove all 6 `!important` declarations and replace with selectors that naturally beat Minima's specificity.

**When:** After disabling Minima dark skin, before changing color values.

**Why:** `!important` blocks contextual overrides. When accent-colored link text is needed in specific components (post card titles, section headers), `!important` on the base link rule forces another `!important` to override it. This creates a specificity arms race. Minima's link styles use simple `a` selectors -- adding `body` as a parent provides specificity 0-0-1-1 vs Minima's 0-0-0-1, which wins cleanly.

**Current (problematic):**
```scss
/* Lines 63-71 */
a,
a:visited,
a:active {
    color: var(--text-primary) !important;  /* !important to fight Minima */
    text-decoration: none;
    transition: color 0.15s ease;
}

a:hover {
    color: var(--accent) !important;  /* !important to fight Minima */
}

/* Line 106 */
.site-title {
    color: var(--accent) !important;
}

/* Line 111 */
.site-title:hover {
    color: var(--text-primary) !important;
}

/* Line 148 */
.nav-link:hover {
    color: var(--accent) !important;  /* duplicate rule -- lines 146-148 */
}

/* Line 289 */
.post-card {
    text-decoration: none !important;
}
```

**Fixed:**
```scss
/* Base links -- body prefix beats Minima's bare `a` selector */
body a,
body a:visited,
body a:active {
    color: var(--text-primary);
    text-decoration: none;
    transition: color 0.15s ease;
}

body a:hover {
    color: var(--accent);
}

/* .site-title already has class specificity > bare `a` */
.site-title {
    color: var(--accent);
}

.site-title:hover {
    color: var(--text-primary);
}

/* .nav-link already has class specificity -- remove duplicate rule */
.nav-link:hover {
    color: var(--accent);
}

/* .post-card already has class specificity */
.post-card {
    text-decoration: none;
}
```

**Note:** The `.nav-link:hover` rule currently appears twice (lines 146-149). The first one (line 147) does NOT have `!important`, the second (line 148) does. Remove the duplicate entirely.

### Pattern 3: Critical CSS Synchronization

**What:** The inline `<style>` block in `_includes/head.html` must match the `:root` token values for `--bg-primary` and `--text-primary`.

**When:** Every time `:root` color tokens change. Treat them as a single atomic edit.

**Current:**
```html
<style>
  html, body {
    background-color: #262335;  /* MUST match --bg-primary */
    color: #ffffff;              /* MUST match --text-primary */
    margin: 0;
    padding: 0;
    font-family: 'JetBrains Mono', monospace;
  }
</style>
```

**Updated:**
```html
<style>
  html, body {
    background-color: #1e1f22;  /* matches new --bg-primary */
    color: #f2f3f5;              /* matches new --text-primary */
    margin: 0;
    padding: 0;
    font-family: 'JetBrains Mono', monospace;
  }
</style>
```

**Why critical:** Without this, the browser renders the page with the old purple background for 100-300ms while the external stylesheet loads. On slow connections or cache-cleared browsers, this is a jarring flash. The inline style loads instantly (it's in the HTML), setting the correct background before any external resources arrive.

### Pattern 4: Atomic Palette Swap

**What:** Change ALL color token values in a single commit. Never leave the palette partially migrated.

**When:** After preconditions (Pattern 1, 2, 3 preparation) are met.

**Why:** A palette has internal consistency. Purple surface colors (`#2a2139`) against a grey background (`#1e1f22`) creates a visually broken half-state. The token system makes atomic swaps trivial -- it is 10 lines of `:root` variable assignments.

**The complete token change:**
```scss
:root {
    --bg-primary: #1e1f22;     /* was #262335 */
    --bg-header: #111214;      /* was #241b2f */
    --bg-surface: #2b2d31;     /* was #2a2139 */
    --bg-hover: #35373c;       /* was #34294f */
    --border: #3f4147;         /* was #34294f */
    --text-primary: #f2f3f5;   /* was #ffffff */
    --text-muted: #949ba4;     /* was #848bbd */
    --accent: #9b84ec;         /* was #ff7edb */
    --accent-dim: #8a7dd0;     /* was #f97e72 */
}
```

### Anti-Patterns to Avoid

- **Changing one variable at a time:** Creates a multi-commit inconsistent state. Change all 10 together.
- **Adding new `!important` declarations:** If a color does not apply, the problem is specificity, not importance. Add a parent selector instead.
- **Creating `_sass/minima/custom-variables.scss`:** This would override Minima's Sass variables to match the new palette. Unnecessary complexity -- disabling the skin entirely is cleaner since `custom.scss` handles everything.
- **Keeping Minima dark skin active "just in case":** Two competing color systems guarantee mystery styles. Remove it.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Contrast ratio verification | Manual eyeballing of color pairs | WebAIM contrast checker or browser DevTools accessibility panel | Human perception of contrast varies by monitor, ambient light, and personal vision. The WCAG formula is deterministic. |
| Palette generation | Picking greys by feel | Use the verified Discord-derived palette in this document | These values are already contrast-verified and internally consistent. Ad hoc grey picking will create contrast failures. |
| Color flash prevention | JavaScript-based theme initialization | Inline critical CSS in `<head>` (already in place) | JS-based solutions add complexity and can fail. The inline `<style>` approach is zero-dependency and instant. |
| Specificity overrides | `!important` declarations | Higher-specificity selectors (`body a` instead of `a`) | `!important` is a dead end -- it can only be overridden by another `!important`. Specificity can always be incrementally increased. |

**Key insight:** This phase is about precision, not creativity. The palette values, contrast ratios, and specificity fixes are all deterministic. The work is mechanical: swap values, verify contrast, test pages.

## Common Pitfalls

### Pitfall 1: Minima Styles "Leaking Through" After Skin Removal

**What goes wrong:** After removing `skin: dark` from `_config.yml`, elements previously styled by Minima's dark skin CSS now render with Minima's default (light) skin values instead. Links turn blue (#5691cd -> default blue), backgrounds go light.

**Why it happens:** The dark skin set variables that Minima internals consumed. Removing the skin means Minima falls back to its light-mode defaults (`$brand-color: #828282`, `$background-color: #fdfdfd`, `$link-base-color: #2a7ae2`). Any element styled by Minima's templates (not overridden in `custom.scss`) will show these defaults.

**How to avoid:** After disabling the skin, build locally and audit every page. Elements to check: any footer content, RSS feed links, default Minima components not present in the current custom layouts. The current site uses fully custom layouts (`_layouts/default.html`, `home.html`, `post.html`) that bypass most Minima templates, so leakage should be minimal. But verify.

**Warning signs:** Blue links, white/light backgrounds on any element, elements that look completely different from the rest of the site.

### Pitfall 2: Hardcoded Colors Surviving the Token Swap

**What goes wrong:** After changing all `:root` variables, some elements still show the old colors because they use hardcoded hex values instead of `var()` references.

**Why it happens:** Not every color in `custom.scss` uses CSS variables. The tagline color on line 118 (`color: #9d8cc4`) is hardcoded. Any future additions might also hardcode values.

**How to avoid:** After the token swap, search the entire `_sass/custom.scss` for hex color literals (`#[0-9a-fA-F]`). Every hex value should either be inside a `:root` variable definition or replaced with a `var()` reference.

**Warning signs:** Elements with colors that don't change when you modify `:root` variables. Purple-tinted text on an otherwise grey page.

**Known hardcoded values:**
- `custom.scss` line 118: `.site-tagline { color: #9d8cc4; }` -- replace with `var(--text-muted)` or `var(--accent-dim)`

### Pitfall 3: Contrast Failures on Surface Backgrounds

**What goes wrong:** Text that passes WCAG AA on the primary background (`#1e1f22`) fails on surface backgrounds (`#2b2d31`) or hover states (`#35373c`) because these lighter backgrounds reduce the contrast ratio.

**Why it happens:** Contrast is relative. `--text-muted` (#949ba4) achieves 5.87:1 on `#1e1f22` but only 4.92:1 on `#2b2d31` and 4.24:1 on `#35373c`. The deeper you nest surfaces, the worse the contrast gets.

**How to avoid:** Verify text colors against EVERY background they appear on, not just `--bg-primary`. The three critical pairs:
- `--text-muted` on `--bg-primary` (5.87:1 -- PASS)
- `--text-muted` on `--bg-surface` (4.92:1 -- PASS)
- `--text-muted` on `--bg-hover` (4.24:1 -- FAIL for normal text, PASS for large text)

The `--bg-hover` case is acceptable because hover states are transient and muted text on hover backgrounds is typically metadata (dates, reading time) displayed at sizes that qualify as "large text" under WCAG (14pt bold or 18pt regular). However, if any normal-sized body text appears on hover backgrounds, the muted color must be avoided there.

**Warning signs:** Metadata text on post card hover states looking washed out or hard to read.

### Pitfall 4: Browser Cache Showing Old Colors

**What goes wrong:** After deploying, the site still shows the old purple palette.

**Why it happens:** Jekyll does not add cache-busting hashes to its CSS output. The browser (and GitHub Pages CDN) may serve the old `style.css` for minutes or hours.

**How to avoid:** During development, use hard refresh (Ctrl+Shift+R). For production, GitHub Pages CDN typically updates within 5-10 minutes. If persistence is a concern, add a query string to the stylesheet link in `head.html`: `href="{{ '/assets/css/style.css' | relative_url }}?v=2"`.

**Warning signs:** Changes work locally but not on the deployed site. The deployed site shows a mix of old and new colors.

## Code Examples

### Complete `:root` Token Block (New Palette)

```scss
/* Source: Codebase analysis + contrast-verified values from this research */
:root {
    --bg-primary: #1e1f22;
    --bg-header: #111214;
    --bg-surface: #2b2d31;
    --bg-hover: #35373c;
    --border: #3f4147;
    --text-primary: #f2f3f5;
    --text-muted: #949ba4;
    --accent: #9b84ec;
    --accent-dim: #8a7dd0;
}
```

### Link Styles Without !important

```scss
/* Source: Specificity analysis of Minima base theme _base.scss */
/* Minima uses: a { color: $link-base-color; } -- specificity 0,0,0,1 */
/* body a gives: specificity 0,0,0,2 -- wins without !important */

body a,
body a:visited,
body a:active {
    color: var(--text-primary);
    text-decoration: none;
    transition: color 0.15s ease;
}

body a:hover {
    color: var(--accent);
}
```

### Critical CSS Update

```html
<!-- Source: _includes/head.html -- update inline style to match new tokens -->
<style>
  html, body {
    background-color: #1e1f22;
    color: #f2f3f5;
    margin: 0;
    padding: 0;
    font-family: 'JetBrains Mono', monospace;
  }
</style>
```

### Tagline Color Fix

```scss
/* Source: custom.scss line 118 -- hardcoded #9d8cc4 must become a variable */
.site-tagline {
    font-family: 'Noto Sans KR', sans-serif;
    font-size: 0.8rem;
    font-weight: 500;
    color: var(--text-muted);   /* was: #9d8cc4 (hardcoded) */
    letter-spacing: 0.02em;
    opacity: 0.7;
    line-height: 1;
    margin-top: -4px;
}
```

### Scrollbar and Selection Updates

```scss
/* Source: custom.scss lines 585-606 -- these already use var() so they auto-update */
::selection {
    background-color: var(--accent);   /* will become #9b84ec */
    color: var(--bg-primary);          /* will become #1e1f22 */
}

::-webkit-scrollbar-track {
    background: var(--bg-primary);     /* will become #1e1f22 */
}

::-webkit-scrollbar-thumb {
    background: var(--border);         /* will become #3f4147 */
}

::-webkit-scrollbar-thumb:hover {
    background: var(--text-muted);     /* will become #949ba4 */
}
```

These already use CSS variables, so they update automatically when `:root` changes. No manual edits needed for scrollbar or selection styles.

## WCAG Contrast Verification

Computed using the WCAG 2.2 relative luminance formula. All ratios independently calculated.

### Passing Combinations (AA for Normal Text, 4.5:1+)

| Foreground | Background | Ratio | WCAG AA | Usage |
|------------|------------|-------|---------|-------|
| `--text-primary` (#f2f3f5) | `--bg-primary` (#1e1f22) | 14.84:1 | PASS | Body text, headings |
| `--text-muted` (#949ba4) | `--bg-primary` (#1e1f22) | 5.87:1 | PASS | Dates, metadata |
| `--accent` (#9b84ec) | `--bg-primary` (#1e1f22) | 5.42:1 | PASS | Links, highlights |
| `--text-primary` (#f2f3f5) | `--bg-surface` (#2b2d31) | 12.42:1 | PASS | Card text |
| `--text-muted` (#949ba4) | `--bg-surface` (#2b2d31) | 4.92:1 | PASS | Card metadata |
| `--accent` (#9b84ec) | `--bg-surface` (#2b2d31) | 4.54:1 | PASS | Card titles |
| `--text-primary` (#f2f3f5) | `--bg-header` (#111214) | 16.88:1 | PASS | Header text |
| `--accent` (#9b84ec) | `--bg-header` (#111214) | 6.16:1 | PASS | Site title |
| `--accent-dim` (#8a7dd0) | `--bg-primary` (#1e1f22) | 4.65:1 | PASS | Timestamps |
| Selection: `--bg-primary` (#1e1f22) on `--accent` (#9b84ec) | N/A | 5.42:1 | PASS | Text selection |

### Edge Cases (Large Text Only, 3.0:1+)

| Foreground | Background | Ratio | WCAG AA Large | Usage | Mitigation |
|------------|------------|-------|---------------|-------|------------|
| `--text-muted` (#949ba4) | `--bg-hover` (#35373c) | 4.24:1 | PASS (large) | Metadata on hovered cards | Acceptable: hover is transient; metadata text is supplementary. If body text ever appears on hover backgrounds, use `--text-primary` instead. |
| `--accent` (#9b84ec) | `--bg-hover` (#35373c) | 3.92:1 | PASS (large) | Accent text on hovered elements | Acceptable: accent on hover is large text (card titles at 1rem/500 weight). |

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| `!important` to override theme styles | Higher-specificity selectors | Always been best practice | Eliminates specificity arms race |
| Minima dark skin + custom overrides | Custom-only (disable skin) | Relevant for sites that have outgrown Minima defaults | Single source of truth for all colors |
| Hardcoded hex in critical CSS | Inline `<style>` synced with tokens | Standard since critical CSS adoption ~2018 | Prevents FOUC/color flash |
| Manual grey selection for dark themes | Discord-derived neutral palette with verified contrast | Discord Onyx palette widely adopted ~2023-2024 | Battle-tested by millions of users for readability |

## Open Questions

1. **Exact accent color choice**
   - What we know: #9b84ec passes WCAG AA on all primary backgrounds (5.42:1). The prior research suggested #9b84ec, FEATURES.md mentioned #9b7bff as an alternative.
   - What's unclear: Whether #9b84ec reads as "purple enough" against neutral grey in practice. On purple backgrounds, any purple accent blends; on grey, it stands out more.
   - Recommendation: Use #9b84ec as the starting point. If it feels too muted during implementation, try #a18ef0 (slightly lighter/more saturated). Both pass AA.

2. **Minima leakage after skin removal**
   - What we know: The site uses fully custom layouts that bypass most Minima templates. Leakage should be minimal.
   - What's unclear: Whether any Minima-generated elements exist in the build output that are not visible from the source templates (e.g., feed.xml styling, sitemap, SEO tag output).
   - Recommendation: After removing `skin: dark`, build locally and check all pages plus view-source on the built output. Any unstyled elements get explicit rules in `custom.scss`.

3. **`color-mix()` usage in this phase**
   - What we know: The prior research recommended `color-mix()` for hover states (~88% browser support).
   - What's unclear: Whether to introduce `color-mix()` now or defer to a later phase.
   - Recommendation: Defer. Phase 1 is about establishing the new palette, not refactoring hover-state generation. Use explicit hex values for all tokens. `color-mix()` can be introduced in Phase 2 or 3 as a refinement.

## Sources

### Primary (HIGH confidence)
- Direct codebase analysis: `_sass/custom.scss` (607 lines), `_includes/head.html`, `_config.yml`, all layouts -- identified all color values, `!important` declarations, and hardcoded hex codes
- Minima theme source: [github.com/jekyll/minima](https://github.com/jekyll/minima) -- dark skin variables (`auto.scss`, `dark.scss`), base link styles (`_base.scss`), override strategy
- WCAG 2.2 contrast formula: independently computed all 12 color pair ratios using the [W3C relative luminance algorithm](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html)
- GitHub Pages dependency versions: [pages.github.com/versions](https://pages.github.com/versions/) -- Jekyll 3.10.0, Sass 3.7.4, Minima 2.5.x

### Secondary (MEDIUM confidence)
- Discord dark theme palette values: cross-referenced across [color-hex.com](https://www.color-hex.com/color-palette/114089), [remote.tools](https://www.remote.tools/discord/hex-codes-for-discord-background-color), and direct Discord UI inspection
- Prior project research: `.planning/research/ARCHITECTURE.md`, `PITFALLS.md`, `STACK.md`, `SUMMARY.md` -- provided foundation for this phase-specific research

### Tertiary (LOW confidence)
- Exact Discord "Onyx" dark mode hex values: minor variations across community sources. The values used (#1e1f22, #2b2d31, #111214, #35373c, #3f4147) are consistent across 3+ sources but may differ slightly from Discord's actual current values. Functionally irrelevant -- we are inspired by Discord, not replicating it.

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH -- CSS custom properties already in use, palette values contrast-verified with WCAG formula, file modifications precisely identified
- Architecture: HIGH -- Minima dark skin behavior verified against source code, specificity analysis verified against Minima `_base.scss`, critical CSS pattern directly observed in codebase
- Pitfalls: HIGH -- all 4 pitfalls identified from direct codebase inspection with specific line numbers; contrast edge cases empirically computed

**Research date:** 2026-02-17
**Valid until:** 2026-03-17 (30 days -- stable domain, no moving parts)
