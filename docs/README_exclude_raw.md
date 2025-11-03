# Excluding `source/raw` Markdown

To keep scratch/draft files out of the generated Hexo site:

1. Add `published: false` front matter to each file in `source/raw/*.md` (already done).
2. Optionally prevent rendering but keep copies by adding to `_config.yml`:

   ```yaml
   skip_render:
     - raw/**/*.md
   ```

3. For full exclusion (not copied at all) create a script in `scripts/`:

   ```js
   hexo.extend.filter.register('before_generate', () => {
     const Page = hexo.model('Page');
     Page.forEach((p) => p.source.startsWith('raw/') && Page.remove(p._id));
   });
   ```

4. Regenerate: `npx hexo clean && npx hexo generate`.

Choose (1) + (2) for simplest; add (3) if you need zero footprint.
