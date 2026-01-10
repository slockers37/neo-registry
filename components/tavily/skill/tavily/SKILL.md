---
name: tavily-search-extract
description: Fast web search and content extraction using the Tavily API.
argument-hint: Use for web search, scraping specific URLs, and gathering real-time data or news.
---

# Tavily Search & Extract

## Purpose
High-speed web search and clean markdown extraction via the Tavily API.

## Prerequisites
User must have `tavily` MCP configured in their `opencode.json`:
```jsonc
"mcp": {
  "tavily": {
    "command": "npx",
    "args": ["-y", "@tavily/mcp"],
    "env": { "TAVILY_API_KEY": "${TAVILY_API_KEY}" }
  }
}
```

## Usage

### Web Search (`tavily_search`)
Performs a search for real-time information.

```
skill_mcp(mcp_name="tavily", tool_name="tavily_search", arguments='{"query": "your search", "search_depth": "advanced", "include_answer": true}')
```

| Parameter | Values | Use Case |
|-----------|--------|----------|
| `search_depth` | `"basic"` | Quick facts, broad searches |
| `search_depth` | `"advanced"` | Thorough research, niche data |
| `include_answer` | `true` | Get summarized response |

### Content Extraction (`tavily_extract`)
Extracts clean markdown from URLs.

```
skill_mcp(mcp_name="tavily", tool_name="tavily_extract", arguments='{"urls": ["https://example.com/page1", "https://example.com/page2"]}')
```

- Batch up to 10 URLs per call for efficiency
- Returns clean markdown, ideal for articles/documentation

## Best Practices
- **Query Optimization**: Use specific, long-tail queries
- **Batching**: Pass multiple URLs in single `tavily_extract` call
- **Modularity**: Use as data provider for other skills

## Security
- API key configured via environment variable `TAVILY_API_KEY`
- Never hardcode keys - they stay in user's local config
