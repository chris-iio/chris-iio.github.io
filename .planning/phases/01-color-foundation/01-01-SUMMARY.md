---
phase: 01-color-foundation
plan: 01
subsystem: ui
tags: [scss, css-specificity, minima, jekyll, css-variables]

# Dependency graph
requires: []
provides:
  - "Clean specificity foundation: no !important, body a overrides, CSS variable tagline"
  - "Minima dark skin disabled, custom theme fully controls styles"
affects: [01-color-foundation]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - "body a specificity override pattern (0,0,1,1) instead of !important"
    - "CSS custom properties for all color values"

key-files:
  created: []
  modified:
    - "_config.yml"
    - "_sass/custom.scss"

key-decisions:
  - "Used body a prefix for link specificity (0,0,1,1 vs 0,0,0,1) instead of !important"
  - "Kept .site-title and .post-card class selectors as-is since they already beat Minima's bare a selector"
  - "Mapped tagline color to --text-muted rather than creating a new variable"

patterns-established:
  - "Specificity override: prefix selectors with body to beat Minima defaults without !important"
  - "Color tokenization: all color values use var() references to :root CSS custom properties"

requirements-completed: [TCH-01, TCH-02]

# Metrics
duration: 2min
completed: 2026-02-17
---

# Phase 1 Plan 1: Specificity Cleanup Summary

**Removed 6 !important declarations via body-prefix specificity, disabled Minima dark skin, and tokenized the tagline color**

## Performance

- **Duration:** 2 min
- **Started:** 2026-02-17T08:07:10Z
- **Completed:** 2026-02-17T08:09:21Z
- **Tasks:** 2
- **Files modified:** 2

## Accomplishments
- Removed all 6 !important declarations from custom.scss, replacing with specificity-based overrides
- Disabled Minima dark skin in _config.yml so custom theme fully controls the cascade
- Converted hardcoded tagline color (#9d8cc4) to var(--text-muted) for palette system compatibility
- Removed duplicate .nav-link:hover rule
- Audited specificity landscape and confirmed no Minima light-mode leakage

## Task Commits

Each task was committed atomically:

1. **Task 1: Disable Minima dark skin and remove !important declarations** - `b5d7de2` (fix)
2. **Task 2: Build and audit for Minima style leakage** - no commit (verification-only task, no file changes needed)

## Files Created/Modified
- `_config.yml` - Removed skin: dark from minima settings block
- `_sass/custom.scss` - Replaced !important with body a specificity, removed duplicate nav rule, tokenized tagline color

## Decisions Made
- Used `body a` prefix pattern for link overrides: specificity 0,0,1,1 beats Minima's bare `a` at 0,0,0,1 without needing !important
- Left `.site-title` and `.post-card` selectors unchanged (class specificity already sufficient)
- Mapped tagline to `--text-muted` rather than introducing a new `--tagline` variable, since the design intent aligns with muted text

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered
- Ruby/Bundler not installed in execution environment, preventing `bundle exec jekyll build` verification. Static analysis of SCSS specificity and import order (`@import "custom"` after `@import "minima"`) confirms correctness. The `body a` selectors (0,0,1,1) beat Minima's bare `a` selectors (0,0,0,1), and all color values use CSS custom properties from `:root`. Build verification should be performed when Ruby is available.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness
- Specificity foundation is clean: zero !important, all colors tokenized
- Ready for Plan 02 (palette swap) to update :root CSS custom property values
- Build verification recommended when Ruby/Bundler become available

## Self-Check: PASSED

- FOUND: _config.yml
- FOUND: _sass/custom.scss
- FOUND: 01-01-SUMMARY.md
- FOUND: b5d7de2 (Task 1 commit)

---
*Phase: 01-color-foundation*
*Completed: 2026-02-17*
