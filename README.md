# My Blog

A personal blog built with Jekyll and hosted on GitHub Pages.

## Getting Started

### 1. Customize Your Blog

**Important:** Update your personal information and settings!

See [CUSTOMIZATION_GUIDE.md](CUSTOMIZATION_GUIDE.md) for detailed instructions on:
- Updating your name, bio, and contact information
- Configuring social media links
- Customizing the theme and colors
- Adding new pages and content

### 2. Local Development

To preview your blog locally:

```bash
# Install dependencies (first time only)
bundle install

# Start the local server
bundle exec jekyll serve

# Visit http://localhost:4000 in your browser
```

### 3. Deploy to GitHub Pages

1. Create a new repository on GitHub
2. Push your code:
   ```bash
   git remote add origin https://github.com/username/repository-name.git
   git branch -M main
   git push -u origin main
   ```
3. Enable GitHub Pages in repository settings
4. Your blog will be live at `https://username.github.io/repository-name/`

## Writing Posts

Create new posts in the `_posts` directory with the naming format: `YYYY-MM-DD-title.md`

Each post should include front matter:

```markdown
---
layout: post
title: "Your Post Title"
date: YYYY-MM-DD
categories: technology tutorials
tags: jekyll blogging
---

Your content here...
```

## Project Structure

```
my-blog/
├── _config.yml          # Main configuration file
├── _posts/              # Blog posts go here
├── _site/               # Generated site (don't edit)
├── assets/              # CSS, images, and other assets
├── about.md             # About page
├── index.md             # Home page
└── CUSTOMIZATION_GUIDE.md  # Detailed customization instructions
```

## Resources

- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [Minima Theme](https://github.com/jekyll/minima)
- [GitHub Pages Guide](https://docs.github.com/en/pages)
- [Markdown Guide](https://www.markdownguide.org/)
