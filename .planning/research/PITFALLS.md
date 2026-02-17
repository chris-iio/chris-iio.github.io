# Pitfalls Research

**Domain:** Jekyll blog CSS/design refresh (dark purple to dark grey/Discord-style)
**Researched:** 2026-02-17
**Confidence:** HIGH

## Critical Pitfalls

### Pitfall 1: Hardcoded Background Color in Critical CSS Causes Flash of Wrong Theme

**What goes wrong:**
The `_includes/head.html` contains an inline `<style>` block with the background color hardcoded to `#262335` (the current dark purple). If you update CSS variables in `custom.scss` but forget to update this inline style, users see a brief purple flash before the stylesheet loads. This is a variant of FART (Flash of inAccurate coloR Theme).

**Why it happens:**
The inline critical CSS exists specifically to prevent a white flash on dark-themed sites. It loads before the external stylesheet. During a color scheme migration, this inline block is easy to overlook because it lives in a template file, not in the SCSS.

**How to avoid:**
Update `_includes/head.html` inline styles at the same time as `_sass/custom.scss` `:root` variables. Treat them as a single atomic change. Both `background-color` and `color` values in the inline block must match the new `--bg-primary` and `--text-primary`.

**Warning signs:**
- Brief color flash on page load (especially on slower connections or cache-cleared browser)
- The inline style in `head.html` references a hex value that does not match `--bg-primary` in `custom.scss`

**Phase to address:**
Phase 1 (Color Variables) -- this must be the very first change made, alongside the variable update.

---

### Pitfall 2: `!important` Declarations Create Specificity Debt During Migration

**What goes wrong:**
The current `custom.scss` already uses `!important` on link colors (lines 63-71: `color: var(--text-primary) !important`). When changing the color palette, these `!important` rules make it impossible to override link colors contextually without adding more `!important` declarations. This cascades into a specificity war where every new style needs `!important` to win.

**Why it happens:**
The `!important` was originally added to fight minima's built-in styles. When you change the underlying color variables, some minima default styles may re-emerge in unexpected places (footer links, code block text, etc.) because the minima dark skin has its own color definitions that interact unpredictably with custom overrides.

**How to avoid:**
Before changing colors, audit and remove `!important` declarations. Replace them with higher-specificity selectors. The current site uses custom layouts that bypass most of minima's HTML structure, so the `!important` force is likely unnecessary. Test by removing one at a time and checking which minima styles bleed through. For those that do, use a more specific selector like `body a` instead of `a`.

**Warning signs:**
- Adding `!important` to any new CSS rule during the redesign
- Colors not changing despite updating CSS variables
- Inconsistent link/text colors across different page types (home vs post vs about)

**Phase to address:**
Phase 1 (Color Variables) -- clean up specificity before changing colors, not after.

---

### Pitfall 3: Minima Theme Skin Variables Conflict with Custom Variables

**What goes wrong:**
The `_config.yml` declares `minima: skin: dark`. Minima's dark skin defines its own background, text, and brand colors. The site then imports minima (`@import "minima"`) and custom (`@import "custom"`) in `style.scss`. Custom overrides minima, but only where specificity is equal or higher. Changing `--bg-primary` in `:root` does not change minima's internal Sass variables like `$brand-color`, `$site-title-color`, `$background-color`, etc. This means some elements styled by minima internals (like the footer, RSS link, or any default component not fully overridden) will still use the old dark skin colors.

**Why it happens:**
Minima uses Sass variables (`$variable`) at compile time, while the custom theme uses CSS custom properties (`--variable`) at runtime. These are two completely different systems. Changing one does not affect the other.

**How to avoid:**
Two options, choose one:
1. **Override minima's Sass variables** by creating `_sass/minima/custom-variables.scss` and setting `$background-color`, `$text-color`, `$brand-color` etc. to match the new palette. This catches any minima component you have not fully overridden.
2. **Stop using minima's dark skin** -- set `minima: skin: classic` (or remove the skin setting) and let `custom.scss` control everything. Since the site already overrides virtually every visual element, the dark skin is redundant baggage that can only cause conflicts.

Option 2 is recommended. The custom stylesheet is comprehensive enough to be the sole source of truth.

**Warning signs:**
- Random elements (footer, RSS icon, pagination) showing colors that do not match the new palette
- Building locally and seeing styles that do not match what `custom.scss` defines
- Any element where you cannot trace the style back to a rule in `custom.scss`

**Phase to address:**
Phase 1 (Color Variables) -- resolve the minima skin conflict before changing any colors.

---

### Pitfall 4: Contrast Ratio Failures When Shifting to Grey Background

**What goes wrong:**
The current muted text color (`--text-muted: #848bbd`) was designed to look good against dark purple (`#262335`). Against a dark grey background like Discord's `#313338` or `#36393e`, the same muted text may fail WCAG AA contrast requirements (4.5:1 minimum for normal text). The accent color `#ff7edb` (pink) will also have a different perceived contrast against neutral grey vs. warm purple.

**Why it happens:**
Contrast perception is relative. Purple backgrounds boost the perceived brightness of blue-tinted muted text. Against neutral grey, the same blue-tinted text appears duller and harder to read. People eyeball it and think "looks fine" without checking ratios.

**How to avoid:**
For every color combination in the new palette, run it through a contrast checker (WebAIM or browser DevTools). Specifically check:
- `--text-primary` (#ffffff) against new `--bg-primary` -- likely fine
- `--text-muted` against new `--bg-primary` -- likely needs adjustment
- `--text-muted` against new `--bg-surface` -- likely needs adjustment
- `--accent` (#ff7edb) against new `--bg-primary` -- check this, pink on grey is less vibrant
- `--accent-dim` (#f97e72) against new `--bg-primary` -- check this too

Target: all text at 4.5:1 minimum (AA), interactive elements at 3:1 minimum.

**Warning signs:**
- Muted text that felt readable on purple feels "flat" or "washed out" on grey
- Accent color feels less punchy or vibrant
- Squinting to read meta text (dates, reading time)

**Phase to address:**
Phase 2 (Palette Application) -- after setting new base colors, verify every combination before moving to layout changes.

---

### Pitfall 5: Forgetting to Update Dependent Surface Colors (Incomplete Palette Migration)

**What goes wrong:**
The current palette has internally consistent purple-tinted surface colors: `--bg-primary: #262335`, `--bg-header: #241b2f`, `--bg-surface: #2a2139`, `--bg-hover: #34294f`, `--border: #34294f`. If you change `--bg-primary` to a neutral grey but leave the surface/hover/border colors as purple-tinted, the site looks like a half-finished redesign. Cards, headers, and hover states will have a purple cast that clashes with the new grey background.

**Why it happens:**
It is natural to change the most visible background color first and feel done. But a coherent dark theme has 5-8 shades that all need to shift together. Discord's palette demonstrates this: `#1e1f22` (darkest), `#2b2d31` (sidebar), `#313338` (main), `#383a40` (hover), `#3f4147` (active). Every shade is neutral grey.

**How to avoid:**
Define the complete new palette before writing any CSS. Map each existing variable to its new value:
- `--bg-primary`: main background (e.g., `#313338`)
- `--bg-header`: slightly darker (e.g., `#2b2d31`)
- `--bg-surface`: card/code block background (e.g., `#2b2d31`)
- `--bg-hover`: interactive hover state (e.g., `#383a40`)
- `--border`: subtle separation (e.g., `#3f4147`)
- `--text-primary`: keep `#ffffff`
- `--text-muted`: adjust for new contrast (e.g., `#b5bac1`, Discord's muted text)
- `--accent`: keep `#ff7edb` (purple accent is intentional)
- `--accent-dim`: may need adjustment

Change ALL variables in a single commit. Never leave half the palette old and half new.

**Warning signs:**
- Purple-tinted borders or surfaces visible against grey backgrounds
- Cards that look "highlighted" when they should blend in
- Headers that feel disconnected from the page body

**Phase to address:**
Phase 1 (Color Variables) -- define complete palette upfront, apply as single atomic change.

---

## Technical Debt Patterns

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Keeping `!important` on links | Quick override of minima defaults | Every future style addition needs `!important` to compete | Never -- fix during this redesign |
| Keeping minima dark skin active while custom overrides everything | Avoid touching `_config.yml` | Two competing color systems, mystery styles appearing | Never -- disable skin or align it |
| Hardcoding hex values instead of using CSS variables | Faster one-off fix | Any future palette change requires hunting all hardcoded values | Never -- the site already uses variables, do not add hardcoded exceptions |
| Skipping `custom-variables.scss` for minima overrides | Fewer files to manage | Minima internal components use old colors | Acceptable for MVP if all minima components are fully overridden in custom.scss |
| Not adding `sass: quiet_deps: true` to config | One less config change | Noisy build output with deprecation warnings drowns out real errors | Never -- add it now, it is a one-line config |

## Integration Gotchas

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| Google Fonts (`head.html`) | Changing fonts without updating the `font-family` fallback stack, causing layout shift if font fails to load | Keep the fallback stack in sync; test with network throttling to see fallback rendering |
| Jekyll Sass pipeline | Editing SCSS and not seeing changes because browser cached old CSS (Jekyll does not add cache-busting hashes) | Hard-refresh during development (Ctrl+Shift+R); for production, consider adding a `?v=` query string to the stylesheet link |
| GitHub Pages deployment | Pushing SCSS changes but GitHub Pages serves old cached CSS | GitHub Pages CDN can take 5-10 minutes to update; use `?v=timestamp` on stylesheet link or wait |
| Minima gem via github-pages | Expecting minima 3.x features but github-pages gem pins minima to 2.5.x | Do not use minima 3.x features (like `@use`-based overrides); stick to 2.5.x patterns (`@import`, skin settings) |

## Performance Traps

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Loading multiple Google Font weights unnecessarily | Slow font load, visible FOUT | The current `head.html` loads JetBrains Mono 400/500/600 + Noto Sans KR 300/400 -- that is 5 weight files. Only use weights that appear in the CSS. Currently 300-weight Noto Sans KR is unused. | Noticeable on 3G/slow connections |
| Not using `font-display: swap` | Text invisible until font loads | Google Fonts URL already includes `display=swap` -- verify this stays present after any URL changes | First visit on slow connection |
| Overly broad CSS reset (`* { margin: 0; padding: 0; }`) | Eliminates all browser defaults; must re-style every element | The current reset is already in place and working -- do not remove it mid-redesign or add a second one | Not a scale issue, but can cause regression if modified |

## UX Pitfalls

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| Monospace body text at long line lengths | JetBrains Mono is optimized for code, not prose. Reading fatigue on blog posts longer than 500 words. Characters are wider, so 720px max-width fits fewer words per line than a proportional font would. | This is a deliberate design choice (terminal aesthetic) -- keep it, but ensure line-height stays at 1.8 for post content and max-width stays at 720px. Do NOT increase max-width during redesign. |
| Post card hover shifting content (`transform: translateX(4px)`) | On touch devices, tap causes a brief rightward shift that feels janky | Consider removing the translateX on mobile (it already has reduced padding on mobile, the shift compounds the cramped feel) |
| Sticky header consuming vertical space on mobile | 15px base font + sticky header = less content visible on small screens | The header is compact enough (roughly 50px). Do not make it taller during redesign. |

## "Looks Done But Isn't" Checklist

- [ ] **Background colors:** All 5 background variables updated (`--bg-primary`, `--bg-header`, `--bg-surface`, `--bg-hover`, `--border`) -- verify no purple tint remains
- [ ] **Critical CSS:** Inline style in `_includes/head.html` matches new `--bg-primary` value
- [ ] **Contrast ratios:** Muted text and accent colors verified against all new background colors (4.5:1 AA minimum)
- [ ] **Scrollbar:** Custom scrollbar colors in `::-webkit-scrollbar-track` and `::-webkit-scrollbar-thumb` updated to match new palette
- [ ] **Selection highlight:** `::selection` background/foreground still readable with new accent on new background
- [ ] **Mobile nav dropdown:** `.nav-links` background uses `--bg-header` -- verify it matches new header color and has visible borders
- [ ] **Code blocks:** `pre` and inline `code` backgrounds use `--bg-surface` -- ensure they are distinguishable from (but harmonious with) new `--bg-primary`
- [ ] **Link colors:** Verify all states (default, visited, hover, active) render correctly after removing `!important`
- [ ] **About page:** Skill tags and cert list borders use theme variables -- verify they render with new palette
- [ ] **Minima leakage:** Check for any minima-styled elements that are not overridden (footer, feed icon, etc.)

## Recovery Strategies

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| Hardcoded critical CSS mismatch | LOW | Single line change in `_includes/head.html` |
| `!important` specificity war | MEDIUM | Audit all `!important` rules, replace with higher-specificity selectors, test each page type |
| Minima skin conflict | LOW | Change `_config.yml` skin setting, rebuild, verify |
| Contrast ratio failures | LOW | Adjust `--text-muted` and `--accent-dim` values, re-check with contrast checker |
| Incomplete palette migration | MEDIUM | Audit all CSS variable references, update remaining values, check scrollbar/selection/mobile nav |
| Purple flash on page load | LOW | Update inline style in `head.html` to match new background |

## Pitfall-to-Phase Mapping

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| Hardcoded critical CSS mismatch | Phase 1 (Color Variables) | Load site with cache cleared; no color flash visible |
| `!important` specificity war | Phase 1 (Color Variables) | `grep -c '!important' custom.scss` returns 0 (or minimal count) |
| Minima skin conflict | Phase 1 (Color Variables) | Disable dark skin, rebuild, no unexpected colors appear |
| Contrast ratio failures | Phase 2 (Palette Application) | All text/background combos pass WebAIM contrast checker at AA level |
| Incomplete palette migration | Phase 1 (Color Variables) | Visual audit of every page (home, post, about, cert) -- no purple tint visible |
| Unused font weights loaded | Phase 3 (Polish) | Google Fonts URL only loads weights that appear in CSS rules |
| Mobile hover jank | Phase 3 (Polish) | Tap post cards on mobile -- no horizontal shift |

## Sources

- Codebase analysis: `/home/chris/my-blog/_sass/custom.scss`, `/home/chris/my-blog/_includes/head.html`, `/home/chris/my-blog/_config.yml` (direct inspection, HIGH confidence)
- [Flash of inAccurate coloR Theme (FART) -- CSS-Tricks](https://css-tricks.com/flash-of-inaccurate-color-theme-fart/) (MEDIUM confidence)
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/) (HIGH confidence, W3C-referenced tool)
- [WCAG 2.2 Contrast Requirements](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html) (HIGH confidence, official W3C)
- [Discord Dark Theme Color Palette](https://www.color-hex.com/color-palette/114089) (MEDIUM confidence, community-sourced)
- [Minima @import deprecation issue #815](https://github.com/jekyll/minima/issues/815) (HIGH confidence, official repo)
- [Minima lighten() deprecation issue #836](https://github.com/jekyll/minima/issues/836) (HIGH confidence, official repo)
- [Jekyll Minima theme overrides](https://github.com/jekyll/minima) (HIGH confidence, official repo)
- [JetBrains Mono readability characteristics](https://www.jetbrains.com/lp/mono/) (HIGH confidence, official page)

---
*Pitfalls research for: Jekyll blog CSS design refresh (dark purple to dark grey)*
*Researched: 2026-02-17*
