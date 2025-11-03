Based on the Claude Code plugin documentation, here's the tree-like folder structure for the example plugin shown in the quickstart guide:

```
test-marketplace/
├── .claude-plugin/
│   └── marketplace.json
└── my-first-plugin/
    ├── .claude-plugin/
    │   └── plugin.json
    └── commands/
        └── hello.md
```

The documentation also shows the complete plugin structure with all possible components [(1)](https://docs.claude.com/en/docs/claude-code/plugins#develop-more-complex-plugins):

```
my-first-plugin/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── commands/                 # Custom slash commands (optional)
│   └── hello.md
├── agents/                   # Custom agents (optional)
│   └── helper.md
└── hooks/                    # Event handlers (optional)
    └── hooks.json
```

For a more comprehensive plugin with all components, the structure would include [(2)](https://docs.claude.com/en/docs/claude-code/plugins-reference#plugin-manifest-schema):
How to clone git repo into existing folder?

- **Commands**: Markdown files in `commands/` directory [(2)](https://docs.claude.com/en/docs/claude-code/plugins-reference#plugin-manifest-schema)
- **Agents**: Agent definitions in `agents/` directory [(2)](https://docs.claude.com/en/docs/claude-code/plugins-reference#plugin-manifest-schema)
- **Hooks**: `hooks/hooks.json` for event handling [(2)](https://docs.claude.com/en/docs/claude-code/plugins-reference#plugin-manifest-schema)
- **MCP servers**: `.mcp.json` for external tool integration [(2)](https://docs.claude.com/en/docs/claude-code/plugins-reference#plugin-manifest-schema)

The plugin manifest (`plugin.json`) contains the plugin metadata [(1)](https://docs.claude.com/en/docs/claude-code/plugins#develop-more-complex-plugins), while the marketplace manifest (`marketplace.json`) defines the marketplace catalog [(1)](https://docs.claude.com/en/docs/claude-code/plugins#develop-more-complex-plugins).
