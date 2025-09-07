# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Architecture Overview

This is **Reference** - a comprehensive cheat sheet website built with **Hexo** (static site generator) and **Tailwind CSS**. The project provides quick reference guides for developers across programming languages, tools, Linux commands, databases, and keyboard shortcuts.

### Key Technologies

- **Hexo** (v7.3.0) - Static site generator for markdown-based content
- **Tailwind CSS** (v3.4.14) - Utility-first CSS framework for styling
- **PostCSS** - CSS processing with Tailwind
- **Gulp** (v5.0.1) - Build automation and asset optimization
- **Node.js** - Runtime environment

### Project Structure

```
.
├── source/
│   ├── _posts/          # All cheat sheet markdown files
│   │   ├── bash.md      # => https://cheatsheets.zip/bash
│   │   ├── vim.md       # => https://cheatsheets.zip/vim
│   │   └── ...          # All individual cheat sheets
│   ├── assets/          # Static assets (images, etc.)
│   └── widget/          # Widget-related files
├── themes/
│   └── coo/            # Custom theme for the site
│       ├── source/     # Theme source files (CSS, JS)
│       ├── layout/     # Template layouts (EJS)
│       └── _config.yml # Theme configuration
├── _config.yml         # Hexo main configuration
├── gulpfile.js         # Build automation tasks
├── package.json        # Dependencies and scripts
└── tailwind.config.js  # Tailwind CSS configuration
```

### Cheat Sheet Format

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
**Important fields:** `background` (Tailwind color classes), `categories` (determines site navigation grouping)

## Development Commands

### Primary Development Workflow

```bash
# Install dependencies
npm install

# Start development server with live reload
npm run dev
# This runs:
# - dev:hexo  - Start Hexo server at http://127.0.0.1:4000
# - dev:css   - Watch and compile Tailwind CSS

# Production build
npm run build
# This runs:
# - build:clean    - Clean Hexo cache
# - build:rm-db.json - Remove db.json file
# - build:css      - Compile Tailwind CSS
# - build:generate - Generate static site
# - build:gulp     - Optimize assets (minification, etc.)
```

### Code Quality & Formatting

```bash
# Format all files with Prettier
npm run format
# Check formatting without changing files
npm run format:check

# Lint JavaScript files
npm run lint          # Auto-fix issues
npm run lint:check     # Check only

# Lint CSS files
npm run lint:css       # Auto-fix CSS issues

# Run all checks (linting + formatting)
npm test
```

### Security & Dependencies

```bash
# Security audit
npm run audit                    # Check for vulnerabilities
npm run audit:fix               # Auto-fix vulnerabilities
npm run security:check         # Comprehensive security check

# Dependency management
npm run deps:check              # Check for outdated packages
npm run deps:update            # Update all dependencies
```

### Git & Commit workflow

```bash
# Make a commit with conventional commits
npm commit
# This uses commitizen for structured commits
```

## Build Process Details

### CSS Processing

- **Source**: `themes/coo/source/css/style.tailwindcss`
- **Processing**: PostCSS with Tailwind CSS and Autoprefixer
- **Output**: `themes/coo/source/css/style.css`
- **Watch mode**: Active during development (`npm run dev`)

### Asset Optimization (Gulp)

Gulp pipeline processes:

- HTML: Minification and cleaning
- CSS: Minification with clean-css
- JavaScript: Terser minification and obfuscation
- Cache busting: Version numbering for assets
- File size reporting

### Static Site Generation (Hexo)

- **Input**: Markdown files in `source/_posts/`
- **Templates**: EJS templates in `themes/coo/layout/`
- **Output**: Static HTML in `public/` directory
- **Features**: SEO, sitemap generation, related posts, analytics

### Content Organization

Cheat sheets are organized by categories defined in `themes/coo/_config.yml`:

- Programming
- Toolkit
- Operating System
- Linux Command
- Python
- Database
- Keyboard Shortcuts
- Other

## Content Development

### Adding New Cheat Sheets

1. Create new markdown file in `source/_posts/`
2. Add proper front matter (follow existing patterns)
3. Use consistent formatting patterns from existing cheat sheets
4. Include `copyCode` plugin for code blocks
5. Test locally with `npm run dev`

### Style Guidelines

- Use Tailwind CSS classes for styling within content
- Follow existing color patterns (see `background` field in front matter)
- Code blocks use ` ```language ` syntax
- Tables use standard markdown format
- Navigation structure controlled by `categories` in front matter

### Site Configuration

Main configuration in `_config.yml`:

- SEO settings and metadata
- URL structure and permalinks
- Plugin configurations
- Build and deployment settings

Theme-specific settings in `themes/coo/_config.yml`:

- Category display order
- Featured/recommended posts
- Related posts configuration

## Working with the Build System

### Common Development Tasks

- **Adding a new cheat sheet**: Create `source/_posts/your-topic.md`
- **Updating styles**: Edit `themes/coo/source/css/style.tailwindcss`
- **Modifying layouts**: Edit EJS files in `themes/coo/layout/`
- **Updating navigation**: Modify categories in theme config
- **Changing site metadata**: Edit `_config.yml`

### Build Output

- **Development**: Served at `http://127.0.0.1:4000` with live reload
- **Production**: Static files generated in `public/` directory
- **Optimization**: All assets are minified and versioned for production
- **Deployment**: Ready for static hosting (Netlify, Vercel, GitHub Pages, etc.)

## Important Notes

- This is a content-heavy site with 90+ cheat sheets
- The build process can be memory-intensive (Gulp uses `--max-old-space-size=4096`)
- All content is markdown-based for easy editing and contribution
- The site uses custom EJS templates with Tailwind CSS styling
- SEO and accessibility are built into the template system
