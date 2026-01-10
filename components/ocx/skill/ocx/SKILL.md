---
name: ocx
description: Manage OpenCode components via ocx - install/remove components, configure registries, setup ghost mode for portable personal AI config. Use when working with ocx CLI or component registries.
---

<essential_principles>
## OCX Overview

OCX is a decentralized package manager for OpenCode extensions. Think "npm for AI primitives" with ShadCN's philosophy - code is cloned into your project so you own it.

### Component Types
| Type | Target | Purpose |
|------|--------|---------|
| `ocx:skill` | `.opencode/skill/{name}/` | Reusable AI instructions |
| `ocx:agent` | `.opencode/agent/` | Specialized AI assistants |
| `ocx:command` | `.opencode/command/` | Custom slash commands |
| `ocx:plugin` | `.opencode/plugin/` | TypeScript event hooks |
| `ocx:mcp` | opencode.jsonc | MCP server configs |
| `ocx:bundle` | N/A | Meta-package (deps only) |

### Key Concepts
- **Registry**: Static JSON API serving components (GitHub Pages works)
- **Packument**: Component metadata JSON (`/components/{name}.json`)
- **Ghost Mode**: Portable personal config via symlink farm
- **Lock file**: `ocx.lock` tracks installed versions + hashes
</essential_principles>

<intake>
What do you need help with?

1. Install/manage components
2. Configure registries
3. Setup/use ghost mode
4. Create/publish to a registry
5. Troubleshoot issues

**Wait for response before proceeding.**
</intake>

<routing>
| Response | Workflow |
|----------|----------|
| 1, "install", "add", "remove", "update" | workflows/manage-components.md |
| 2, "registry", "registries", "connect" | workflows/configure-registries.md |
| 3, "ghost", "portable", "profile" | workflows/ghost-mode.md |
| 4, "create registry", "publish", "host" | workflows/create-registry.md |
| 5, "troubleshoot", "error", "not working" | workflows/troubleshoot.md |

**Intent-based routing:**
- "ocx add X" → Execute directly, no workflow needed
- "setup ghost mode" → workflows/ghost-mode.md
- "create my own registry" → workflows/create-registry.md
- "component not found" → workflows/troubleshoot.md

**After reading the workflow, follow it exactly.**
</routing>

<quick_reference>
## Common Commands

```bash
# Project setup
ocx init                              # Initialize in current project
ocx init --registry --namespace foo   # Scaffold a registry

# Component management
ocx add namespace/component           # Install component
ocx add npm:package-name              # Install npm plugin
ocx remove namespace/component        # Uninstall
ocx update                            # Update all
ocx list                              # List installed
ocx search query                      # Search registries

# Registry management
ocx registry add URL --name alias     # Connect registry
ocx registry remove alias             # Disconnect
ocx registry list                     # Show configured

# Ghost mode
ocx ghost init                        # Setup global ghost config
ocx ghost opencode                    # Launch in ghost environment
ocx ghost profile add NAME            # Create profile
ocx ghost profile use NAME            # Switch profile
ocx ghost config                      # Edit ghost.jsonc
```
</quick_reference>

<reference_index>
## References

| File | Content |
|------|---------|
| references/cli-commands.md | Complete CLI reference |
| references/registry-format.md | How to structure a registry |
| references/ghost-mode.md | Ghost configuration details |
| references/component-types.md | Packument formats per type |
| references/troubleshooting.md | Common issues + fixes |
</reference_index>
