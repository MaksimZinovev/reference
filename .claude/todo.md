# Deploy Hexo/Tailwind cheatsheets to **GitHub Pages**:

1. Clean + prepare repo

- Ensure the Hexo project is at repo root.
- Add a solid `.gitignore` (`node_modules`, `.cache`, `.hexo`, `public`, `db.json`, etc.).
- Verify `npm run build` produces `public/` locally.

2. Pick your Pages type & set URLs

- **Project site** (recommended): `https://<user>.github.io/<repo>/`
  - In `_config.yml` set:

    ```yml
    url: https://<user>.github.io/<repo>
    root: /<repo>/
    relative_link: false
    ```

- **User/Org site** (`<user>.github.io`):

  ```yml
  url: https://<user>.github.io
  root: /
  relative_link: false
  ```

3. (Optional) Custom domain (e.g., `cheatsheets.zip`)

- Add a file `source/CNAME` with exactly:

  ```
  cheatsheets.zip
  ```

- In GitHub → Settings → Pages, set **Custom domain** to `cheatsheets.zip` (and set DNS `A`/`ALIAS`/`CNAME` records at your DNS host).
- For custom domain, `_config.yml` should be:

  ```yml
  url: https://cheatsheets.zip
  root: /
  ```

4. Ensure theme links are path-safe

- In EJS layouts, wrap asset links with Hexo helpers (they usually are):
  `url_for('/css/style.css')`, `css('/css/style.css')`, `js('/js/app.js')`.
- This prevents broken paths when `root` ≠ `/`.

5. Add a Pages workflow
   Create `.github/workflows/pages.yml`:

```yaml
name: Deploy GitHub Pages
on:
  push:
    branches: [main]
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: 'pages'
  cancel-in-progress: false
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

6. Push + enable Pages

- Commit and push to `main`.
- GitHub → Settings → Pages → **Build and deployment**: set **Source = GitHub Actions**.

7. Verify build inputs

- Build uses your scripts: `build:clean`, `build:css`, `build:generate`, `build:gulp`.
- If memory-heavy, you already handle Node/Gulp flags; CI has \~7 GB RAM which is usually fine.

8. Check URLs after first deploy

- Visit the environment URL shown in the workflow run.
- For **project site**, confirm pages, images, and CSS load under `/<repo>/...`.

9. Fix 404s quickly (if any)

- If assets 404 on project site, double-check `_config.yml` `root`, and that theme uses `url_for`/`css`/`js` helpers.
- Re-run `npm run build` locally to replicate.

10. Lock in quality gates

- Keep `npm test`, `lint`, `format` as separate workflow(s) on PRs.
- Optionally add a second job that runs `npm audit --omit=dev` and `npm run deps:check` on a schedule.

That’s it—merge to `main` and your cheatsheets will auto-deploy on every push.
