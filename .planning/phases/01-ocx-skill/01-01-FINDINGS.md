# OCX Research Findings

## 1. CLI Commands

### Project Management
| Command | Description | Key Options |
|---------|-------------|-------------|
| `ocx init [directory]` | Initialize OCX in project | `--registry` (scaffold registry), `--namespace`, `--author` |
| `ocx add <components...>` | Install components | `npm:<package>`, `--trust`, `--dry-run` |
| `ocx update [components...]` | Update components | `--all`, `--registry <name>` |
| `ocx remove <components...>` | Uninstall components | |
| `ocx list` / `ocx search [query]` | List/search components | `--installed` |
| `ocx diff [component]` | Show diff vs upstream | |

### Registry Management
| Command | Description |
|---------|-------------|
| `ocx registry add <url> [--name <alias>]` | Connect registry |
| `ocx registry remove <name>` | Disconnect registry |
| `ocx registry list` | List configured registries |
| `ocx build [path]` | Compile registry to static dist |

### Ghost Mode
| Command | Description |
|---------|-------------|
| `ocx ghost init` | Setup global ghost config |
| `ocx ghost opencode [args...]` | Launch OpenCode in isolated env |
| `ocx ghost profile <add\|use\|list\|remove>` | Manage profiles |
| `ocx ghost config` | Open ghost.jsonc in editor |

---

## 2. Registry Structure

### index.json (at registry root)
```json
{
  "name": "My Registry",
  "namespace": "my-org",
  "version": "1.0.0",
  "author": "Alice",
  "components": [
    { "name": "researcher", "type": "ocx:agent", "version": "1.2.0" }
  ]
}
```

### Packument Format (/components/{name}.json)
```json
{
  "name": "researcher",
  "dist-tags": { "latest": "1.2.0" },
  "versions": {
    "1.2.0": {
      "name": "researcher",
      "type": "ocx:agent",
      "version": "1.2.0",
      "files": [
        { "path": "agent.md", "target": ".opencode/agent/researcher.md" }
      ],
      "dependencies": ["my-org/utils"],
      "opencode": { "agent": { "researcher": { "temperature": 0.2 } } }
    }
  }
}
```

### File Organization
- `/index.json` - Registry manifest
- `/components/{name}.json` - Component packument
- `/components/{name}/{path}` - Raw file contents
- `/.well-known/ocx.json` - Discovery file: `{ "version": 1, "registry": "/index.json" }`

---

## 3. Ghost Mode

### How It Works
1. Creates unique temporary directory
2. Symlinks all project files EXCEPT OpenCode files
3. Symlinks OpenCode files from Ghost Profile
4. Sets `GIT_WORK_TREE` to real project
5. Spawns `opencode` in ghost directory

### ghost.jsonc Configuration
```jsonc
{
  "registries": {
    "kdco": { "url": "https://ocx.kdco.dev" }
  },
  "componentPath": ".opencode",
  "include": ["**/AGENTS.md", ".opencode/skills/**"],
  "exclude": ["node_modules/**"]
}
```

---

## 4. Component Types

| Type | Default Target | Description |
|------|----------------|-------------|
| `ocx:skill` | `.opencode/skill/{name}/` | Reusable Markdown instructions |
| `ocx:agent` | `.opencode/agent/` | Specialized AI assistant definitions |
| `ocx:command` | `.opencode/command/` | Custom slash commands |
| `ocx:plugin` | `.opencode/plugin/` | TypeScript/JS event hooks |
| `ocx:mcp` | N/A | MCP server configuration |
| `ocx:bundle` | N/A | Meta-package with dependencies only |

### Packument Fields
- `files`: Array of `{ path, target? }` 
- `dependencies`: Array of `namespace/name`
- `opencode`: Deep-merged into consumer's opencode.jsonc

---

## 5. Project Config

### ocx.jsonc (project root)
```jsonc
{
  "registries": {
    "kdco": { "url": "https://ocx.kdco.dev" },
    "private": { "url": "https://registry.internal.com", "headers": { "Authorization": "Bearer..." } }
  },
  "lockRegistries": false,
  "skipCompatCheck": false
}
```

### ocx.lock (lock file)
```json
{
  "lockVersion": 1,
  "installed": {
    "kdco/agents": {
      "version": "1.2.0",
      "hash": "sha256:...",
      "files": [".opencode/agent/coder.md"],
      "registry": "kdco"
    }
  }
}
```

---

## Source Links
- CLI Commands: `packages/cli/src/commands`
- Registry Protocol: `docs/REGISTRY_PROTOCOL.md`
- Ghost Mode: `packages/cli/src/utils/symlink-farm.ts`
- Schemas: `docs/schemas`
