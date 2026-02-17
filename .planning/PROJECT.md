# Chris Infante's Blog

## What This Is

A personal blog and portfolio site for Chris Infante, built with Jekyll and deployed to GitHub Pages. Dark-themed with a Discord-inspired neutral grey palette, single-column layout with JetBrains Mono typography, syntax-highlighted code blocks, and anchor links on headings.

## Core Value

A clean, readable blog where Chris can publish posts without friction.

## Requirements

### Validated

- ✓ Jekyll static site with blog post support — existing
- ✓ Dark theme with custom styling — existing
- ✓ Single-column centered layout — existing
- ✓ About page with skills and certifications — existing
- ✓ GitHub Pages deployment — existing
- ✓ JetBrains Mono typography — existing
- ✓ Post cards on homepage — existing
- ✓ Discord-style dark grey palette (#1e1f22 base) — v1.0
- ✓ Purple accent (#9b84ec) with WCAG AA contrast — v1.0
- ✓ Clean CSS foundation (no !important, CSS variables) — v1.0
- ✓ Fluid typography with clamp() scaling — v1.0
- ✓ Heading hierarchy (h1/h2/h3 visually distinct) — v1.0
- ✓ Visible in-post links (accent color + underline) — v1.0
- ✓ GitHub icon in header navigation — v1.0
- ✓ Homepage without "Posts" section title — v1.0
- ✓ Site-wide footer with copyright and social links — v1.0
- ✓ Vertical card hover effect — v1.0
- ✓ Syntax highlighting for code blocks — v1.0
- ✓ Anchor links on headings (h2-h4) — v1.0
- ✓ Critical CSS synced (no color flash on load) — v1.0
- ✓ Scrollbar and selection colors updated — v1.0

### Active

- [ ] Ongoing blog post writing

### Out of Scope

- Comments system — unnecessary complexity for a personal blog
- Search functionality — not enough content to warrant it yet
- Mobile app — static site works on mobile already
- CMS or admin panel — Markdown + git is the workflow
- Dark/light mode toggle — blog is dark-themed by identity
- SPA page transitions — JS weight, breaks browser nav
- Inter font for body text — deferred to v2
- Table of contents for long posts — deferred to v2
- Tag/category pills — deferred to v2
- Reading progress bar — deferred to v2
- Print stylesheet — deferred to v2

## Context

- Built with Jekyll, uses Bundler for dependency management
- Theme: Discord-inspired dark grey (#1e1f22 background, #9b84ec purple accent, #949ba4 muted text)
- CSS architecture: Minima base → custom.scss (704 lines) → _syntax-highlighting.scss (82 lines)
- All colors tokenized as CSS variables in :root
- Content flow: Markdown → Liquid templates → static HTML in `_site/`
- Shipped v1.0 Visual Refresh: 3 phases, 6 plans, ~792 lines of CSS

## Constraints

- **Tech stack**: Jekyll + GitHub Pages — no migration, keep what works
- **Simplicity**: Minimal dependencies, no JavaScript frameworks
- **Maintenance**: Must stay easy to write and publish posts

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Keep Jekyll + GitHub Pages | Already working, familiar, zero hosting cost | ✓ Good |
| Discord-style dark grey over purple bg | Easier on the eyes, more modern feel | ✓ Good |
| Purple accent (#9b84ec) replacing pink (#ff7edb) | Better palette cohesion with grey base | ✓ Good |
| CSS variables for all colors | Maintainability, future theme flexibility | ✓ Good |
| body a specificity (0,0,1,1) instead of !important | Clean CSS, no specificity wars | ✓ Good |
| clamp() for fluid typography | Smooth scaling, no jarring breakpoints | ✓ Good |
| Scoped link styles (.post-content a) | Prevents nav/card link interference | ✓ Good |
| Separate _syntax-highlighting.scss partial | Keeps 82-line token CSS out of 704-line custom.scss | ✓ Good |
| jekyll-anchor-headings Liquid include (not gem) | GitHub Pages compatible, no JS, build-time | ✓ Good |
| Anchors on h2-h4 only | h1 is post title, h5/h6 rarely used | ✓ Good |

---
*Last updated: 2026-02-17 after v1.0 milestone*
