Update any GitHub fork so it matches the latest version from the original repo

---

### 🧩 One-time setup (do this once per fork)

```bash
# 1. Go to your fork's local directory
cd ~/repos/superpowers-skills

# 2. Check existing remotes (you’ll see "origin")
git remote -v

# 3. Add the original repo as “upstream”
git remote add upstream https://github.com/original-author/superpowers-skills.git

# 4. Verify it’s added
git remote -v
```

---

### 🔄 Regular update steps

```bash
# 1. Fetch latest changes from original repo
git fetch upstream

# 2. Switch to your main branch
git checkout main

# 3. Merge updates into your fork
git merge upstream/main

# 4. Push changes to your fork on GitHub
git push origin main
```

---

### ⚙️ Optional – safer method using rebase

```bash
git fetch upstream
git rebase upstream/main
git push origin main --force
```

_(Use this only if you know you didn’t change history locally.)_
