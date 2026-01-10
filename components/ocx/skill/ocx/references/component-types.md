# Component Types Reference

## ocx:skill

Reusable AI instructions loaded on demand.

**Target:** `.opencode/skill/{name}/`

```json
{
  "name": "my-skill",
  "type": "ocx:skill",
  "files": [
    "skill/my-skill/SKILL.md",
    "skill/my-skill/workflows/process.md",
    "skill/my-skill/references/guide.md"
  ],
  "dependencies": []
}
```

---

## ocx:agent

Specialized AI assistant definitions.

**Target:** `.opencode/agent/`

```json
{
  "name": "researcher",
  "type": "ocx:agent",
  "files": [
    "agent/researcher.md"
  ],
  "dependencies": []
}
```

---

## ocx:command

Custom slash commands.

**Target:** `.opencode/command/`

```json
{
  "name": "deploy",
  "type": "ocx:command",
  "files": [
    "command/deploy.md"
  ],
  "dependencies": []
}
```

---

## ocx:plugin

TypeScript/JavaScript event hooks.

**Target:** `.opencode/plugin/`

```json
{
  "name": "my-plugin",
  "type": "ocx:plugin",
  "files": [
    "plugin/my-plugin.ts"
  ],
  "dependencies": []
}
```

For npm plugins (no files, just config):
```json
{
  "name": "oh-my-opencode",
  "type": "ocx:plugin",
  "files": [],
  "dependencies": [],
  "opencode": {
    "plugin": ["npm:oh-my-opencode@2.14.0"]
  }
}
```

---

## ocx:mcp

MCP server configuration.

**Target:** Merged into `opencode.jsonc`

```json
{
  "name": "tavily",
  "type": "ocx:mcp",
  "files": [],
  "dependencies": [],
  "opencode": {
    "mcp": {
      "tavily": {
        "command": "npx",
        "args": ["-y", "@tavily/mcp"],
        "env": {
          "TAVILY_API_KEY": "${TAVILY_API_KEY}"
        }
      }
    }
  }
}
```

Environment variables in `${}` are expanded at runtime.

---

## ocx:bundle

Meta-package that installs dependencies only.

**Target:** N/A (no files)

```json
{
  "name": "core",
  "type": "ocx:bundle",
  "files": [],
  "dependencies": [
    "neo/tavily",
    "neo/notebooklm"
  ]
}
```

Installing a bundle installs all its dependencies recursively.

---

## opencode Field

The `opencode` field in any component is deep-merged into the consumer's `opencode.jsonc`:

```json
{
  "opencode": {
    "plugin": ["npm:some-plugin"],
    "mcp": {
      "server-name": { ... }
    },
    "agent": {
      "agent-name": { "temperature": 0.2 }
    }
  }
}
```

This allows components to configure OpenCode without manual editing.
