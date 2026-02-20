# Browser Testing with playwright-cli

## Setup for Headed Mode

Create config file in project root:

```json
// playwright-cli.json
{
  "headless": false
}
```

## Essential Commands

```bash
# Fresh start (clear sessions)
playwright-cli session-stop-all && playwright-cli session-delete

# Open in headed mode
playwright-cli --headed open http://127.0.0.1:4000/reference/

# Core workflow
playwright-cli snapshot          # Get element refs
playwright-cli click e123        # Click by ref
playwright-cli type "text"       # Type into focused element
playwright-cli eval "JS code"    # Run JavaScript

# Debug
playwright-cli console           # Console messages
playwright-cli network           # Network requests
playwright-cli screenshot        # Visual evidence
```

## Testing Search Functionality

```bash
# Open search modal
playwright-cli eval "document.getElementById('mysearch-trigger')?.click()"

# Type search query
playwright-cli type "powershell"

# Check URLs in results
playwright-cli snapshot
# Then grep for "/url:" in the snapshot file
```

## Common Issues

| Issue                  | Cause            | Solution                        |
| ---------------------- | ---------------- | ------------------------------- |
| Click intercepted      | Overlay element  | Use JS click via eval           |
| Service worker caching | Old files cached | Delete session, restart browser |
| No browser visible     | Headless mode    | Use --headed flag or config     |

## Evidence Collection Workflow

1. Run command that changes state
2. Take snapshot to get refs
3. Grep snapshot for URLs/elements of interest
4. Take screenshot for visual proof
5. Check console/network for errors

## Session Management

```bash
# List sessions
playwright-cli session-list

# Clean slate before testing
playwright-cli close
playwright-cli session-stop-all
playwright-cli session-delete
```
