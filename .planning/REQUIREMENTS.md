# Requirements: Chris Infante's Blog

**Defined:** 2026-02-17
**Core Value:** A clean, readable blog where Chris can publish posts without friction

## v1 Requirements

Requirements for the visual refresh. Each maps to roadmap phases.

### Color & Theme

- [ ] **CLR-01**: Site background changes from dark purple (#262335) to Discord-style dark grey (~#1e1f22)
- [ ] **CLR-02**: All surface colors (header, cards, hover states, borders) shift to neutral greys matching the new background
- [ ] **CLR-03**: Critical CSS in head.html stays in sync with new palette (no color flash on load)
- [ ] **CLR-04**: All text/background color combinations pass WCAG AA contrast (4.5:1 minimum)
- [ ] **CLR-05**: Scrollbar, selection highlight, and other accent elements updated to match new palette

### Typography & Readability

- [ ] **TYP-01**: Heading hierarchy visually distinct (h1/h2/h3 sizes spread further apart)
- [ ] **TYP-02**: Fluid typography using clamp() for smooth responsive scaling
- [ ] **TYP-03**: In-post links are visually distinguishable without hovering (accent color or underline)

### Layout & Navigation

- [ ] **NAV-01**: GitHub link (github.com/chris-iio) appears in header next to LinkedIn
- [ ] **NAV-02**: "Posts" section title removed from homepage
- [ ] **NAV-03**: Minimal footer added to all pages (copyright, social links)
- [ ] **NAV-04**: Post card hover effect refined (vertical lift instead of horizontal slide)

### Content Polish

- [ ] **CNT-01**: Syntax highlighting CSS for code blocks (dark theme compatible)
- [ ] **CNT-02**: Anchor links on headings (hoverable link icon for sharing sections)

### Technical Cleanup

- [ ] **TCH-01**: Remove !important declarations from link styles, replace with higher-specificity selectors
- [ ] **TCH-02**: Resolve Minima dark skin conflict (disable skin or align variables)

## v2 Requirements

Deferred to future. Tracked but not in current roadmap.

### Typography

- **TYP-04**: Switch body text to Inter proportional font, keep JetBrains Mono for code

### Content Features

- **CNT-03**: Table of contents for long posts (3+ headings)
- **CNT-04**: Tag/category pills on posts
- **CNT-05**: Reading progress bar on post pages
- **CNT-06**: "Last updated" date on posts

### Polish

- **POL-01**: Print stylesheet
- **POL-02**: Page fade-in animation on load
- **POL-03**: Subtle background noise texture
- **POL-04**: RSS feed XSLT styling

## Out of Scope

| Feature | Reason |
|---------|--------|
| Dark/light mode toggle | Blog is dark-themed by identity; doubling color work for no benefit |
| Comment system | Unnecessary complexity for a personal blog |
| Search functionality | Not enough content to warrant it |
| Newsletter/email capture | RSS feed covers subscribe use case |
| Sidebar layout | Breaks single-column reading flow |
| Complex grid/masonry layouts | No hero images per post to fill them |
| Social share buttons | Visual clutter; everyone knows how to copy a URL |
| SPA page transitions | JS weight, breaks browser nav, maintenance burden |
| Pagination | Fewer than 20 posts; show all on homepage |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| CLR-01 | TBD | Pending |
| CLR-02 | TBD | Pending |
| CLR-03 | TBD | Pending |
| CLR-04 | TBD | Pending |
| CLR-05 | TBD | Pending |
| TYP-01 | TBD | Pending |
| TYP-02 | TBD | Pending |
| TYP-03 | TBD | Pending |
| NAV-01 | TBD | Pending |
| NAV-02 | TBD | Pending |
| NAV-03 | TBD | Pending |
| NAV-04 | TBD | Pending |
| CNT-01 | TBD | Pending |
| CNT-02 | TBD | Pending |
| TCH-01 | TBD | Pending |
| TCH-02 | TBD | Pending |

**Coverage:**
- v1 requirements: 16 total
- Mapped to phases: 0
- Unmapped: 16

---
*Requirements defined: 2026-02-17*
*Last updated: 2026-02-17 after initial definition*
