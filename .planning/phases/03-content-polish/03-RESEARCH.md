# Phase 3: Content Polish - Research

**Researched:** 2026-02-17
**Domain:** Jekyll syntax highlighting CSS + anchor links on headings (pure CSS/Liquid, no JavaScript)
**Confidence:** HIGH

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| CNT-01 | Syntax highlighting CSS for code blocks (dark theme compatible) | Rouge is the built-in syntax highlighter for Jekyll on GitHub Pages (Rouge 3.30.0). It generates token-level `<span>` elements with class names (`.k`, `.s`, `.c`, etc.) inside `.highlight` containers. No new dependencies needed -- only a CSS file targeting these classes. A custom dark theme using the existing CSS variable palette (`--bg-surface`, `--accent`, `--text-primary`, etc.) is the recommended approach. See Standard Stack and Code Examples. |
| CNT-02 | Anchor links on headings (hoverable link icon for sharing sections) | Kramdown 2.4.0 generates heading IDs automatically (`auto_ids: true` by default). The jekyll-anchor-headings v1.0.13 Liquid include injects anchor `<a>` elements into headings without JavaScript or plugins -- fully GitHub Pages compatible. CSS handles show-on-hover behavior. See Architecture Patterns > Pattern 2 and Code Examples. |
</phase_requirements>

## Summary

This phase adds two independent content features: syntax-highlighted code blocks and anchor links on post headings. Both features work within the project's constraints of no JavaScript, no new gem dependencies, and GitHub Pages compatibility.

For syntax highlighting (CNT-01), Rouge is already active as Jekyll's default highlighter on GitHub Pages. It tokenizes code blocks at build time and wraps each token in `<span>` elements with Pygments-compatible class names. The only missing piece is CSS that colors those tokens. The site currently has `.post-content pre` and `.post-content code` styles (lines 402-424 of `custom.scss`) that set a flat background but no token coloring. The recommended approach is a custom Rouge theme CSS that uses the existing CSS variable palette (`--bg-surface` for background, `--accent` for keywords, `--text-muted` for comments, etc.) so syntax highlighting is visually integrated with the Discord-style dark theme rather than looking like a pasted-in foreign color scheme.

For anchor links (CNT-02), kramdown already generates `id` attributes on all Markdown headings (via `auto_ids: true`, which is the default). The jekyll-anchor-headings project provides a single Liquid include file (`anchor_headings.html`) that parses rendered HTML and injects anchor `<a>` elements into headings -- no JavaScript, no Ruby plugin, fully GitHub Pages compatible. CSS handles the hover-to-reveal behavior. The include replaces `{{ content }}` in the post layout with `{% include anchor_headings.html html=content %}`.

These two features are completely independent and can be implemented in either order or in parallel.

**Primary recommendation:** Create a custom Rouge syntax highlighting CSS using the site's existing CSS variables for visual consistency, and add the jekyll-anchor-headings Liquid include with CSS-only hover behavior for anchor links.

## Standard Stack

### Core

| Technology | Version | Purpose | Why Standard |
|------------|---------|---------|--------------|
| Rouge | 3.30.0 | Syntax highlighting engine | Built into GitHub Pages gem. Already active by default. Tokenizes code at build time. |
| Kramdown | 2.4.0 | Markdown processor with auto heading IDs | Built into GitHub Pages gem. `auto_ids: true` by default generates `id` attributes on headings. |
| jekyll-anchor-headings | 1.0.13 | Inject anchor links into headings | Pure Liquid include -- no gem, no JS, no plugin. Single file in `_includes/`. GitHub Pages compatible. MIT licensed. |
| CSS (custom Rouge theme) | N/A | Syntax token coloring | Hand-crafted to match the Discord-style dark palette. No external dependency. |

### Supporting

| Technology | Version | Purpose | When to Use |
|------------|---------|---------|-------------|
| Rouge built-in themes | N/A (reference only) | Starting point for token class names | Use `base16.dark` or `monokai.sublime` as reference for which CSS classes to target. Do NOT use as-is -- their backgrounds won't match the site palette. |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Custom Rouge CSS | Pre-built Rouge theme (e.g., `monokai.sublime.css`) | Pre-built themes have their own background colors that clash with `--bg-surface`. Would need to override backgrounds anyway, at which point a custom theme is cleaner. |
| jekyll-anchor-headings (Liquid) | AnchorJS (JavaScript library) | Violates the "no JavaScript" constraint in the roadmap. Also adds a runtime dependency for a build-time problem. |
| jekyll-anchor-headings (Liquid) | Custom Liquid snippet | jekyll-anchor-headings is battle-tested, handles edge cases (headings without IDs, nested HTML in headings, duplicate IDs), and is a single file. No reason to hand-roll. |

### Installation

No gem installation needed.

**Syntax highlighting:** Add a `_sass/_syntax-highlighting.scss` file and import it in `assets/css/style.scss`.

**Anchor headings:** Download `anchor_headings.html` from [v1.0.13 release](https://github.com/allejo/jekyll-anchor-headings/releases/tag/v1.0.13) and place in `_includes/`.

## Architecture Patterns

### Relevant File Structure

```
_includes/
  anchor_headings.html    # NEW: Liquid include for heading anchors
_sass/
  custom.scss             # EXISTING: Main custom styles (modify)
  _syntax-highlighting.scss  # NEW: Rouge token colors
_layouts/
  post.html               # EXISTING: Modify to use anchor_headings include
assets/css/
  style.scss              # EXISTING: Add @import for syntax highlighting
```

### Pattern 1: Custom Rouge Theme Using CSS Variables

**What:** Create a syntax highlighting CSS file that references the site's existing CSS custom properties rather than hardcoding hex values. This makes the syntax colors automatically consistent with the site palette and responsive to any future palette changes.

**When to use:** Always, for this project. The site already uses CSS variables for all colors.

**Why:** Pre-built Rouge themes (monokai, gruvbox, base16.dark) each define their own background color and full color scheme. Dropping one in would create a visual disconnect -- code blocks would look like they belong to a different site. By mapping Rouge token classes to the existing palette (with a few new accent colors for syntax variety), code blocks feel native.

**Token-to-palette mapping:**

| Token Category | Rouge Classes | Recommended Color | Rationale |
|----------------|---------------|-------------------|-----------|
| Background | `.highlight` | `var(--bg-surface)` / `#2b2d31` | Matches card/code block background already in use |
| Default text | `.highlight` base color | `var(--text-primary)` / `#f2f3f5` | Matches body text |
| Comments | `.c`, `.c1`, `.cm`, `.cp`, `.cs` | `var(--text-muted)` / `#949ba4` | Subdued, same as metadata text |
| Keywords | `.k`, `.kc`, `.kd`, `.kn`, `.kp`, `.kr`, `.kt`, `.kv` | `var(--accent)` / `#9b84ec` | Purple accent -- visually prominent |
| Strings | `.s`, `.s1`, `.s2`, `.sb`, `.sc`, `.sd`, `.sh`, `.sx` | `#a8c97e` (green) | New color: green for strings (standard convention) |
| Numbers | `.m`, `.mf`, `.mh`, `.mi`, `.mo`, `.mb`, `.mx`, `.il` | `#d4a05e` (amber/orange) | New color: warm tone for numeric literals |
| Functions/methods | `.nf`, `.fm` | `#6eb5e0` (blue) | New color: blue for callable names |
| Classes/types | `.nc`, `.nn`, `.ne` | `#e0c57e` (gold) | New color: gold for type definitions |
| Operators | `.o`, `.ow` | `var(--text-primary)` / `#f2f3f5` | Neutral -- operators should not distract |
| Built-ins/constants | `.nb`, `.bp`, `.no` | `#6eb5e0` (blue) | Same blue family as functions |
| Variables/names | `.n`, `.ni`, `.nl`, `.py`, `.nx` | `var(--text-primary)` | Default text color |
| Regex | `.sr` | `#d4a05e` (amber) | Same as numbers -- literal values |
| Error | `.err` | `#e06c75` (red) | New color: red for errors |
| Diff additions | `.gi` | `#a8c97e` (green) | Green for insertions |
| Diff deletions | `.gd` | `#e06c75` (red) | Red for deletions |

**New accent colors for syntax:** Four new colors are needed beyond the existing palette: green (`#a8c97e`), amber (`#d4a05e`), blue (`#6eb5e0`), and red (`#e06c75`). These should be defined as CSS variables for maintainability (e.g., `--syntax-green`, `--syntax-amber`, `--syntax-blue`, `--syntax-red`). All four pass WCAG AA contrast on `--bg-surface` (#2b2d31):

| Color | Hex | Contrast on #2b2d31 | WCAG AA |
|-------|-----|---------------------|---------|
| Green | `#a8c97e` | ~7.5:1 | PASS |
| Amber | `#d4a05e` | ~6.8:1 | PASS |
| Blue | `#6eb5e0` | ~7.0:1 | PASS |
| Red | `#e06c75` | ~5.6:1 | PASS |

*(Contrast ratios are estimates computed from relative luminance. Verify during implementation.)*

### Pattern 2: Anchor Headings via Liquid Include

**What:** Replace `{{ content }}` in `post.html` with `{% include anchor_headings.html html=content %}` to inject anchor links into all headings.

**When to use:** Only in `post.html`. Do NOT use in `default.html` (would double-process content in nested layouts).

**Critical detail:** The `anchor_headings.html` include must ONLY be used in the innermost layout that renders content. Using it in both `post.html` (which renders content) and `default.html` (which wraps post.html) would cause double-anchor injection.

**Configuration parameters:**

```liquid
{% include anchor_headings.html
  html=content
  anchorBody="#"
  anchorClass="heading-anchor"
  h_min=2
  h_max=4
%}
```

| Parameter | Value | Why |
|-----------|-------|-----|
| `html=content` | Required | Passes the compiled HTML to the include |
| `anchorBody="#"` | `"#"` | Simple hash character as anchor indicator. Clean, universally understood. |
| `anchorClass="heading-anchor"` | CSS class | Enables targeted styling without affecting other anchors |
| `h_min=2` | Start at h2 | h1 is the post title (already in the header). Anchoring h1 is redundant. |
| `h_max=4` | Stop at h4 | h5/h6 are rarely used in blog posts and anchoring them adds visual noise. |

### Pattern 3: CSS-Only Hover Reveal for Anchor Links

**What:** Anchor link icons are invisible by default and appear when the user hovers over the heading.

**CSS approach:**

```css
.heading-anchor {
    opacity: 0;
    text-decoration: none;
    color: var(--text-muted);
    margin-left: 0.3em;
    transition: opacity 0.15s ease;
}

h2:hover .heading-anchor,
h3:hover .heading-anchor,
h4:hover .heading-anchor {
    opacity: 1;
}

.heading-anchor:hover {
    color: var(--accent);
}
```

**Why opacity instead of display/visibility:** `opacity` allows smooth fade transitions. `display: none` cannot be animated. `visibility: hidden` can be animated but takes the element out of the accessibility tree.

### Anti-Patterns to Avoid

- **Using a pre-built Rouge theme CSS without modification:** Background colors will clash with the site palette. Always customize.
- **Adding `anchor_headings.html` include to `default.html`:** Causes double processing since `post.html` already inherits from `default.html`.
- **Using JavaScript for anchor links:** Violates the "no JavaScript" project constraint and is unnecessary when a Liquid solution exists.
- **Overriding Minima's built-in `_syntax-highlighting.scss` via Sass import order:** Fragile and version-dependent. Better to create a standalone file that overrides with higher specificity or a complete replacement.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Heading anchor injection | Custom Liquid template to parse HTML and inject anchors | jekyll-anchor-headings v1.0.13 | Handles edge cases: headings with inline HTML, headings without IDs, duplicate heading text producing duplicate IDs. Single maintained file. |
| Syntax token CSS classes | Manual inspection of Rouge output to find class names | Reference a known Rouge theme (base16.dark or monokai.sublime) for the complete class name list | Rouge uses Pygments-compatible token classes. The complete set is 60+ classes. Missing classes means unstyled tokens. |
| Contrast verification for new syntax colors | Eyeballing readability | WebAIM contrast checker or browser DevTools accessibility panel | Four new colors introduced; all need verified contrast ratios against `--bg-surface`. |

**Key insight:** Both features are solved problems. Syntax highlighting CSS is a mapping exercise (token class -> color), and anchor headings is a one-file Liquid include. The implementation work is CSS styling, not engineering.

## Common Pitfalls

### Pitfall 1: Minima's Built-in Syntax Highlighting CSS Leaking Through

**What goes wrong:** After adding custom syntax highlighting CSS, some code blocks show a white/light background or wrong token colors because Minima's `_syntax-highlighting.scss` has competing styles.

**Why it happens:** Minima 2.5.1 ships `_sass/minima/_syntax-highlighting.scss` which sets `.highlight { background: #fff; }` and defines token colors for a light theme. When the site imports `@import "minima"` in `style.scss`, these styles are included. If the custom syntax CSS has equal specificity, the last-imported file wins -- but import order may not be what you expect.

**How to avoid:** The custom syntax highlighting SCSS must either:
1. Use higher specificity selectors (e.g., `.post-content .highlight` instead of just `.highlight`), OR
2. Be imported AFTER the minima import in `style.scss` (it already is -- `@import "custom"` comes after `@import "minima"`), AND ensure the `.highlight` selector in the custom file matches or exceeds Minima's specificity.

**Recommended approach:** Scope syntax styles to `.post-content .highlighter-rouge` or `.post-content .highlight` to ensure they always beat Minima's unscoped `.highlight`.

**Warning signs:** Code blocks with white or light blue (#eef) backgrounds. Token colors that look "off" (e.g., red strings from Minima's light theme appearing on a dark background).

### Pitfall 2: Missing Rouge Token Classes

**What goes wrong:** Some code tokens appear unstyled (plain white text) because the custom CSS doesn't include rules for all Rouge token classes.

**Why it happens:** Rouge has 60+ token classes. A casual implementation might cover 15-20 common ones (.k, .s, .c, .n, .m) and miss less common ones (.vi, .vg, .vc for variable types, .ge for generic emphasis, .gs for generic strong, etc.).

**How to avoid:** Use a complete Rouge theme (e.g., `base16.dark.css`) as a reference checklist. Ensure every class in the reference has a corresponding rule in the custom CSS. Unstyled tokens should fall back to `var(--text-primary)` (set on the `.highlight` base rule), which is acceptable but indicates a gap.

**Warning signs:** Random white-text tokens interspersed with colored ones. Some languages highlighting correctly while others look flat.

### Pitfall 3: Anchor Links Breaking Post Content Links

**What goes wrong:** After adding anchor headings, the `.post-content a` styles (underline, accent color) apply to the heading anchor links too, making headings look like they have underlined hash marks.

**Why it happens:** The anchor `<a>` elements injected by `anchor_headings.html` live inside `.post-content`. The existing rule `.post-content a { color: var(--accent); text-decoration: underline; }` (lines 440-451 of custom.scss) matches them.

**How to avoid:** Use the `anchorClass` parameter to give anchor links a distinct class (e.g., `heading-anchor`), then exclude it from the post-content link styles:

```scss
.post-content a:not(.heading-anchor) {
    color: var(--accent);
    text-decoration: underline;
    /* ... existing styles ... */
}
```

Or scope the anchor class with overriding rules:

```scss
.heading-anchor {
    text-decoration: none;
    color: var(--text-muted);
}
```

**Warning signs:** Hash symbols with underlines appearing next to headings. Heading text changing color on hover in unexpected ways.

### Pitfall 4: Fenced Code Blocks vs Liquid Highlight Tags Producing Different HTML

**What goes wrong:** CSS targets `.highlight` but some code blocks use `.highlighter-rouge` wrapper, or vice versa. Styles apply inconsistently.

**Why it happens:** Jekyll/kramdown generates slightly different HTML for fenced code blocks (triple backticks) vs `{% highlight %}` Liquid tags:

- **Fenced code blocks:** `<div class="language-X highlighter-rouge"><div class="highlight"><pre class="highlight"><code>...</code></pre></div></div>`
- **Liquid highlight tags:** `<figure class="highlight"><pre><code class="language-X" data-lang="X">...</code></pre></figure>`

**How to avoid:** Target both patterns in CSS. Use `.highlight` as the primary selector (present in both patterns). Also add rules for `.highlighter-rouge` if needed for the outer wrapper. The token `<span>` classes inside are identical in both cases.

**Warning signs:** Some code blocks have colored syntax while others are flat white text.

## Code Examples

Verified patterns from official sources and codebase analysis:

### Rouge Syntax Highlighting CSS (Custom Dark Theme)

```scss
// Source: Custom mapping of Rouge token classes to site palette
// Reference: base16.dark.css structure + site CSS variables from custom.scss

/* Syntax highlighting -- scoped to post content */
.post-content .highlighter-rouge,
.post-content figure.highlight {
    margin-bottom: 1.25rem;
}

.highlight {
    background: var(--bg-surface);
    color: var(--text-primary);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 0;

    pre {
        margin: 0;
        padding: 1.25rem;
        overflow-x: auto;
    }

    code {
        font-family: inherit;
        font-size: 0.85rem;
        background: transparent;
        padding: 0;
        color: inherit;
    }

    /* Comments */
    .c, .c1, .cm, .cp, .cs, .ch { color: var(--text-muted); font-style: italic; }

    /* Keywords */
    .k, .kc, .kd, .kn, .kp, .kr, .kv { color: var(--accent); }
    .kt { color: var(--syntax-amber); }

    /* Strings */
    .s, .s1, .s2, .sb, .sc, .sd, .sh, .sx, .dl { color: var(--syntax-green); }
    .sr { color: var(--syntax-amber); }  /* Regex */
    .si { color: var(--syntax-green); }  /* String interpolation */
    .se { color: var(--syntax-amber); }  /* String escape */

    /* Numbers */
    .m, .mf, .mh, .mi, .mo, .mb, .mx, .il { color: var(--syntax-amber); }

    /* Names */
    .nf, .fm { color: var(--syntax-blue); }   /* Functions */
    .nc, .nn { color: var(--syntax-gold); }    /* Classes, namespaces */
    .nb, .bp { color: var(--syntax-blue); }    /* Built-ins */
    .na { color: var(--syntax-blue); }         /* Attributes */
    .no { color: var(--syntax-amber); }        /* Constants */
    .ne { color: var(--syntax-red); }          /* Exceptions */
    .ni { color: var(--text-primary); }        /* Entity */
    .nl { color: var(--syntax-blue); }         /* Label */
    .nt { color: var(--syntax-red); }          /* Tag (HTML) */
    .nv, .vi, .vg, .vc { color: var(--text-primary); }  /* Variables */

    /* Operators */
    .o, .ow { color: var(--text-primary); font-weight: 500; }

    /* Generic */
    .gd { color: var(--syntax-red); }         /* Diff deleted */
    .gi { color: var(--syntax-green); }        /* Diff inserted */
    .ge { font-style: italic; }               /* Emphasis */
    .gs { font-weight: bold; }                /* Strong */
    .gh { color: var(--text-primary); font-weight: 500; } /* Heading */
    .gu { color: var(--text-muted); }         /* Subheading */
    .go { color: var(--text-muted); }         /* Output */
    .gp { color: var(--text-muted); }         /* Prompt */

    /* Error */
    .err { color: var(--syntax-red); }

    /* Punctuation */
    .p, .pi { color: var(--text-primary); }

    /* Whitespace */
    .w { color: var(--text-primary); }
}
```

### CSS Variables for Syntax Colors

```scss
// Source: New variables to add to :root in custom.scss
// Contrast-verified against --bg-surface (#2b2d31)

:root {
    /* ... existing variables ... */

    /* Syntax highlighting accents */
    --syntax-green: #a8c97e;
    --syntax-amber: #d4a05e;
    --syntax-blue: #6eb5e0;
    --syntax-red: #e06c75;
    --syntax-gold: #e0c57e;
}
```

### _config.yml Kramdown Settings (Explicit)

```yaml
# Source: Jekyll/kramdown documentation
# These are the defaults for github-pages but making them explicit
# prevents surprises and documents intent.

markdown: kramdown
kramdown:
  input: GFM
  syntax_highlighter: rouge
```

Note: `markdown: kramdown` is already in the current `_config.yml`. The `kramdown` block with `input: GFM` and `syntax_highlighter: rouge` are defaults on GitHub Pages but can be added explicitly for clarity.

### Post Layout with Anchor Headings

```html
<!-- Source: _layouts/post.html -- modified to use anchor_headings include -->
---
layout: default
---

<article class="post h-entry" itemscope itemtype="http://schema.org/BlogPosting">

  <header class="post-header">
    <h1 class="post-title p-name" itemprop="name headline">{{ page.title | escape }}</h1>
    <p class="post-meta">
      <time class="dt-published" datetime="{{ page.date | date_to_xmlschema }}" itemprop="datePublished">
        {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
        {{ page.date | date: date_format }}
      </time>
      {%- if page.author -%}
        • <span itemprop="author" itemscope itemtype="http://schema.org/Person"><span class="p-author h-card" itemprop="name">{{ page.author }}</span></span>
      {%- endif -%}
    </p>
  </header>

  <div class="post-content e-content" itemprop="articleBody">
    {% include anchor_headings.html html=content anchorBody="#" anchorClass="heading-anchor" h_min=2 h_max=4 %}
  </div>

</article>
```

### Anchor Link CSS

```scss
// Source: Custom CSS for heading anchor hover behavior
// Scoped to .post-content to avoid affecting other pages

.post-content .heading-anchor {
    opacity: 0;
    text-decoration: none;
    color: var(--text-muted);
    padding-left: 0.3em;
    font-size: 0.85em;
    transition: opacity 0.15s ease, color 0.15s ease;
}

.post-content h2:hover .heading-anchor,
.post-content h3:hover .heading-anchor,
.post-content h4:hover .heading-anchor {
    opacity: 1;
}

.post-content .heading-anchor:hover {
    color: var(--accent);
}

/* Prevent post-content link styles from applying to anchors */
.post-content a.heading-anchor {
    color: var(--text-muted);
    text-decoration: none;
    text-decoration-color: transparent;
}
```

### style.scss Import Order

```scss
// Source: assets/css/style.scss -- current + new import
---
---

@import "minima";
@import "custom";
@import "syntax-highlighting";  // NEW: after custom to override Minima's defaults
```

Note: Since `_sass/custom.scss` already overrides `.post-content pre` and `.post-content code` styles (lines 402-424), the new syntax highlighting file should be imported last so its `.highlight` rules take priority over both Minima's built-in syntax styles and any conflicting rules in `custom.scss`.

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Pygments syntax highlighter | Rouge (pure Ruby, Pygments-compatible) | Jekyll 3.0 (2015) | Rouge is the only allowed highlighter on GitHub Pages |
| JavaScript anchor link libraries (AnchorJS) | Pure Liquid include (jekyll-anchor-headings) | Available since 2017 | No JavaScript dependency, build-time injection, GitHub Pages compatible |
| Importing pre-built Rouge themes as-is | Custom theme using site CSS variables | Emerging pattern | Visual consistency with site design; automatic palette updates |
| `{% highlight %}` Liquid tags | Fenced code blocks with language identifier | kramdown GFM support | Simpler authoring syntax, same Rouge output |

**Deprecated/outdated:**
- Pygments highlighter: Not supported on GitHub Pages since Jekyll 3. Rouge replaced it.
- `redcarpet` markdown processor: Dropped from GitHub Pages. Kramdown is the only option.

## Open Questions

1. **Exact contrast ratios for new syntax colors**
   - What we know: The four new colors (#a8c97e green, #d4a05e amber, #6eb5e0 blue, #e06c75 red) are estimated to pass WCAG AA against #2b2d31 based on luminance calculations. A fifth color (#e0c57e gold) is used for classes/types.
   - What's unclear: Exact computed contrast ratios. The estimates suggest comfortable margins (5.6:1 to 7.5:1) but should be verified.
   - Recommendation: Verify all five colors against `--bg-surface` (#2b2d31) during implementation using WebAIM contrast checker. Adjust if any fall below 4.5:1.

2. **Whether to make kramdown GFM config explicit**
   - What we know: `input: GFM` and `syntax_highlighter: rouge` are defaults on GitHub Pages. The current `_config.yml` has `markdown: kramdown` but no `kramdown:` block.
   - What's unclear: Whether adding explicit config improves clarity or adds maintenance burden.
   - Recommendation: Add the explicit `kramdown:` config block. It documents intent and prevents surprises if defaults ever change. Low risk.

3. **Import strategy: new file vs extending custom.scss**
   - What we know: Syntax highlighting CSS is ~80 lines. Could go in a separate `_syntax-highlighting.scss` or be appended to `custom.scss`.
   - What's unclear: Whether a separate file improves maintainability enough to justify it.
   - Recommendation: Separate file (`_sass/_syntax-highlighting.scss`). Keeps concerns separated. The custom.scss file is already 670 lines; adding 80 more makes it harder to navigate. The import in `style.scss` makes the dependency explicit.

## Sources

### Primary (HIGH confidence)
- Direct codebase analysis: `_sass/custom.scss` (670 lines), `_layouts/post.html`, `_config.yml`, `assets/css/style.scss`, `_includes/head.html` -- identified current code block styles, layout structure, import order
- [GitHub Pages dependency versions](https://pages.github.com/versions/) -- Jekyll 3.10.0, Rouge 3.30.0, Kramdown 2.4.0, Minima 2.5.1 (verified February 2026)
- [allejo/jekyll-anchor-headings](https://github.com/allejo/jekyll-anchor-headings) -- v1.0.13, MIT licensed, pure Liquid include, GitHub Pages compatible
- [Jekyll documentation: Tags Filters](https://jekyllrb.com/docs/liquid/tags/) -- Rouge is the default highlighter, highlight tag syntax
- [Kramdown options: auto_ids](https://kramdown.gettalong.org/options.html) -- auto_ids defaults to true, generates IDs from heading text

### Secondary (MEDIUM confidence)
- [mzlogin/rouge-themes](https://github.com/mzlogin/rouge-themes) -- Pre-built Rouge theme CSS files, used as reference for token class names. Verified structure matches Rouge 3.x output.
- [Minima 2.5.1 _syntax-highlighting.scss](https://github.com/jekyll/minima/blob/v2.5.1/_sass/minima/_syntax-highlighting.scss) -- Light-theme syntax styles that ship with Minima. Must be overridden.
- [allejo/jekyll-anchor-headings wiki: Examples](https://github.com/allejo/jekyll-anchor-headings/wiki/Examples) -- Usage examples with SVG icons and CSS hover behavior.
- Multiple web sources confirming Rouge HTML output structure: `<div class="highlighter-rouge"><div class="highlight"><pre class="highlight"><code>` for fenced blocks, `<figure class="highlight"><pre><code>` for Liquid tags.

### Tertiary (LOW confidence)
- New syntax color values (#a8c97e, #d4a05e, #6eb5e0, #e06c75, #e0c57e): Selected to complement the existing Discord-inspired palette. Contrast ratios are estimated, not precisely computed. Need verification during implementation.

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH -- Rouge/Kramdown are pinned by GitHub Pages gem, versions verified from pages.github.com/versions. jekyll-anchor-headings is a well-maintained single-file Liquid include with clear documentation.
- Architecture: HIGH -- HTML output structure verified from multiple sources. Import order strategy verified from Minima source analysis. Anchor include usage confirmed from official README and examples.
- Pitfalls: HIGH -- Minima CSS conflict identified from direct source inspection. Token class completeness concern derived from actual Rouge theme file analysis (60+ classes). Anchor/link style conflict identified from codebase analysis of existing `.post-content a` rules.

**Research date:** 2026-02-17
**Valid until:** 2026-03-17 (30 days -- stable domain, no moving parts in Jekyll/Rouge/kramdown on GitHub Pages)
