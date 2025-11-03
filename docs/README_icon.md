# Icon SVG Guide

Steps to add a post icon matching filename slug.

1. Filename `source/_posts/my-new-cheatsheet.md` => slug `my-new-cheatsheet`.
2. Create `source/assets/icon/my-new-cheatsheet.svg` (simple; 10x10 viewBox consistent).
3. Duplicate an existing SVG for style; tweak paths.
4. Do NOT set custom `slug:`; name SVG after the file slug.
5. Remove width/height; rely on viewBox; no fonts/scripts/styles.
6. Single `<svg>` element only.
7. Run `npm run build`; open page; confirm icon (not fallback rectangle).
8. If missing: check filename, case, SVG validity.
9. Commit markdown + SVG together.
10. Keep file small (<2KB); optimize if larger.
