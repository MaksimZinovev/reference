# Project Memory

## Topic References

| Topic                            | File                                                                   |
| -------------------------------- | ---------------------------------------------------------------------- |
| Hexo subdirectory deployment     | [hexo-subdirectory-deployment.md](./hexo-subdirectory-deployment.md)   |
| Search engine troubleshooting    | [search-engine-troubleshooting.md](./search-engine-troubleshooting.md) |
| Browser testing (playwright-cli) | [browser-testing-playwright.md](./browser-testing-playwright.md)       |

## Project Structure

```
source/_posts/           # Cheat sheet markdown files
themes/coo/
├── layout/              # EJS templates
│   ├── index.ejs        # Homepage
│   └── _partial/        # Partials (head.ejs, etc.)
└── source/js/main.js    # Search engine, fuse.js integration
```

## Build Commands

```bash
npm run dev              # Development server (port 4000)
npm run build            # Production build with optimization
```

## Lessons Learned

### 2026-02-20: Always Test With Evidence

**Rule:** Never claim code is "verified" or "done" without running actual tests/builds as evidence.

**Wrong approach:**

- Trust subagent reports
- Only read files / `git diff`
- Claim completion based on file contents

**Right approach:**

1. Make edit
2. Run build/test command
3. Verify output passes
4. Then claim "done" with evidence

**Example:** Claimed fixes were "verified" based on `git diff` alone. Should have run `npm run build` first - that's the real proof.

## Agent Teams

### Pre-approve Permissions Before Spawning

- Teammates inherit lead's permissions at spawn time
- Add `Edit(//path/**)` to `allow` list in settings.json BEFORE spawning
- After spawning, permission changes don't help already-running teammates

### Mailbox Location

- Team config: `~/.claude/teams/{team-name}/config.json`
- Lead inbox: `~/.claude/teams/{team-name}/inboxes/team-lead.json`
- Permission requests appear as JSON with `request_id` field
