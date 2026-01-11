# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build Commands

```bash
# Install dependencies
bundle install

# Local development server (http://localhost:4000)
bundle exec jekyll serve

# Build static site to _site/
bundle exec jekyll build
```

## Architecture

This is a Jekyll static site blog with a custom dark terminal theme, deployed to GitHub Pages.

**Key directories:**
- `_posts/` - Blog posts in Markdown (format: `YYYY-MM-DD-title.md`)
- `_layouts/` - Page templates (default.html wraps all pages, home.html for index, post.html for articles)
- `_includes/` - Reusable components (header, sidebar with skills/certs, head for fonts)
- `assets/css/style.scss` - Main stylesheet with dark theme variables

**Content flow:** Markdown files → Liquid templates → Static HTML in `_site/`

**Site configuration:** `_config.yml` contains author info, certifications list, skills, and Jekyll settings. Update this file to modify sidebar content.

**Theme:** Dark terminal aesthetic using JetBrains Mono font, #1f1f1f background, #f97316 orange accents. CSS uses terminal-style pseudo-elements (`##`, `>`).
