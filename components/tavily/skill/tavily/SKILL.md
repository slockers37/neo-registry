---
name: tavily-search-extract
description: Fast web search and content extraction using the Tavily API.
argument-hint: Use for web search, scraping specific URLs, and gathering real-time data or news.
mcp:
  tavily:
    type: http
    url: https://mcp.tavily.com/mcp
---

# Tavily Search & Extract

High-speed web search and clean markdown extraction via the Tavily API.

## Usage

### Web Search
```
skill_mcp(mcp_name="tavily", tool_name="tavily_search", arguments='{"query": "your search", "search_depth": "advanced", "include_answer": true}')
```

| Parameter | Values | Use Case |
|-----------|--------|----------|
| `search_depth` | `"basic"` | Quick facts, broad searches |
| `search_depth` | `"advanced"` | Thorough research, niche data |
| `include_answer` | `true` | Get summarized response |

### Content Extraction
```
skill_mcp(mcp_name="tavily", tool_name="tavily_extract", arguments='{"urls": ["https://example.com/page1", "https://example.com/page2"]}')
```

- Batch up to 10 URLs per call
- Returns clean markdown

## Best Practices
- Use specific, long-tail queries
- Batch URLs in single `tavily_extract` call
