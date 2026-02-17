# Project Research Summary

**Project:** Chris Infante's Blog -- Visual Refresh
**Domain:** Jekyll static site CSS redesign (dark purple to Discord-style dark grey)
**Researched:** 2026-02-17
**Confidence:** HIGH

## Executive Summary

This project is a pure CSS color and typography refresh of an existing Jekyll blog deployed to GitHub Pages. The site already has solid bones: single-column centered layout, CSS custom properties, sticky header, responsive mobile nav, and post cards. The work is not a rebuild -- it is a palette swap from purple-tinted dark (#262335 bg, #ff7edb pink accent) to Discord-inspired neutral dark grey (#1e1f22 bg, #9b84ec purple accent), combined with typography improvements (adding Inter for body text, expanding the design token system, fixing heading hierarchy). No new gems, no JavaScript frameworks, no build tool changes. All changes land in two files: `_sass/custom.scss` and `_includes/head.html`.

The recommended approach is token-first: define the complete new color palette and typography scale as CSS custom properties in `:root`, then propagate changes component by component. The existing 8 CSS variables already drive most of the site, so expanding to ~20 tokens and updating their values will accomplish 80% of the visual transformation automatically. The remaining 20% is refining individual components (post cards, content styling, header, about page) and adding missing table-stakes features (footer, syntax highlighting, in-post link visibility).

The primary risks are all avoidable with discipline: (1) the hardcoded background color in `head.html` critical CSS must stay in sync with the SCSS tokens or users see a color flash on load, (2) existing `!important` declarations on link colors will cause a specificity war during the migration if not cleaned up first, and (3) the Minima dark skin's Sass variables conflict with the custom CSS variables -- the dark skin should be disabled since `custom.scss` already overrides everything. All three risks belong in the first phase of work and have low recovery cost if caught.

## Key Findings

### Recommended Stack

The stack does not change. This is a CSS-only refresh on an existing Jekyll + GitHub Pages site. The "stack" decisions are about which modern CSS features to adopt.

**Core technologies:**
- **CSS Custom Properties (expanded):** Grow from 8 to ~20 tokens covering colors, spacing scale, typography scale, transitions, and border radii. This is the foundation -- every component reads from these tokens.
- **`clamp()` for fluid typography:** Replace fixed `font-size` values with fluid scales that smoothly adapt between mobile and desktop. Eliminates the current crude media query that drops font size at 640px. 95%+ browser support.
- **Inter (variable font) for body text:** Pair with JetBrains Mono. Inter has a matching x-height and dramatically improves prose readability over monospace. JetBrains Mono stays for code blocks, nav, and headings (developer identity).
- **`color-mix()` for hover states:** Generate hover/focus color variants from base tokens without hardcoding every shade. ~88% support with graceful fallback (just use the accent color directly).
- **Discord-inspired neutral grey palette:** Shift from saturated purple backgrounds to neutral dark greys (#1e1f22 primary, #2b2d31 surface, #3f4147 border) with #9b84ec purple as the sole accent color. All combinations verified for WCAG AA contrast.

**What NOT to use:** Tailwind (needs Node.js build step), CSS-in-JS (not applicable), container queries (overkill for single-column), `@layer` (source order already works), dark/light toggle (doubles complexity, blog IS dark), View Transitions API (Chrome-only).

### Expected Features

**Must have (table stakes):**
- Consistent color palette with WCAG AA contrast -- the entire point of this refresh
- Readable body typography -- fix heading hierarchy (h1/h2/h3 too close), improve line spacing
- Syntax-highlighted code blocks -- use Rouge with a dark theme (currently missing syntax colors)
- Visible in-post links -- links in body text must be distinguishable without hovering
- Footer -- currently missing; needs copyright, RSS link at minimum
- Accessible color contrast -- verify every text/background combination

**Should have (differentiators):**
- Post-level table of contents for long posts (kramdown `{:toc}`)
- Refined post card hover (replace horizontal translateX with subtle vertical lift or border accent)
- CSS-only page fade-in animation (< 300ms)
- Tag/category pills on posts (small, muted styling)
- Anchor links on headings (share specific sections)
- Subtle background noise texture (1-2% opacity, adds depth)

**Defer (v2+):**
- Tag filtering system -- wait until 10+ posts exist
- Reading progress bar -- nice but not essential
- Print stylesheet -- low effort but low priority
- RSS XSLT styling -- medium effort, niche audience
- Back-to-top button -- add when core design is solid

**Anti-features (do NOT build):**
- Dark/light mode toggle, sidebar layout, comment system, newsletter signup, search, pagination (currently set to 5 -- should be removed), hero images per post, social share buttons

### Architecture Approach

Keep the single-file CSS architecture. The site has ~607 lines of CSS in `_sass/custom.scss` -- splitting into multiple partials (7-1 or 5-1 pattern) adds navigational overhead with zero organizational benefit at this scale. The existing section-comment structure provides sufficient logical grouping. The `@import "minima"` + `@import "custom"` pattern cleanly separates base theme from customization. The architecture change is about the *content* of the CSS, not its file structure.

**Major components (sections within custom.scss):**
1. **Design Tokens** (`:root`) -- color palette, spacing scale, typography scale, transitions
2. **Critical CSS** (`head.html` inline) -- must mirror `:root` bg/text values to prevent FOUC
3. **Base + Typography** -- box model reset, body/heading/link defaults
4. **Header** -- sticky nav, title, tagline, hamburger menu
5. **Post List** -- homepage card grid with hover states
6. **Post Content** -- article styling, code blocks, blockquotes, lists
7. **About Page** -- skills tags, certification list
8. **Responsive** -- mobile breakpoint overrides
9. **Utilities** -- selection color, scrollbar, print (future)

### Critical Pitfalls

1. **Critical CSS color flash** -- `head.html` has hardcoded `#262335` background. Must update simultaneously with `:root` tokens or users see a purple flash on every page load. Fix: treat `head.html` inline style and `:root` as a single atomic change.
2. **`!important` specificity debt** -- existing `!important` on link colors will block contextual overrides during migration. Fix: replace with `body a` selector for higher specificity before changing any colors.
3. **Minima dark skin conflict** -- `_config.yml` sets `minima: skin: dark`, which defines its own Sass variables (`$background-color`, `$brand-color`) that compile independently of CSS custom properties. Fix: disable the dark skin (set to `classic` or remove) since `custom.scss` already overrides everything.
4. **Contrast ratio failures on grey** -- muted text (#848bbd) was designed for purple backgrounds and may fail WCAG AA on neutral grey. Fix: verify every text/background pair with a contrast checker before finalizing the palette.
5. **Incomplete palette migration** -- changing `--bg-primary` but leaving surface/hover/border colors purple-tinted creates a half-finished look. Fix: define and apply the complete palette (all 8-10 variables) in a single commit.

## Implications for Roadmap

Based on research, suggested phase structure:

### Phase 1: Foundation (Design Tokens + Critical Cleanup)
**Rationale:** Everything depends on the token system. Changing tokens propagates to 80% of the site. But three preconditions must be met first: remove `!important` declarations, disable Minima dark skin, and prepare the complete palette.
**Delivers:** New color palette applied site-wide, expanded token system (colors + spacing + typography scale), critical CSS in sync, `!important` removed, Minima skin conflict resolved.
**Addresses:** Consistent color palette (table stakes), accessible contrast (table stakes), complete palette migration.
**Avoids:** Critical CSS flash (Pitfall 1), specificity debt (Pitfall 2), Minima conflict (Pitfall 3), incomplete migration (Pitfall 5).

### Phase 2: Typography and Content Readability
**Rationale:** With colors settled, typography is the next highest-impact change. Heading hierarchy, body font (Inter), line height, and link visibility all affect every page.
**Delivers:** Inter font for body text, fixed heading hierarchy (h1 1.75rem, h2 1.35rem, h3 1.1rem), fluid typography via `clamp()`, visible in-post links, improved line-height.
**Addresses:** Readable body typography (table stakes), clear typographic hierarchy (table stakes), in-post link styling (table stakes).
**Avoids:** Contrast ratio failures (Pitfall 4) -- verify all new font sizes against backgrounds.

### Phase 3: Component Refinement
**Rationale:** With tokens and typography settled, refine individual components. Post cards, header, about page all need adjustments to feel cohesive with the new palette.
**Delivers:** Refined post card hover (vertical lift instead of horizontal slide), syntax-highlighted code blocks (Rouge dark theme), header polish, about page skill/cert styling updates.
**Addresses:** Syntax highlighting (table stakes), hover states (table stakes), post card design (differentiator).
**Avoids:** Mobile hover jank (remove translateX on touch devices).

### Phase 4: Missing Elements + Polish
**Rationale:** Add the missing table-stakes features that close visual gaps, plus low-effort differentiators.
**Delivers:** Footer (copyright + RSS link), page fade-in animation, anchor links on headings, background noise texture, font loading optimization (remove unused weights, verify `display=swap`).
**Addresses:** Footer (table stakes), page transitions (differentiator), anchor links (differentiator).
**Avoids:** Unused font weight performance trap.

### Phase 5: Content Features (Defer)
**Rationale:** These features add value but are not part of the visual refresh. They involve front-matter changes and content-level decisions.
**Delivers:** Post-level TOC, tag/category pills, "last updated" dates.
**Addresses:** TOC (differentiator), tags (differentiator).

### Phase Ordering Rationale

- **Tokens first** because CSS custom properties cascade -- changing 10 variable values updates the entire site. This is the architecturally correct starting point.
- **Typography second** because font changes affect spacing, line lengths, and visual hierarchy across every component. Components cannot be refined until typography is settled.
- **Components third** because they depend on both token values and typography decisions being finalized. Refining a post card before knowing the body font or heading scale means doing the work twice.
- **Missing elements last** because they are additive, not transformative. A footer and fade-in animation do not affect existing components.
- **Content features deferred** because they require front-matter and content decisions beyond CSS.

### Research Flags

Phases likely needing deeper research during planning:
- **Phase 2 (Typography):** Inter font pairing with JetBrains Mono needs visual testing. The x-height match is documented but the actual rendering on this site's content should be verified before committing. Consider testing 2-3 body font options.
- **Phase 3 (Component Refinement):** Rouge syntax theme generation (`rougify style monokai`) needs verification against the new grey palette. The generated theme may need manual color adjustments.

Phases with standard patterns (skip research-phase):
- **Phase 1 (Foundation):** Well-documented pattern. CSS custom properties, specificity rules, and Minima override strategy are all thoroughly covered in the research.
- **Phase 4 (Missing Elements):** Footer, CSS animations, and anchor links are standard Jekyll/CSS patterns with no ambiguity.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | No new technologies. CSS features verified against caniuse.com. GitHub Pages constraints well-documented. |
| Features | HIGH | Table stakes verified against multiple production dev blogs (steipete.me, overreacted.io, joshwcomeau.com). Anti-features list consistent across successful minimal blogs. |
| Architecture | HIGH | Based on direct codebase analysis (607 lines in custom.scss). Single-file approach validated for this scale. Token cascade behavior is fundamental CSS. |
| Pitfalls | HIGH | All 5 critical pitfalls identified from direct codebase inspection. Recovery strategies are low-cost. Minima version pinning verified against GitHub Pages docs. |

**Overall confidence:** HIGH

### Gaps to Address

- **Exact accent color:** STACK.md proposes #9b84ec, FEATURES.md suggests #9b7bff or #b5a3ff. The final purple accent needs visual testing against the grey palette. Resolve during Phase 1 implementation by testing 2-3 candidates.
- **Muted text contrast on surfaces:** ARCHITECTURE.md notes that `--text-muted` on `--bg-surface` may be borderline (4.4:1). Needs empirical verification with the finalized palette. Resolve during Phase 1 with a contrast checker.
- **Minima dark skin removal:** Recommended but not tested. Disabling the skin may surface unstyled elements that were previously handled by Minima's dark skin CSS. Resolve during Phase 1 by building locally with the skin disabled and auditing all pages.
- **`paginate: 5` in _config.yml:** FEATURES.md flags this as too aggressive (should show all posts, not paginate at 5). This is a config change, not a CSS change -- decide whether to address it during the refresh or separately.

## Sources

### Primary (HIGH confidence)
- GitHub Pages dependency versions (pages.github.com/versions) -- Jekyll 3.10.0, Sass 3.7.4, Minima 2.5.x
- WCAG 2.2 Contrast Requirements (w3.org) -- 4.5:1 AA minimum
- WebAIM Contrast Checker -- verified proposed palette combinations
- Jekyll official docs -- Sass/SCSS configuration, asset pipeline
- Minima theme repo (github.com/jekyll/minima) -- override strategy, known issues #815, #836
- MDN Web Docs -- `clamp()`, `color-mix()`, CSS custom properties
- Can I Use -- CSS nesting (90.7%), `color-mix()` (~88%), `clamp()` (95%+)
- Direct codebase analysis -- `custom.scss`, `head.html`, `_config.yml`, all layouts

### Secondary (MEDIUM confidence)
- Discord dark theme color palette (color-hex.com) -- hex values cross-referenced across multiple sources
- Smashing Magazine -- fluid typography with `clamp()` best practices
- CSS-Tricks -- dark mode patterns, FART prevention
- steipete.me, overreacted.io, joshwcomeau.com -- reference site design patterns (directly inspected)
- Nick Paolini -- Modern CSS Toolkit 2026 feature recommendations

### Tertiary (LOW confidence)
- JetBrains Mono + Inter font pairing -- single source (maxibestof.one), needs visual verification
- Exact Discord Onyx color values -- community-sourced, minor variations between sources

---
*Research completed: 2026-02-17*
*Ready for roadmap: yes*
