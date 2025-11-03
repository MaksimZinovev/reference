---
published: false
---

## How can I clone repo but only specific folders to my local machine? <https://github.com/dharmx/walls.git>

Yes, you can clone only specific folders from a Git repository! Git has a feature called "sparse checkout" that allows you to work with only a subset of the repository's files and folders.

Here's how to do it:

1. **Clone the repository without checking out files:**

```bash
   git clone --no-checkout https://github.com/dharmx/walls.git
   cd walls
```

2. **Enable sparse checkout:**

```bash
   git sparse-checkout init --cone
```

3. **Specify which folders you want:**

```bash
   git sparse-checkout set folder1 folder2 folder3
```

4. **Check out the files:**

```bash
   git checkout
```

**Alternative one-liner approach:**

```bash
git clone --filter=blob:none --sparse https://github.com/dharmx/walls.git
cd walls
git sparse-checkout set folder1 folder2
```

The `--filter=blob:none` option creates a "partial clone" that initially downloads only the Git metadata, making the initial clone faster for large repositories.

**To see what folders are available in the repository first:**

```bash
git clone --depth=1 https://github.com/dharmx/walls.git temp-repo
ls temp-repo
rm -rf temp-repo
```

Would you like me to help you clone this repository with specific folders? If so, let me know which folders you're interested in, or I can first check what folders are available in the repository.
