# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Architecture Overview

This is **Reference** - a comprehensive cheat sheet website built with **Hexo** (static site generator) and **Tailwind CSS**. The project provides quick reference guides for developers across programming languages, tools, Linux commands, databases, and keyboard shortcuts.

### Key Technologies

- **Hexo** (v7.3.0) - Static site generator for markdown-based content
- **Tailwind CSS** (v3.4.14) - Utility-first CSS framework for styling
- **PostCSS** - CSS processing with Tailwind
- **Gulp** (v5.0.1) - Build automation and asset optimization

### Project Structure

```
.
├── source/
│   ├── _posts/          # All cheat sheet markdown files
│   │   ├── bash.md      # => https://cheatsheets.zip/bash
│   │   └── ...          # All individual cheat sheets
│   ├── assets/          # Static assets (images, icons)
│   └── widget/          # Widget-related files
├── themes/
│   └── coo/             # Custom theme
│       ├── source/      # Theme source files (CSS, JS)
│       ├── layout/      # Template layouts (EJS)
│       └── _config.yml  # Theme configuration
├── docs/                # Contributor guides (TOC, icons, etc.)
├── _config.yml          # Hexo main configuration
├── _config.github.yml   # GitHub Pages deployment config
├── gulpfile.js          # Build automation tasks
└── tailwind.config.js   # Tailwind CSS configuration
```

## Development Commands

```bash
# Install dependencies
npm install

# Start development server with live reload
npm run dev
# Serves at http://127.0.0.1:4000

# Production build
npm run build

# Code quality
npm run format          # Format with Prettier
npm run lint            # Lint and fix JS
npm test                # Run all checks

# Commit with conventional commits
npm commit
```

## Cheat Sheet Format

All cheat sheets are markdown files in `source/_posts/` with specific front matter:

```markdown
---
title: Bash
date: 2020-11-25 18:28:43
background: bg-[#3e4548]
tags: [shell, sh, echo, script, linux]
categories: [Programming, Operating System]
intro: This is a quick reference cheat sheet to getting started with linux bash shell scripting.
plugins: [copyCode]
---
```

**Required fields:** `title`, `intro`
**Important fields:** `background` (Tailwind color classes), `categories` (determines site navigation)

### Markdown Content Rules

- **Top-level heading is H2 (`##`), not H1** - This is project-specific markdown convention
- Code blocks use ` ```language ` syntax
- Tables use standard markdown format
- Navigation structure controlled by `categories` in front matter

## Adding New Cheat Sheets

1. Create `source/_posts/{slug}.md` (follow existing patterns)
2. Add SVG icon to `source/assets/icon/{slug}.svg` (10x10 viewBox recommended)
3. Verify category exists in `themes/coo/_config.yml` under `index_categories`
4. Test locally with `npm run dev`

See contributor guides in `docs/`:

- `docs/README_toc.md` - Table of Contents with anchor fragments
- `docs/README_icon.md` - SVG icon requirements

## Build Process

### CSS Processing

- **Source**: `themes/coo/source/css/style.tailwindcss`
- **Output**: `themes/coo/source/css/style.css`
- **Watch mode**: Active during `npm run dev`

### Asset Optimization (Gulp)

- HTML: Minification and cleaning
- CSS/JS: Minification with Terser
- Cache busting: Version numbering for assets

### Static Site Generation (Hexo)

- **Input**: Markdown files in `source/_posts/`
- **Templates**: EJS templates in `themes/coo/layout/`
- **Output**: Static HTML in `public/` directory

## Commit Convention

This project uses **conventional commits** with commitlint enforcement. Commits must follow:

```
type(scope): subject
```

### Valid Types

`feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `ci`, `build`, `revert`

### Valid Scopes

`search`, `theme`, `css`, `js`, `content`, `build`, `deps`, `config`, `docs`, `layout`, `widget`, `gulp`, `hexo`, `markdown`, `seo`, `a11y`, `perf`

**Example:** `feat(content): add playwright cheat sheet`

## Site Maintenance

When adding/removing posts:

1. **Update `themes/coo/_config.yml`**:
   - `index_recommends`: Ensure all slugs exist in `source/_posts/`
   - `index_categories`: Only keep categories used by posts

2. **Verification**:
   ```bash
   npx hexo generate  # Test for errors
   npx hexo server    # Preview before commit
   ```

## Excluding Draft Files

Draft files in `source/raw/` are excluded via `published: false` front matter. See `docs/README_exclude_raw.md` for details.

## Important Notes

- This is a content-heavy site with 90+ cheat sheets
- Build process can be memory-intensive (`--max-old-space-size=4096`)
- Deployment target: GitHub Pages (`_config.github.yml`)

## Project Memory

Topic-specific memories (loaded into context):

- @.claude/projects/-Users-maksim-repos-reference/memory/hexo-subdirectory-deployment.md
- @.claude/projects/-Users-maksim-repos-reference/memory/search-engine-troubleshooting.md
- @.claude/projects/-Users-maksim-repos-reference/memory/browser-testing-playwright.md

See @.claude/projects/-Users-maksim-repos-reference/memory/MEMORY.md for index.
