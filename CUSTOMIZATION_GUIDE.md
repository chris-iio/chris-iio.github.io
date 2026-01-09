# Blog Customization Guide

This guide will help you customize your Jekyll blog and make it your own!

## Quick Start: Update Your Personal Information

Edit `_config.yml` and replace the following placeholders:

### Required Updates

1. **Blog Title** (line 2):
   ```yaml
   title: Your Blog Title  # Change this to your actual blog title
   ```

2. **Author Information** (lines 8-11):
   ```yaml
   author:
     name: Your Name  # Your actual name
     email: your.email@example.com  # Your email address
     bio: "..."  # A short bio about yourself
   ```

3. **Social Media Links** (lines 14-25):
   ```yaml
   social:
     github: your-github-username  # Your GitHub username
     twitter: your-twitter-handle  # Your Twitter handle
     linkedin: your-linkedin-username  # Your LinkedIn username
   ```

4. **Site URL** (line 4):
   ```yaml
   url: "https://username.github.io"  # After deployment, add your site URL
   ```

5. **Timezone** (line 29):
   ```yaml
   timezone: America/New_York  # Change to your timezone
   ```

## Theme Customization

### Change Color Scheme

The Minima theme supports different color skins. Edit `_config.yml` line 21:

```yaml
minima:
  skin: auto  # Options: auto, classic, dark, solarized
```

### Custom CSS

Edit `assets/css/style.scss` to customize colors and styling:

```scss
/* Uncomment and modify these variables */
:root {
  --brand-color: #2a7ae2;  /* Your brand color */
  --brand-color-dark: #1756a9;  /* Darker shade for hovers */
  --text-color: #111;  /* Main text color */
  --background-color: #fdfdfd;  /* Page background */
}
```

## Adding Content

### Write a New Blog Post

1. Create a new file in `_posts/` with the format: `YYYY-MM-DD-post-title.md`
2. Add front matter at the top:

```markdown
---
layout: post
title: "Your Post Title"
date: 2026-01-09
categories: technology tutorials
tags: jekyll blogging
---

Your content here...
```

### Add a New Page

1. Create a new `.md` file in the root directory (e.g., `projects.md`)
2. Add front matter:

```markdown
---
layout: page
title: Projects
permalink: /projects/
---

Your content here...
```

3. Add it to the navigation menu in `_config.yml`:

```yaml
header_pages:
  - index.md
  - about.md
  - projects.md  # Add your new page here
```

## Advanced Features

### Enable Comments

Consider adding [Disqus](https://disqus.com/) or [utterances](https://utteranc.es/) for comments.

### Add Google Analytics

Add your tracking ID to `_config.yml`:

```yaml
google_analytics: UA-XXXXXXXXX-X
```

### Custom Domain

1. Add a `CNAME` file with your domain:
   ```
   yourdomain.com
   ```

2. Configure DNS with your domain provider
3. Update the `url` in `_config.yml`

## Testing Locally

To preview your blog locally before deploying:

```bash
# Install dependencies (first time only)
bundle install

# Run local server
bundle exec jekyll serve

# Visit http://localhost:4000 in your browser
```

## Deploying to GitHub Pages

After making changes:

```bash
git add .
git commit -m "Customize blog settings and styling"
git push origin main
```

Your changes will be live at your GitHub Pages URL within a few minutes!

## Need Help?

- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [Minima Theme GitHub](https://github.com/jekyll/minima)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
