# MCPorter Command Reference

## Global Flags

| Flag | Description |
|------|-------------|
| `--help`, `-h` | Show help |
| `--version`, `-v` | Show version |
| `--config <path>` | Override config file path |

---

## `mcporter list`

List servers and their tools.

```bash
mcporter list [server] [options]
```

| Option | Description |
|--------|-------------|
| `--all-parameters` | Show all parameters including optional |
| `--schema` | Output full JSON schema |
| `--timeout <ms>` | Override timeout (default: 30000) |
| `--http-url <url>` | Connect to ad-hoc HTTP server |
| `--stdio <cmd>` | Connect to ad-hoc stdio server |
| `--name <name>` | Name for ad-hoc server |
| `--persist` | Save ad-hoc server to config |
| `--allow-http` | Allow non-HTTPS URLs |

---

## `mcporter call`

Call an MCP tool.

```bash
mcporter call <server.tool> [args...] [options]
mcporter call "<server.tool(arg: val)>" [options]
```

| Option | Description |
|--------|-------------|
| `--timeout <ms>` | Override timeout |
| `--output <format>` | Output format: `json`, `text` |
| `--tail-log` | Stream server logs |
| `--http-url <url>` | Use ad-hoc HTTP server |
| `--stdio <cmd>` | Use ad-hoc stdio server |

**Argument Formats:**
- Key=value: `arg=value`
- JSON: `arg='{"nested": "value"}'`
- Natural: `"tool(arg: value, list: [1, 2, 3])"`

---

## `mcporter generate-cli`

Generate standalone CLI from MCP server.

```bash
mcporter generate-cli [options]
```

| Option | Description |
|--------|-------------|
| `--server <name>` | Source MCP server |
| `--command <name>` | Output command name |
| `--output <dir>` | Output directory (default: `.`) |
| `--bundle` | Create single-file bundle |
| `--bundler <name>` | Bundler: `esbuild`, `bun` |
| `--compile` | Compile to native binary |
| `--include-tools <list>` | Comma-separated tools to include |
| `--exclude-tools <list>` | Comma-separated tools to exclude |
| `--from <url>` | Generate from ad-hoc URL |
| `--dry-run` | Preview without writing |

---

## `mcporter emit-ts`

Generate TypeScript types or client.

```bash
mcporter emit-ts [options]
```

| Option | Description |
|--------|-------------|
| `--mode <mode>` | `types` (`.d.ts`) or `client` (proxy factory) |
| `--out <path>` | Output file path |
| `--include-optional` | Include optional parameters in types |
| `--server <name>` | Specific server (default: all) |

---

## `mcporter config`

View or edit configuration.

```bash
mcporter config [options]
```

| Option | Description |
|--------|-------------|
| `--edit` | Open in $EDITOR |
| `--global` | Target global config |
| `--project` | Target project config |

---

## `mcporter auth`

Manage OAuth authentication.

```bash
mcporter auth <url> [options]
```

| Option | Description |
|--------|-------------|
| `--clear` | Clear cached credentials |
| `--force` | Force re-authentication |

---

## `mcporter daemon`

Manage background daemon for warm servers.

```bash
mcporter daemon <command>
```

| Command | Description |
|---------|-------------|
| `start` | Start daemon |
| `stop` | Stop daemon |
| `status` | Show daemon status |
| `restart` | Restart daemon |

---

## `mcporter iterm`

Launch full TTY mode for interactive sessions.

```bash
mcporter iterm [server]
```

Useful for:
- OAuth flows that need browser interaction
- Interactive debugging
- REPL-style exploration
