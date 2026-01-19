# Call MCP Tools

<objective>
Execute MCP tools with proper syntax, handle arguments, and process output.
</objective>

<process>
## Step 1: Identify the Tool

First, list available tools:
```bash
npx mcporter list <server>
```

## Step 2: Call with Key=Value Syntax

The standard syntax:
```bash
npx mcporter call <server>.<tool> arg1=value1 arg2=value2
```

Examples:
```bash
npx mcporter call firecrawl.scrape_url url=https://example.com
npx mcporter call tavily.search query="climate change" max_results=5
npx mcporter call github.get_issue owner=facebook repo=react number=1234
```

## Step 3: Alternative Natural Syntax

For complex or nested arguments:
```bash
npx mcporter call "server.tool(arg: value, nested: {key: val})"
```

Examples:
```bash
npx mcporter call "firecrawl.crawl_site(url: 'https://docs.example.com', depth: 3)"
npx mcporter call "openai.chat(messages: [{role: 'user', content: 'Hello'}])"
```

## Step 4: Handle Output

| Flag | Description |
|------|-------------|
| `--output json` | Force JSON output |
| `--output text` | Plain text output |
| `--tail-log` | Stream server logs |

```bash
npx mcporter call server.tool arg=val --output json | jq '.result'
```

## Step 5: Timeout and Error Handling

```bash
npx mcporter call slow-server.tool --timeout 60000  # 60 seconds
```

If you get auth errors (401/403), see `workflows/handle-oauth.md`.

## Common Patterns

**Pipe input from file:**
```bash
cat data.json | npx mcporter call server.process_data data=-
```

**Chain with jq:**
```bash
npx mcporter call api.fetch --output json | jq '.items[] | .name'
```

**Save output:**
```bash
npx mcporter call server.export > output.json
```
</process>

<success_criteria>
- Tool executes successfully with correct arguments
- Output is captured in desired format
- Errors are understood and actionable
</success_criteria>
