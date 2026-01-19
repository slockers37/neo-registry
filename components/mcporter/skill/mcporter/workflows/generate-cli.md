# Generate Standalone CLI from MCP

<objective>
Create standalone command-line tools from MCP servers for distribution or shell scripting.
</objective>

<when_to_use>
Generate a CLI when:
- Sharing tools with non-agent users
- Creating simple shell scripts
- Distributing as a standalone binary
- Need offline/portable tool

**For agent use:** Prefer `mcporter call` directly - it's more flexible.
</when_to_use>

<process>
## Step 1: Generate Basic CLI

```bash
npx mcporter generate-cli --server <server-name> --command <cli-name>
```

Example:
```bash
npx mcporter generate-cli --server firecrawl --command fc
```

Creates `./fc` (or `./fc.js`) that you can run:
```bash
./fc scrape_url --url https://example.com
./fc crawl_site --url https://docs.example.com --depth 3
```

## Step 2: Filter Tools

Include only specific tools:
```bash
npx mcporter generate-cli --server tavily --command tavily-cli \
  --include-tools search,extract
```

Exclude tools:
```bash
npx mcporter generate-cli --server github --command gh-mcp \
  --exclude-tools delete_repo,force_push
```

## Step 3: Bundle for Distribution

Create a single-file bundle:
```bash
npx mcporter generate-cli --server myserver --command myctl --bundle
```

Choose bundler:
```bash
npx mcporter generate-cli --server X --command X --bundle --bundler esbuild
npx mcporter generate-cli --server X --command X --bundle --bundler bun
```

## Step 4: Compile to Binary

Create a standalone executable (no Node required):
```bash
npx mcporter generate-cli --server tavily --command tavily --compile
```

Produces platform-specific binary (uses `bun build --compile` or `pkg`).

## Step 5: Generate from Ad-hoc URL

```bash
npx mcporter generate-cli --from https://mcp.example.com --command myctl
```

## Step 6: Dry Run

Preview without writing files:
```bash
npx mcporter generate-cli --server X --command X --dry-run
```

## Output Location

| Flag | Output |
|------|--------|
| (default) | `./<command>` |
| `--output ./bin` | `./bin/<command>` |
| `--bundle` | Single `.js` file |
| `--compile` | Native binary |
</process>

<success_criteria>
- CLI is generated and executable
- Correct tools are included/excluded
- Binary runs without Node.js if compiled
</success_criteria>
