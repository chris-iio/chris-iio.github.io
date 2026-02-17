---
phase: 03-content-polish
plan: 02
subsystem: ui
tags: [anchor-links, liquid-include, jekyll, hover-reveal, css-transitions]

requires:
  - phase: 02-typography-layout
    provides: Post content link styles (.post-content a) with underline and accent color
provides:
  - Anchor heading injection for h2-h4 via jekyll-anchor-headings v1.0.13 Liquid include
  - Hover-reveal CSS for heading anchors with accent color interaction
  - Override rules preventing post-content link styles from bleeding into anchor links
affects: []

tech-stack:
  added: [jekyll-anchor-headings v1.0.13 (Liquid include, not gem)]
  patterns: [Liquid content pipeline (content passed through include before rendering), CSS specificity override for scoped link exceptions]

key-files:
  created: [_includes/anchor_headings.html]
  modified: [_layouts/post.html, _sass/custom.scss]

key-decisions:
  - "Used jekyll-anchor-headings Liquid include (not a gem plugin) for GitHub Pages compatibility"
  - "Scoped anchors to h2-h4 only (h_min=2 h_max=4) -- h1 is the post title, h5/h6 rarely used"
  - "Override block (.post-content a.heading-anchor) prevents underline/accent bleed from general link styles"

patterns-established:
  - "Content pipeline: post HTML passed through Liquid includes for build-time transformation"
  - "CSS specificity override: more-specific selector to exempt elements from broad rules"

requirements-completed: [CNT-02]

duration: 2min
completed: 2026-02-17
---

# Phase 3 Plan 2: Anchor Headings Summary

**Hoverable anchor links on post headings (h2-h4) using jekyll-anchor-headings Liquid include with CSS hover-reveal fade**

## Performance

- **Duration:** 2 min
- **Started:** 2026-02-17T10:23:46Z
- **Completed:** 2026-02-17T10:25:40Z
- **Tasks:** 2
- **Files modified:** 3

## Accomplishments
- Integrated jekyll-anchor-headings v1.0.13 as a pure Liquid include (no gem, no JavaScript)
- Post headings h2-h4 now contain clickable '#' anchor links that update the URL fragment
- Anchor links hidden by default, fade in on heading hover, change to accent color on direct hover
- Override CSS ensures anchors are visually distinct from regular post links (no underline, muted color)

## Task Commits

Each task was committed atomically:

1. **Task 1: Add anchor headings include and update post layout** - `6fbe792` (feat)
2. **Task 2: Add anchor link hover-reveal CSS** - `7703f67` (feat)

## Files Created/Modified
- `_includes/anchor_headings.html` - jekyll-anchor-headings v1.0.13 Liquid include (174 lines)
- `_layouts/post.html` - Replaced `{{ content }}` with anchor_headings include call
- `_sass/custom.scss` - Added .heading-anchor hover-reveal rules and .post-content a.heading-anchor override

## Decisions Made
- Used jekyll-anchor-headings as a Liquid include file rather than a gem plugin -- GitHub Pages restricts gem plugins, but Liquid includes work without restriction
- Scoped to h2-h4 (h_min=2, h_max=4) because h1 is the post title rendered in the header section, not in content, and h5/h6 are too granular for section linking
- Added a dedicated `.post-content a.heading-anchor` override block to prevent the existing `.post-content a` underline/accent styles from applying to anchor links

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered
- Initial download URL (`/v1.0.13/anchor_headings.html`) returned 404; the file is located at `_includes/anchor_headings.html` within the repository. Resolved by querying the GitHub API to discover the correct path and downloading from the correct URL.
- Ruby/Bundler not available in execution environment, so `bundle exec jekyll build` could not be run. Verification performed by inspecting file contents directly: confirmed include parameters, CSS selectors, and specificity override. Full build verification will occur on GitHub Pages deployment.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness
- All Phase 3 plans (03-01 syntax highlighting, 03-02 anchor headings) are complete
- The site is ready for content creation and deployment
- No blockers or concerns remaining

## Self-Check: PASSED

All created files verified on disk. Commits `6fbe792` and `7703f67` confirmed in git log.

---
*Phase: 03-content-polish*
*Completed: 2026-02-17*
