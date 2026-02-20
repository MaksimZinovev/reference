# Search Engine Troubleshooting - Reference Site

## Component: SearchEngine (main.js)

### Architecture

```
SearchEngine()
├── dbPath: points to search.json
├── basePath: root path for URL construction
├── fetch(): loads search.json
├── render(): builds result HTML
└── renderPreview(): builds preview panel
```

### Critical Path: fuse.js Loading

```javascript
// WRONG ORDER - path updated after library loads
Utils.externalLibrary(Utils.LIBRARY.fuse) // Uses hardcoded path
  .then(() => new SearchEngine()); // Path update TOO LATE

// CORRECT ORDER - update path BEFORE loading
const basePath = getRootPath();
Utils.LIBRARY.fuse.lib = [`${basePath}js/fuse_7.1.0.js`];
Utils.externalLibrary(Utils.LIBRARY.fuse);
```

### URL Rendering Bug Pattern

```javascript
// WRONG - direct path without root
href="${hit.path}"  => /page.html (404 on subdirectory deploy)

// CORRECT - with basePath prefix
const urlPath = hit.path.startsWith('/') ? hit.path.slice(1) : hit.path;
href="${this.basePath}${urlPath}"  => /reference/page.html
```

### Locations Using hit.path

1. Line ~872: Main result link `<a href>`
2. Line ~909: Preview panel H3 anchors
3. Line ~911: Preview panel H2 anchors

### Debug Commands

```javascript
// Check fuse loaded
console.log(typeof Fuse);

// Check basePath
console.log(window.search.basePath);

// Check search data
console.log(window.search.lastSearch.resp);
```

## 404 vs URL Issues

| Error Type        | Cause                | Fix Location                    |
| ----------------- | -------------------- | ------------------------------- |
| fuse.js 404       | Wrong lib path       | Update before externalLibrary() |
| Page 404 on click | Missing root in href | Add basePath prefix             |
| Double slash      | Both have leading /  | Strip leading / from hit.path   |
