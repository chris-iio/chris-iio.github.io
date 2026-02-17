# Feature Landscape: Personal Dev Blog Visual Refresh

**Domain:** Personal developer blog (dark theme, single-column layout)
**Researched:** 2026-02-17
**Reference sites:** steipete.me, overreacted.io, joshwcomeau.com

## Table Stakes

Features users expect. Missing any of these and the blog looks amateur or half-finished.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| **Consistent color palette with proper contrast** | Mismatched colors or poor contrast screams "I didn't design this." WCAG AA contrast ratios (4.5:1 body text, 3:1 large text) are the baseline. | Low | Current palette is purple-heavy (#262335 bg). Shift to Discord-style dark greys (#1e1f22 bg, #2b2d31 surfaces, #313338 elevated) with purple as accent only. |
| **Readable body typography** | 50-75 characters per line (66 is the sweet spot). Line height 1.6-1.8 for body text. Without this, long posts are painful to read. | Low | Current 720px max-width with monospace font at 0.9rem produces very short lines (~50 chars). Monospace is wider per character -- keep the width but bump font size to 1rem for post content, or use a proportional font for body text and reserve monospace for code/nav. |
| **Clear typographic hierarchy** | Readers scan before they read. H1 > H2 > H3 must be visually distinct in size, weight, and spacing. | Low | Current h1 (1.5rem), h2 (1.25rem), h3 (1.1rem) are too close together. Spread them: h1 at 1.75rem, h2 at 1.35rem, h3 at 1.1rem with color/weight differentiation. |
| **Syntax-highlighted code blocks** | Dev blog without syntax highlighting looks broken. This is table stakes for the audience. | Low | Jekyll uses Rouge by default with kramdown. Generate a dark theme CSS with `rougify style monokai > syntax.css` or use base16.monokai.dark. Current code blocks have basic styling but no syntax colors. |
| **Responsive mobile layout** | Over 50% of web traffic is mobile. A blog that breaks on phones is a non-starter. | Low | Already implemented with hamburger nav and responsive breakpoints at 640px. Solid foundation. |
| **Sticky header navigation** | Users expect persistent navigation, especially on long posts. Losing the nav forces scroll-to-top. | Low | Already implemented with `position: sticky`. Keep it. |
| **Post metadata** (date + reading time) | Sets expectations for the reader. "How old is this? How long will it take?" Both are standard on every dev blog worth reading. | Low | Already implemented in post cards and post headers. |
| **Hover states and transitions** | Static elements feel dead. Subtle hover feedback (color shift, slight movement) signals interactivity. | Low | Already implemented on post cards (translateX, border-color change). Could be refined but functional. |
| **Proper link styling within posts** | In-content links must be visually distinct from surrounding text. Invisible links make content feel broken. | Low | Currently all links are white with hover-to-accent. In post content, links should have a persistent accent color or underline so readers can spot them without hovering. |
| **Custom scrollbar (dark theme)** | Default light scrollbar on a dark site is jarring. Small detail, big impact. | Low | Already implemented. |
| **Favicon** | Missing favicon = amateur. | Low | Already implemented (SVG + PNG fallback). |
| **Selection color** | Default blue selection on dark theme looks wrong. | Low | Already implemented (accent bg, dark text). |
| **Footer** | Even minimal blogs need a footer: copyright, maybe social links, RSS. No footer = page feels unfinished. | Low | Currently missing. Add a minimal footer: copyright year, maybe a link to RSS feed and source repo. |
| **Accessible color contrast** | Not just ethical -- it's practical. Low contrast means nobody reads your blog on a sunny day or on a cheap monitor. | Low | Verify new palette against WCAG AA. Discord's palette was designed for all-day readability, which is a good foundation. |

## Differentiators

Features that elevate the blog from "adequate" to "this person cares about their craft." Not expected, but noticed.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| **Post-level table of contents** | For longer posts (1500+ words), a TOC at the top of the post helps readers navigate. Shows you write substantial content worth navigating. Not a sidebar TOC -- just an inline list of H2s at the top. | Low | Jekyll/kramdown can auto-generate TOC with `{:toc}`. Style it as a subtle, collapsible section. Only show on posts with 3+ headings. |
| **Refined post card design** | Post cards that feel like real designed elements -- not just styled `<li>` items. Subtle left-border accent on hover, clean spacing, excerpt that actually says something. Reference: overreacted.io's card hover (scale 1.005). | Low | Current cards are decent. Refine: remove translateX (feels odd), add subtle scale or border-left accent. Keep the surface-color background. |
| **Smooth page transitions** | Content that doesn't just pop in but feels placed. Even a simple fade-in on page load (CSS-only, no JS framework needed) adds polish. | Low | Pure CSS: `@keyframes fadeIn` on `.page-content`. Keep it under 300ms. No library needed. |
| **Tag/category pills on posts** | Small colored pills showing post topics (e.g., "security", "privacy", "linux"). Helps with scannability on the post list. Keeps it to 3-5 total tags across the blog, not per-post. | Low | Add tags to post front matter. Display as small pills in post cards and post headers. Use muted styling -- don't let tags dominate the visual hierarchy. |
| **Back-to-top button** | On long posts, a subtle fixed button that appears after scrolling down. Small quality-of-life feature. | Low | CSS + minimal JS (show after 500px scroll). Style as a subtle circle with up-arrow in the accent color. |
| **"Last updated" date on posts** | Shows content is maintained, not abandoned. Especially valuable for technical content that can go stale. | Low | Add `last_modified_at` to front matter. Display alongside publish date when different. |
| **Subtle background texture or noise** | A barely-visible noise texture on the background (1-2% opacity) adds depth that flat color lacks. Discord does this. It's the difference between "painted wall" and "digital flat." | Low | CSS: `background-image` with a tiny noise SVG or PNG at very low opacity. Almost invisible but adds warmth. |
| **Print stylesheet** | Readers who print or save-as-PDF get a clean, ink-friendly version. Rare but impressive when present. | Low | `@media print` -- hide nav, footer, make bg white, text black. 30 lines of CSS. |
| **RSS feed with styled XML** | Jekyll generates RSS via jekyll-feed plugin (already configured). Adding an XSLT stylesheet makes the raw feed page human-readable instead of scary XML. | Medium | Requires an XSL file. Not hard, but not trivial. Nice touch for the RSS-using dev audience. |
| **Reading progress bar** | A thin accent-colored bar at the top of the page that fills as you scroll through a post. Subtle motivation to keep reading. | Low | ~15 lines of JS + a fixed 2px bar. Only show on post pages, not the homepage. |
| **Anchor links on headings** | Hovering over an H2/H3 shows a link icon. Lets readers share specific sections. Standard on good dev blogs. | Low | CSS + kramdown auto-IDs. Add a `::before` pseudo-element with a link symbol on heading hover. |

## Anti-Features

Features to deliberately NOT build. Each of these is tempting but wrong for a simple personal blog.

| Anti-Feature | Why Avoid | What to Do Instead |
|--------------|-----------|-------------------|
| **Dark/light mode toggle** | Massive complexity for a blog that has a clear dark identity. Requires doubling every color variable, testing both modes, handling preference persistence, flash-of-wrong-theme issues. The blog IS dark themed -- own it. | Commit fully to the dark palette. Ensure it meets WCAG contrast requirements so it's comfortable for everyone. |
| **Sidebar layout** | Breaks the clean single-column reading flow. Sidebars compete for attention, don't work well on mobile, and add layout complexity. steipete.me and overreacted.io both avoid sidebars entirely. | Keep single-column. Use inline elements (TOC at top of post, tags in post header) instead. |
| **Comment system** (Disqus, utterances, etc.) | Adds third-party JS, privacy concerns, moderation burden, and visual clutter. A personal blog is for publishing, not discussion. | Link to social profiles. If you want discussion, point to a thread on LinkedIn or Mastodon. |
| **Newsletter signup / email capture** | Popup modals, sticky banners, inline CTAs -- all of these degrade the reading experience for a personal blog. The RSS feed already serves the "subscribe" use case. | Keep the RSS feed link visible. That's enough. |
| **Animation-heavy design** | Parallax scrolling, floating elements, complex SVG animations. These are for portfolio sites selling design services, not for blogs selling ideas through writing. | Limit animations to micro-interactions: hover states, page fade-in, progress bar. Everything under 300ms. |
| **Search functionality** | For a blog with <50 posts, search adds complexity with near-zero value. Readers can use browser Ctrl+F or browse the post list. | Keep the post list scannable. Add tags for lightweight filtering later if needed. |
| **Complex grid/masonry post layouts** | Multi-column card grids look great with images. Without hero images on every post (which you shouldn't force), they create uneven, messy layouts. | Stick with vertical single-column post list. Each card is full-width. Clean, predictable, scannable. |
| **Hero images per post** | Forces you to find/create an image for every post, which becomes a blogging friction point. Most dev blog posts don't benefit from a header image. | Let the title and excerpt speak for themselves. If a post genuinely needs an image, it goes inline in the content. |
| **Custom 404 page with illustrations** | Fun but not valuable. Time spent here is time not spent on content or core design. | Jekyll's default 404 with your site layout is fine. Maybe add a single line: "Nothing here. Head back to posts." |
| **Social share buttons** | Visual clutter. Everyone knows how to copy a URL. Share buttons from 2015 look dated. | If you want sharing, a single "copy link" button is cleaner than a row of social icons. Even that is optional. |
| **Page transition animations (JS-based)** | SPA-style page transitions (barba.js, swup) add JS weight, break browser navigation expectations, and create maintenance burden. | CSS fade-in on page load is enough. Let the browser handle navigation normally. |
| **Pagination** | For a blog with fewer than 20-30 posts, pagination fragments the reading experience. "Show all" is better than "page 1 of 3." | Show all posts on the homepage. Revisit pagination only if you exceed 50+ posts. Config has `paginate: 5` -- this is too aggressive; remove it. |

## Feature Dependencies

```
Color palette refresh --> ALL other visual features (everything inherits from CSS variables)
Typography hierarchy --> Post content readability
                     --> Code block styling (font decisions affect code too)
Syntax highlighting  --> Code block styling (needs color tokens from palette)
Footer               --> RSS link placement
                     --> Social link placement
Tag system           --> Post card design (tags display in cards)
                     --> Post header design (tags display in post view)
TOC generation       --> Anchor links on headings (TOC links need heading IDs)
```

## MVP Recommendation

Prioritize in this order (each builds on the previous):

1. **Color palette refresh** -- Foundation for everything else. Switch to Discord-inspired dark greys, keep purple accent. This alone transforms the feel of the entire site.
2. **Typography tightening** -- Fix heading hierarchy, improve body readability, ensure proper line-height. Maybe switch post body to a proportional sans-serif (Inter, system-ui) and keep JetBrains Mono for nav/headers/code.
3. **Syntax highlighting** -- Generate Rouge dark theme CSS. Immediate credibility boost for a security/dev blog.
4. **In-post link styling** -- Make links visible in body text (underline or persistent accent color).
5. **Footer** -- Minimal: copyright, RSS link, social link. Closes the visual loop on every page.
6. **Post content polish** -- Anchor links on headings, inline TOC for long posts, better blockquote/list styling.

**Defer:**
- Tag system: Adds front-matter complexity. Do it when you have 10+ posts and need discoverability.
- Reading progress bar: Nice but not necessary. Add when core design is solid.
- Print stylesheet: Low effort but low priority. Add whenever.
- RSS XSLT styling: Medium effort, niche audience. Backlog.

## Existing Strengths to Preserve

The current blog already has several things right. These should be kept, not redesigned:

- **Single-column centered layout** -- This is correct. steipete.me, overreacted.io, and most respected dev blogs use this.
- **JetBrains Mono** -- Excellent monospace font choice for a security/dev blog identity. Consider keeping it for nav and headings while using a proportional font for body text only.
- **Post card format** -- Cards with title, date, reading time, excerpt. This is the right structure.
- **Korean tagline** -- Distinctive personal touch. Keep it.
- **Hamburger nav on mobile** -- Standard, works, no need to reinvent.
- **Sticky header** -- Correct for this layout.
- **Reading time estimate** -- Already implemented, already valuable.

## Color Palette Direction

Based on the user's stated preference for "dark grey like Discord, purple accent":

| Token | Current | Proposed | Role |
|-------|---------|----------|------|
| --bg-primary | #262335 (purple-tinted) | #1e1f22 (Discord dark) | Page background |
| --bg-surface | #2a2139 (purple-tinted) | #2b2d31 (Discord surface) | Cards, code blocks |
| --bg-elevated | N/A | #313338 (Discord elevated) | Hover states, header |
| --bg-header | #241b2f (deep purple) | #1e1f22 or #2b2d31 | Sticky header |
| --border | #34294f (purple) | #3f4147 (Discord border) | Borders, dividers |
| --text-primary | #ffffff | #f2f3f5 (Discord text) | Body text, headings |
| --text-muted | #848bbd (blue-purple) | #949ba4 (Discord muted) | Dates, metadata |
| --accent | #ff7edb (pink) | #9b7bff or #b5a3ff (purple) | Links, highlights, brand |
| --accent-dim | #f97e72 (coral) | #7c6bc4 (muted purple) | Secondary accent |

This shifts from "terminal theme with pink accents" to "Discord-like neutral dark with purple identity." The purple accent becomes a deliberate choice rather than the entire palette.

## Sources

- [steipete.me](https://steipete.me/) -- Reference site specified by user. Single-column Astro blog, minimal design. (Confidence: HIGH, directly inspected)
- [overreacted.io](https://overreacted.io/) -- Dan Abramov's blog. Gold standard for minimal dev blog design. Single-column, card-based post list, dark mode, excellent typography. (Confidence: HIGH, directly inspected)
- [joshwcomeau.com](https://www.joshwcomeau.com/) -- Josh Comeau's blog. Represents the "high production" end of dev blogs with interactive elements, category browsing, dark theme. (Confidence: HIGH, directly inspected)
- [Discord dark theme palette](https://www.color-hex.com/color-palette/114089) -- #202225, #292b2f, #2f3136, #40444b, #36393f. (Confidence: HIGH, widely documented)
- [Baymard Institute line length research](https://baymard.com/blog/line-length-readability) -- 50-75 CPL optimal, 66 sweet spot. (Confidence: HIGH, peer-reviewed UX research)
- [UXPin optimal line length](https://www.uxpin.com/studio/blog/optimal-line-length-for-readability/) -- 65ch CSS unit recommendation. (Confidence: HIGH)
- [Jekyll Rouge syntax highlighting](https://mcpride.github.io/posts/development/2018/03/06/syntax-highlighting-with-jekyll/) -- `rougify style` for theme generation. (Confidence: HIGH, official Jekyll tooling)
- [CSS-Tricks sticky TOC](https://css-tricks.com/sticky-table-of-contents-with-scrolling-active-states/) -- IntersectionObserver for active state tracking. (Confidence: HIGH)
- [Marketer Milk blog design roundup](https://www.marketermilk.com/blog/best-blog-designs) -- Cross-site design pattern analysis. (Confidence: MEDIUM, editorial source)
