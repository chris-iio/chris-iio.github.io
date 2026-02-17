# Roadmap: Chris Infante's Blog -- Visual Refresh

## Overview

A CSS-only visual refresh of an existing Jekyll blog. The work moves from foundation (technical cleanup + new color palette) through typography and layout refinements to content polish features. Every change lands in CSS/HTML -- no new dependencies, no JavaScript, no build tool changes.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Color Foundation** - Technical cleanup and complete palette swap from purple to Discord-style dark grey
- [ ] **Phase 2: Typography & Layout** - Heading hierarchy, fluid scaling, readable links, navigation updates, and footer
- [ ] **Phase 3: Content Polish** - Syntax-highlighted code blocks and anchor links on headings

## Phase Details

### Phase 1: Color Foundation
**Goal**: The site's entire color palette is replaced -- from purple-tinted dark to neutral dark grey -- with no visual artifacts, no specificity conflicts, and accessible contrast everywhere
**Depends on**: Nothing (first phase)
**Requirements**: TCH-01, TCH-02, CLR-01, CLR-02, CLR-03, CLR-04, CLR-05
**Success Criteria** (what must be TRUE):
  1. Site background is dark grey (~#1e1f22), not purple (#262335), on every page
  2. All surface colors (header, cards, hover states, borders) are neutral greys -- no purple-tinted remnants
  3. Page loads show no color flash (critical CSS in head.html matches the stylesheet)
  4. Every text/background combination passes WCAG AA contrast (4.5:1 minimum)
  5. Scrollbar, text selection, and accent elements use the updated palette
**Plans**: TBD

Plans:
- [ ] 01-01: TBD
- [ ] 01-02: TBD

### Phase 2: Typography & Layout
**Goal**: Text is readable with clear visual hierarchy, navigation is complete with all intended links, and the site has a proper footer
**Depends on**: Phase 1
**Requirements**: TYP-01, TYP-02, TYP-03, NAV-01, NAV-02, NAV-03, NAV-04
**Success Criteria** (what must be TRUE):
  1. Headings h1, h2, h3 are visually distinct from each other (clear size separation)
  2. Font sizes scale smoothly between mobile and desktop (no jarring breakpoint jumps)
  3. Links within blog post body text are visually distinguishable without hovering
  4. GitHub icon/link appears in the header alongside LinkedIn
  5. Homepage shows post cards directly without a "Posts" section title above them
  6. Every page has a footer with copyright and social links
  7. Post card hover effect lifts vertically (not horizontal slide)
**Plans**: TBD

Plans:
- [ ] 02-01: TBD
- [ ] 02-02: TBD

### Phase 3: Content Polish
**Goal**: Code blocks in posts have proper syntax coloring and readers can link directly to any heading
**Depends on**: Phase 2
**Requirements**: CNT-01, CNT-02
**Success Criteria** (what must be TRUE):
  1. Code blocks in posts display language-specific syntax highlighting with colors that fit the dark theme
  2. Hovering over a post heading reveals a clickable anchor link icon for sharing that section's URL
**Plans**: TBD

Plans:
- [ ] 03-01: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Color Foundation | 0/? | Not started | - |
| 2. Typography & Layout | 0/? | Not started | - |
| 3. Content Polish | 0/? | Not started | - |
