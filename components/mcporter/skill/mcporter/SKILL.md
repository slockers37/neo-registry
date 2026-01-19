---
name: mcporter
description: Unified MCP server management via MCPorter - discover tools, call MCP servers, manage config, handle OAuth, generate standalone CLIs. Use when working with MCP servers or needing to call MCP tools.
---

<essential_principles>
## MCPorter Overview

MCPorter is a unified TypeScript runtime and CLI for MCP (Model Context Protocol) servers. It provides:

- **Zero-config discovery**: Auto-imports MCPs from Cursor, Claude, Codex, Windsurf, OpenCode, VS Code
- **Unified calling**: One syntax (`mcporter call server.tool arg=val`) for all MCPs
- **CLI generation**: Create standalone CLIs from any MCP server
- **TypeScript codegen**: Generate typed clients for programmatic use
- **OAuth handling**: Automatic OAuth flows with credential caching
- **Daemon mode**: Keep MCP servers warm for faster response

### Key Principle: Config-Driven, Not Skill-Driven

MCPs belong in `~/.mcporter/mcporter.json` (the source of truth). MCPorter auto-discovers servers from:
1. `~/.mcporter/mcporter.json` (global config)
2. `./config/mcporter.json` (project config)
3. Editor configs (Cursor, Claude, VS Code, etc.) via imports

### Running MCPorter

```bash
npx mcporter <command>          # Standard invocation
npx mcporter iterm              # Full TTY mode (interactive)
```
</essential_principles>

<intake>
What do you need help with?

1. Discover available MCP servers and their tools
2. Call an MCP tool
3. Configure MCP servers
4. Handle OAuth authentication
5. Generate a standalone CLI from an MCP

**Wait for response before proceeding.**
</intake>

<routing>
| Response | Workflow |
|----------|----------|
| 1, "discover", "list", "find", "what tools", "available" | workflows/discover-tools.md |
| 2, "call", "run", "execute", "invoke" | workflows/call-tools.md |
| 3, "config", "configure", "setup", "add server" | workflows/manage-config.md |
| 4, "oauth", "auth", "login", "401", "403" | workflows/handle-oauth.md |
| 5, "generate", "cli", "standalone", "emit" | workflows/generate-cli.md |

**Intent-based routing:**
- "mcporter call X" → Execute directly, use call syntax from quick reference
- "list tools from firecrawl" → workflows/discover-tools.md
- "add a new MCP server" → workflows/manage-config.md
- "getting 401 errors" → workflows/handle-oauth.md
- "create CLI for tavily" → workflows/generate-cli.md

**After reading the workflow, follow it exactly.**
</routing>

<quick_reference>
## Command Quick Reference

```bash
# Discovery
npx mcporter list                    # List all servers
npx mcporter list <server>           # List tools for a server
npx mcporter list <server> --schema  # Show full JSON schema

# Calling tools
npx mcporter call server.tool arg=value     # Key=value syntax
npx mcporter call "server.tool(arg: value)" # Natural syntax
npx mcporter call server.tool --output json # Output format

# Configuration
npx mcporter config                  # Show merged config
npx mcporter config --edit           # Open config in editor

# OAuth
npx mcporter auth <url>              # Authenticate to OAuth server
npx mcporter auth <url> --clear      # Clear cached credentials

# CLI Generation
npx mcporter generate-cli --server X --command myctl
npx mcporter generate-cli --server X --compile  # Compile to binary

# TypeScript
npx mcporter emit-ts --mode types    # Generate .d.ts
npx mcporter emit-ts --mode client   # Generate proxy factory

# Daemon
npx mcporter daemon start            # Keep servers warm
npx mcporter daemon stop
```

## Ad-hoc Connections

Connect to any MCP without config:
```bash
npx mcporter list --http-url https://mcp.example.com
npx mcporter call --http-url https://api.example.com server.tool arg=val
npx mcporter auth https://oauth-server.com  # Pre-authenticate
```
</quick_reference>

<reference_index>
## References

| File | Content |
|------|---------|
| references/command-reference.md | Complete CLI flags and options |
| references/config-schema.md | Config file format and imports |
| references/typescript-api.md | Programmatic usage patterns |
</reference_index>

<common_patterns>
## Frequently Used Patterns

**List what's available:**
```bash
npx mcporter list
npx mcporter list firecrawl --schema
```

**Call a tool:**
```bash
npx mcporter call firecrawl.scrape_url url=https://example.com
npx mcporter call "tavily.search(query: 'latest news')"
```

**Add ad-hoc server and persist:**
```bash
npx mcporter list --http-url https://new-mcp.com --persist --name mynew
```

**Generate CLI for distribution:**
```bash
npx mcporter generate-cli --server tavily --command tavily-cli --compile
```
</common_patterns>
