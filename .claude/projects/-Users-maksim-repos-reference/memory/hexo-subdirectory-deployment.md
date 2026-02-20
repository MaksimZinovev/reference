# Hexo Subdirectory Deployment - URL Path Handling

## Problem Context

When deploying Hexo sites to a subdirectory (e.g., GitHub Pages with `root: /reference/`), URLs generated for JavaScript must include the root prefix.

## Key Configuration Files

```
_config.yml           # Main config (root: '/')
_config.github.yml    # Override for deployment (root: '/reference/')
```

### Build with merged config:

```bash
hexo generate --config _config.yml,_config.github.yml
```

## URL Generation in Hexo

| Template Helper | Output (root=/reference/)     |
| --------------- | ----------------------------- |
| `url_for(path)` | `/reference/path`             |
| `post.path`     | `/post-slug.html` (NO prefix) |

### Common Bug Pattern

Double `url_for` calls add prefix twice:

```ejs
<!-- WRONG -->
url: url_for(post.path)  => /reference//reference/post.html

<!-- CORRECT -->
url: post.path           => /reference/post.html (url_for in parent)
```

## JavaScript Path Access

JavaScript cannot directly access Hexo config. Solutions:

1. **Meta tag bridge** (recommended):

```html
<meta name="root" content="<%- config.root || '/' %>" />
```

2. **Read in JS**:

```javascript
const root = document
  .querySelector('meta[name="root"]')
  ?.getAttribute('content');
```

3. **Store as instance property** for use in class methods:

```javascript
function SearchEngine() {
  this.basePath = root;
  // Now accessible as this.basePath in all methods
}
```

## Files That Need Root Path Fixes

- `themes/coo/layout/index.ejs` - Card URLs
- `themes/coo/layout/_partial/head.ejs` - Meta tags
- `themes/coo/source/js/main.js` - Search URLs, fuse.js path
