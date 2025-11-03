---
published: false
---

> [!NOTE] NOTE
> Make sure source folder does not existist. Otherwise so `ln -s` creates a link _inside_ it → `themes/rio → /Users/maksim/repos/...`

To create a symlink in bash:

```bash
ln -s /Users/maksim/repos/iTerm2-Color-Schemes/rio /Users/maksim/.config/rio/themes
```

Explanation:

- `ln` → link command
- `-s` → create **symbolic** (soft) link
- First path → **target** (real folder)
- Second path → **link name** (what you’ll access)

✅ Verify:

```bash
ls -l /Users/maksim/.config/rio
```

You’ll see `themes -> /Users/maksim/repos/iTerm2-Color-Schemes/rio`.
