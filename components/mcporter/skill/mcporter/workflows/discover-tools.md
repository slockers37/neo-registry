# Discover MCP Tools

<objective>
Find available MCP servers and explore their tools, parameters, and schemas.
</objective>

<process>
## Step 1: List All Available Servers

```bash
npx mcporter list
```

This shows all configured servers from:
- `~/.mcporter/mcporter.json` (global)
- `./config/mcporter.json` (project)
- Imported editor configs (Cursor, Claude, VS Code, etc.)

## Step 2: List Tools for a Specific Server

```bash
npx mcporter list <server-name>
```

Example output:
```
firecrawl
  ├─ scrape_url      Scrape a single URL
  ├─ crawl_site      Crawl entire website
  └─ search          Search indexed content
```

## Step 3: Get Full Schema for a Tool

```bash
npx mcporter list <server-name> --schema
```

Shows JSON Schema for all parameters including types, descriptions, and defaults.

## Step 4: Explore Ad-hoc Server (No Config)

```bash
npx mcporter list --http-url https://mcp-server.example.com
npx mcporter list --http-url https://mcp-server.example.com --schema
```

## Useful Flags

| Flag | Description |
|------|-------------|
| `--all-parameters` | Show all params including optional |
| `--schema` | Full JSON schema output |
| `--timeout <ms>` | Override default timeout |
</process>

<success_criteria>
- User can see all available MCP servers
- User can list tools for any specific server
- User understands parameter schemas for tools they want to call
</success_criteria>
