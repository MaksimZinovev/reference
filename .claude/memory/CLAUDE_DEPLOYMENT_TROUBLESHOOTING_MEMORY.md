# Session Learnings and Self-Improvement Instructions

## Key Technical Learnings

- **GitHub Pages**: `package-lock.json` must be committed for `npm ci` to work
- **URL Path Issues**: Double `/reference` paths in navigation links vs direct URLs
- **Task Management**: Use task-master for systematic investigation subtasks
- **MCP Integration**: Playwright MCP for browser-based issue verification

## Critical Commands

```bash
# GitHub Pages deployment fixes
sed -i '' '/package-lock.json/d' .gitignore
git add package-lock.json
gh run view <run-id>

# GitHub CLI commands used in session
gh repo view --json name,owner,homepage,isTemplate,hasPages,hasProjects,hasWiki
gh run list --limit=5

# Package.json commands used in session
npm run build
npm test
npm run format

# Task management workflow
task-master list
task-master add-subtask --parent=8 --title="Investigate 404" --description="Findings"
task-master set-status --id=8.4 --status=done

# URL path verification
curl -I https://maksimzinovev.github.io/reference/dspy-py-cheatsheet.html
grep -r "reference/reference" public/

# Playwright investigation
mcp__playwright__browser_navigate --url="https://siteurl/"
mcp__playwright__browser_snapshot
```

## Critical Instructions

### When Deploying to GitHub Pages:

1. **ALWAYS** verify `.gitignore` doesn't block `package-lock.json`
2. **ALWAYS** configure Pages to use "GitHub Actions" as source
3. **ALWAYS** test both direct URLs and navigation links

### When Investigating 404 Errors:

1. **FIRST** test direct URL access vs navigation clicks
2. **THEN** check for double paths in generated HTML
3. **NEXT** examine Hexo template link generation

### When Using Task-Master:

1. **START** with `task-master list`
2. **IMMEDIATELY** create subtasks for findings
3. **UPDATE** status as you work through subtasks
