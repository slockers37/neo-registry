# MCPorter Config Schema

## Config File Locations

| Location | Scope |
|----------|-------|
| `~/.mcporter/mcporter.json` | Global (user-wide) |
| `./config/mcporter.json` | Project-specific |

Project config merges with and overrides global config.

---

## Full Schema

```json
{
  "$schema": "https://mcporter.dev/schema.json",
  
  "mcpServers": {
    "<server-name>": {
      // STDIO-based server
      "command": "npx",
      "args": ["-y", "@package/mcp-server"],
      "env": {
        "API_KEY": "${ENV_VAR}"
      },
      
      // OR HTTP-based server
      "baseUrl": "https://mcp.example.com",
      "headers": {
        "Authorization": "Bearer ${TOKEN}"
      }
    }
  },
  
  "imports": [
    "cursor",
    "claude-code", 
    "vscode",
    "opencode",
    "windsurf",
    "codex"
  ],
  
  "daemon": {
    "enabled": true,
    "warmServers": ["frequently-used-server"]
  },
  
  "defaults": {
    "timeout": 30000,
    "output": "json"
  }
}
```

---

## Server Configuration

### STDIO Server (runs locally)

```json
{
  "mcpServers": {
    "my-local-server": {
      "command": "node",
      "args": ["./path/to/server.js"],
      "env": {
        "DEBUG": "true"
      },
      "cwd": "/path/to/working/dir"
    }
  }
}
```

### NPX Server (auto-installed)

```json
{
  "mcpServers": {
    "firecrawl": {
      "command": "npx",
      "args": ["-y", "@anthropic/firecrawl-mcp"],
      "env": {
        "FIRECRAWL_API_KEY": "${FIRECRAWL_API_KEY}"
      }
    }
  }
}
```

### HTTP Server (remote)

```json
{
  "mcpServers": {
    "remote-api": {
      "baseUrl": "https://api.example.com/mcp",
      "headers": {
        "Authorization": "Bearer ${API_TOKEN}",
        "X-Custom-Header": "value"
      }
    }
  }
}
```

---

## Environment Variable Interpolation

Use `${VAR}` syntax for secrets:

```json
{
  "mcpServers": {
    "openai": {
      "env": {
        "OPENAI_API_KEY": "${OPENAI_API_KEY}"
      }
    }
  }
}
```

Variables are read from your shell environment at runtime.

---

## Editor Imports

The `imports` array auto-discovers servers from editor configs:

| Import | Config Location |
|--------|-----------------|
| `cursor` | `~/.cursor/mcp.json` |
| `claude-code` | `~/.claude/claude_desktop_config.json` |
| `vscode` | `~/.vscode/mcp.json` |
| `opencode` | `~/.config/opencode/opencode.json` |
| `windsurf` | `~/.windsurf/mcp.json` |
| `codex` | `~/.codex/mcp.json` |

Imported servers are merged with your MCPorter config.

---

## Daemon Configuration

Keep frequently-used servers warm:

```json
{
  "daemon": {
    "enabled": true,
    "warmServers": ["firecrawl", "tavily"],
    "idleTimeout": 300000
  }
}
```
