---
published: false
---

You can’t clone directly into a **non-empty directory**, but there are a few clean workarounds 👇

### ✅ Option 1 – Clone into a new subfolder

```bash
cd /Users/maksim/.config
git clone https://github.com/MaksimZinovev/superpowers-marketplace.git
```

→ creates `/Users/maksim/.config/superpowers-marketplace`.

---

### ✅ Option 2 – Clone into a temp dir, then move contents

```bash
git clone https://github.com/MaksimZinovev/superpowers-marketplace.git /tmp/superpowers-marketplace
cp -R /tmp/superpowers-marketplace/. /Users/maksim/.config/
rm -rf /tmp/superpowers-marketplace
```

---

### ✅ Option 3 – Initialize existing folder as repo

If `/Users/maksim/.config` should **be the repo root**:

```bash
cd /Users/maksim/.config
git init
git remote add origin https://github.com/MaksimZinovev/superpowers-marketplace.git
git fetch
git checkout -t origin/main
```

Use Option 3 if you want `.config` itself under Git version control.
