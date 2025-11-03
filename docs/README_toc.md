# TOC Links Guide

Short steps to add a Table of Contents with working anchors (Hexo + markdown-it).

1. Pick headings (h3 or deeper) you want listed.
2. Write headings normally: `### Version & Host` etc; add layout classes `{.cols-2}` if needed.
3. Predict slug: lowercase; spaces -> dashes; remove `&`: "Help & Aliases" => `help-aliases`.
4. Build list: `- [Help & Aliases](#help-aliases)`.
5. Optional IDs for lint: `### Help & Aliases {#help-aliases .cols-3}`.
6. Keep classes and ID in one `{}` block.
7. Avoid `<a id>` (triggers MD033); prefer `{#id}`.
8. Use short, kebab-case fragments.
9. Verify by clicking links after `npm run build`.
10. Suppress fragment lint globally via `.markdownlint.json` `MD051: false` if needed.
11. Don't add duplicate permalink `#`; theme injects it.
12. Rebuild and manually test scrolling.
