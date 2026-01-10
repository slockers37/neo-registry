---
name: tavily-search-extract
description: Fast web search and content extraction using the Tavily API.
model: gemini-2.0-flash-exp
argument-hint: Use for web search, scraping specific URLs, and gathering real-time data or news.
mcp:
  tavily-mcp:
    command: npx -y @tavily/mcp
---

# Tavily Search & Extract

## Purpose
A generic technical driver for the Tavily API, providing high-speed web search and clean markdown extraction capabilities.

## Scope
- Real-time web search with depth control.
- Content extraction from target URLs into clean markdown.
- Batch processing of multiple URLs.

## Instructions

### 1. Web Search (`tavily_search`)
Performs a search for real-time information.
- Use `search_depth: "advanced"` for thorough research or finding niche data.
- Use `search_depth: "basic"` for quick facts or broad searches.
- Set `include_answer: true` for a summarized response.

### 2. Content Extraction (`tavily_extract`)
Extracts clean markdown content from a list of URLs.
- Always batch URLs (pass as an array) to maximize efficiency.
- Ideal for scraping portfolio pages, articles, or documentation.

## Best Practices
- **Query Optimization**: Use specific, long-tail queries.
- **Batching**: Pass up to 10 URLs in a single `tavily_extract` call.
- **Modularity**: Do not include business-specific logic in this skill; use it as a data provider for other skills.

## Security
- **Authentication**: This skill assumes the `TAVILY_API_KEY` is pre-configured in the MCP server's environment.
- **Secrets**: NEVER hardcode or commit API keys to this file. Use a local `.env` file ignored by Git.

## Dependencies
- `tavily-mcp` server.

## Version History
- 1.0.0: Initial WAI-compliant version.
