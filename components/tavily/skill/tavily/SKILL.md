---
name: tavily-search-extract
description: Fast web search and content extraction using the Tavily API. Use for web search, scraping specific URLs, and gathering real-time data or news.
mcp:
  tavily:
    transport: sse
    url: https://mcp.tavily.com/sse
    headers:
      Authorization: Bearer ${TAVILY_API_KEY}
---

<objective>
Perform high-speed web searches and extract clean markdown content from URLs via the Tavily API.
</objective>

<prerequisites>
Set environment variable before use:
```bash
export TAVILY_API_KEY=tvly-your-key-here
```
</prerequisites>

<tools>

## tavily_search
Performs web search for real-time information.

```
skill_mcp(mcp_name="tavily", tool_name="tavily_search", arguments='{"query": "your search query", "search_depth": "advanced", "include_answer": true}')
```

| Parameter | Values | Purpose |
|-----------|--------|---------|
| `query` | string | Search query |
| `search_depth` | `"basic"` / `"advanced"` | Basic for quick facts, advanced for thorough research |
| `include_answer` | boolean | Include AI-summarized answer |

## tavily_extract
Extracts clean markdown from URLs.

```
skill_mcp(mcp_name="tavily", tool_name="tavily_extract", arguments='{"urls": ["https://example.com/page1", "https://example.com/page2"]}')
```

| Parameter | Purpose |
|-----------|---------|
| `urls` | Array of URLs to extract (batch up to 10) |

</tools>

<best_practices>
- Use specific, long-tail queries for better results
- Batch multiple URLs in single `tavily_extract` call
- Use `search_depth: "advanced"` for niche or technical topics
- Set `include_answer: true` when you need a quick summary
</best_practices>

<success_criteria>
- Search returns relevant results with sources
- Extract returns clean markdown without boilerplate
- No authentication errors (API key properly set)
</success_criteria>
