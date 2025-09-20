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

## When you encounter any issues with MCP

`$HOME=/Users/maksim`

- Make sure to check your memory in `$HOME/.claude/CLAUDE.md` to troubleshoot the issue
- Always output one-line summary explaining the result of checking `$HOME/.claude/CLAUDE.md` to troubleshoot the {issue}:
  - when found relevant memory, start with "checked, found notes in memory related to {issue}: ... "
  - when did not find relevant memory, "checked, no notes in memory related to {issue}: ... "
  - or explain what happened when you tried to read ``$HOME/.claude/CLAUDE.md` to troubleshoot the issue `
- Next output one-line reasoning and your next planned step.

## Task Master AI CLI - Most Frequently Used Commands

### Installation & Setup Commands

```bash
# Quick installation check and install if needed
task-master --version 2>/dev/null || npm install -g task-master-ai

# Initialize project in current directory
task-master init

# Quick initialization with default confirmations
task-master init -y

# Check AI provider setup status
task-master models --status

# Interactive AI model configuration setup
task-master models --setup
```

### Daily Development Workflow Commands

```bash
# List all tasks with status and priorities
task-master list

# Show next task to work on (considers dependencies and priorities)
task-master next

# Show detailed information for a specific task
task-master show <task-id>

# Mark task as complete/done
task-master set-status --id=<task-id> --status=done

# Add a new task manually
task-master add-task "Task description here"
```

### Project Requirements & Task Management Commands

```bash
# Parse PRD file and generate tasks
task-master parse-prd requirements.txt

# Parse with specific number of tasks
task-master parse-prd prd.txt -n 8

# Analyze project complexity
task-master analyze-complexity

# Research best practices and update tasks
task-master research "JWT security best practices 2024" --id=5

# Create branch-based task tags
task-master add-tag --from-branch --description="Feature description"
```

### MCP Integration Commands (for use within Claude Code)

```bash
# Natural language task management via MCP
/project:tm/add-task create user authentication system
/project:tm/list show blocked tasks
/project:tm/show 45
/project:tm/update mark all API tasks as high priority

# Project initialization via MCP
/project:tm/init/quick
/project:tm/models/setup
/project:tm/parse-prd requirements.md
```

### Proactive Usage Guidelines

- **When users mention project planning**: Run `task-master init` if not initialized, then suggest `task-master parse-prd`
- **When users discuss task priorities**: Run `task-master list` to show current task state and priorities
- **When users ask "what's next"**: Run `task-master next` to get the next recommended task
- **When users complete features**: Run `task-master set-status --status=done` and then `task-master next` for next work item
- **When starting new features**: Run `task-master add-tag --from-branch` to associate tasks with the current feature branch
- **When researching solutions**: Use `task-master research` with relevant topics to inform task updates and implementation approaches
- **When you encounter error running task-master command**: Use @context7 or @github mcp to pull most recent documentation for `github.com/eyaltoledano/claude-task-master` and find solution
- **When you successfully resolved encounter error running task-master command**: Notify user and add concise instructions (1-2 lines with optional reference) in Claude Code memory file (scope: project) to avoid this error/issue in the future

## Task Master Integration - Active Task Management

**CRITICAL**: Always use task-master for task tracking and planning workflow:

1. **Start every session** with `task-master list` to check current task status and dependencies
2. **Update task status immediately** when starting/completing work using `task-master set-status --id=<id> --status=<status>`
3. **Read all tasks** when starting/completing task to ensure you understand what changes were done previously, what will be next steps and ensure your align them with overall plan and user's expectations. Make sure to raise questions if you noticed potential issues, inconsistencies or ambiguity
4. **End every session** by updating task status and running `task-master next` to determine next work item
5. **Notify user immediately** about task inconsistencies, ambiguities, or missing critical details
6. **Always ask for clarification** when task descriptions are unclear or dependencies seem wrong
7. **Commit changes after each task completion** - first highlight potential issues (mandatory) or improvements (optional), wait for feedback from user, commit changes if permission is granted

**Goal**: Maintain accurate task tracking and prevent status drift between actual work and task-master records.

## Task Master Integration - Previous Context Management

1. **Always ask for clarification** when task descriptions are unclear or dependencies seem wrong
2. **When task is done**, always review wording and update it to match actual implementation
3. **When task is done and updated**, always review dependant and downstream tasks to ensure they are aligned with completed task and consistent.
4. **When task is done and updated**, always notify user if updates are needed for dependant, downstream task and confirm with user before making changes.

**Goal**: Maintain concise, accurate and helpful information in tasks.json to reflect

1. task-related decisions and instructions from user
2. concise 1-line reasoning (why)
3. changes in current task and downstream tasks when they are impacted

## ## Task Master Integration - Valid Task Status Values

- "pending": Ready to work on
- "in-progress": Currently being worked on
- "done": Completed and verified
- "review": Awaiting review
- "deferred": Postponed
- "cancelled": No longer needed ← This is what we need!

## Session Memory

**Reference deployment troubleshooting**: `.claude/CLAUDE_DEPLOYMENT_TROUBLESHOOTING_MEMORY.md`

## MCP Integration Commands

**Use these without asking** (already allowed in settings.local.json):

- `task-master list`, `task-master show <id>`, `task-master next`
- `task-master set-status` for status updates
- `task-master add-task` for new tasks
- All other task-master commands related to project management
