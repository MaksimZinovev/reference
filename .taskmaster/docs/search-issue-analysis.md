# Search Functionality Issue Analysis

## Problem Summary

The search functionality works correctly on the main branch but fails on the feature/github-pages-setup branch with 404 errors when trying to fetch search.json.

## Root Causes Identified

### 1. URL Path Mismatch (Primary Issue)

**Description:** The JavaScript search implementation constructs the search path incorrectly when the site is configured with a subdirectory root.

**Technical Details:**

- Main branch configuration: `url: https://cheatsheets.zip`, `root: /`
- Feature branch configuration: `url: https://maksimzinovev.github.io/reference`, `root: /reference/`
- JavaScript code in main.js constructs path as: `${location.protocol}//${location.host}/search.json?v=1.0`
- When root is `/reference/`, the search.json file is generated at `/reference/search.json`, not at root `/search.json`

**Evidence:**

- Search request fails with 404 when looking for `/search.json`
- File is actually generated and accessible at the correct subdirectory path
- This mismatch causes the JavaScript to fetch HTML error pages instead of JSON data, leading to JSON parsing errors

### 2. Hardcoded URL References (Secondary Issue)

**Description:** Some template files contain hardcoded references to the production domain that haven't been updated for the GitHub Pages configuration.

**Technical Details:**

- EJS templates (hero.ejs, footer.ejs) contain hardcoded "cheatsheets.zip" references
- Global.js script generates list.md with hardcoded production URLs
- Though task 4 shows as "done", these references may still exist in some locations

**Evidence:**

- Mixed domain references in generated content
- Potential for inconsistent linking behavior

## Solution Recommendations

1. **Update JavaScript Search Path Construction:**
   Modify the search path construction in main.js to respect the site's root configuration:

   ```javascript
   // Current (incorrect):
   dbPath: `${location.protocol}//${location.host}/search.json?v=1.0`;

   // Should be:
   dbPath: `${location.protocol}//${location.host}${hexoConfig.root}search.json?v=1.0`;
   ```

2. **Verify All Hardcoded References Are Removed:**
   - Search all EJS templates for remaining "cheatsheets.zip" references
   - Update global.js to use dynamic URL configuration
   - Ensure all asset paths use Hexo helpers (url_for, css, js)

3. **Test with Correct Configuration:**
   - Rebuild with proper root configuration
   - Verify search.json is accessible at the expected path
   - Test search functionality in development mode
