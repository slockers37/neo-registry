# Manage MCPorter Configuration

<objective>
Configure MCP servers, set up imports from editors, and manage global/project configs.
</objective>

<required_reading>
Read `references/config-schema.md` for full config format.
</required_reading>

<process>
## Step 1: Understand Config Locations

| Location | Scope | Priority |
|----------|-------|----------|
| `~/.mcporter/mcporter.json` | Global | Base |
| `./config/mcporter.json` | Project | Overrides global |
| Editor imports | Dynamic | Merged in |

## Step 2: View Current Config

```bash
npx mcporter config              # Show merged config
npx mcporter config --edit       # Open in $EDITOR
```

## Step 3: Add a New MCP Server

Edit `~/.mcporter/mcporter.json`:

```json
{
  "mcpServers": {
    "my-server": {
      "command": "npx",
      "args": ["-y", "@company/mcp-server"],
      "env": {
        "API_KEY": "${MY_API_KEY}"
      }
    }
  }
}
```

For HTTP-based servers:
```json
{
  "mcpServers": {
    "remote-api": {
      "baseUrl": "https://mcp.example.com",
      "headers": {
        "Authorization": "Bearer ${API_TOKEN}"
      }
    }
  }
}
```

## Step 4: Import from Editor Configs

Add to your config:
```json
{
  "imports": ["cursor", "claude-code", "vscode", "opencode", "windsurf", "codex"]
}
```

MCPorter will automatically discover servers configured in these editors.

## Step 5: Add Ad-hoc Server and Persist

```bash
npx mcporter list --http-url https://new-server.com --persist --name myserver
```

This adds the server to your global config.

## Step 6: Environment Variables

Config supports `${VAR}` syntax for secrets:
```json
{
  "mcpServers": {
    "openai": {
      "command": "npx",
      "args": ["-y", "@openai/mcp"],
      "env": {
        "OPENAI_API_KEY": "${OPENAI_API_KEY}"
      }
    }
  }
}
```

Set in your shell: `export OPENAI_API_KEY=sk-...`
</process>

<success_criteria>
- New MCP server is configured and accessible via `mcporter list`
- Environment variables are properly set for secrets
- Editor imports are working (servers from Cursor/Claude visible)
</success_criteria>
