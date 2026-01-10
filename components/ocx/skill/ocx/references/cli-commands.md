# OCX CLI Commands Reference

## Project Commands

### `ocx init [directory]`
Initialize OCX in a project.

| Option | Description |
|--------|-------------|
| `--registry` | Scaffold a registry instead of project |
| `--namespace <name>` | Set registry namespace |
| `--author <name>` | Set registry author |

### `ocx add <components...>`
Install components.

| Option | Description |
|--------|-------------|
| `--trust` | Skip integrity verification |
| `--dry-run` | Preview without installing |
| `--force` | Overwrite existing files |

Examples:
```bash
ocx add neo/core                    # From registry
ocx add npm:oh-my-opencode          # From npm
ocx add neo/core neo/plugins        # Multiple
```

### `ocx remove <components...>`
Uninstall components.

### `ocx update [components...]`
Update components to latest versions.

| Option | Description |
|--------|-------------|
| `--all` | Update all installed |
| `--registry <name>` | Only from specific registry |

### `ocx list`
List components.

| Option | Description |
|--------|-------------|
| `--installed` | Only show installed (default) |
| `--all` | Show all available |
| `<namespace>` | Filter by registry namespace |

### `ocx search <query>`
Search components by name/description.

### `ocx diff [component]`
Show changes between installed and upstream versions.

---

## Registry Commands

### `ocx registry add <url>`
Connect to a registry.

| Option | Description |
|--------|-------------|
| `--name <alias>` | Local alias for registry |
| `--header <key:value>` | Auth header |

### `ocx registry remove <name>`
Disconnect a registry.

### `ocx registry list`
Show configured registries.

### `ocx build [path]`
Compile registry source to static distribution.

---

## Ghost Mode Commands

### `ocx ghost init`
Initialize ghost mode configuration.

| Option | Description |
|--------|-------------|
| `--force` | Overwrite existing config |

### `ocx ghost opencode [args...]`
Launch OpenCode in ghost environment. All args passed to opencode.

### `ocx ghost add <components...>`
Install components to active ghost profile.

### `ocx ghost profile <subcommand>`
Manage ghost profiles.

| Subcommand | Description |
|------------|-------------|
| `add <name>` | Create new profile |
| `use <name>` | Switch active profile |
| `list` | Show all profiles |
| `remove <name>` | Delete profile |

### `ocx ghost config`
Open ghost.jsonc in default editor.
