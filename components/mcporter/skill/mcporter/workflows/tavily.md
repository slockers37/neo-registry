# Tavily Web Research

<objective>
Use Tavily MCP for web search, content extraction, site crawling, and URL mapping.
</objective>

<available_tools>
| Tool | Description |
|------|-------------|
| `tavily_search` | Web search with AI-powered relevance ranking |
| `tavily_extract` | Extract clean content from URLs |
| `tavily_crawl` | Crawl multiple pages from a site |
| `tavily_map` | Discover site structure and URLs |
</available_tools>

<process>
## Web Search

```bash
npx mcporter call "tavily.tavily_search(query: 'your search query')"
```

With options:
```bash
npx mcporter call tavily.tavily_search \
  query="AI coding assistants 2024" \
  max_results=10 \
  search_depth=advanced \
  include_raw_content=true
```

| Option | Values | Description |
|--------|--------|-------------|
| `search_depth` | `basic`, `advanced` | Depth of search |
| `topic` | `general`, `news`, `finance` | Search category |
| `max_results` | 1-20 | Number of results |
| `include_raw_content` | true/false | Include full page content |
| `include_domains` | list | Limit to specific domains |
| `exclude_domains` | list | Exclude specific domains |
| `time_range` | `day`, `week`, `month`, `year` | Recency filter |

## Extract Content from URLs

```bash
npx mcporter call tavily.tavily_extract \
  urls='["https://example.com/article1", "https://example.com/article2"]' \
  format=markdown
```

## Crawl a Website

```bash
npx mcporter call tavily.tavily_crawl \
  url="https://docs.example.com" \
  max_depth=3 \
  limit=50
```

## Map Site Structure

```bash
npx mcporter call tavily.tavily_map \
  url="https://docs.example.com" \
  max_depth=2
```
</process>

<common_patterns>
**Quick search:**
```bash
npx mcporter call "tavily.tavily_search(query: 'latest news on topic')"
```

**Deep research (more results, full content):**
```bash
npx mcporter call tavily.tavily_search \
  query="topic" \
  search_depth=advanced \
  max_results=15 \
  include_raw_content=true
```

**News search:**
```bash
npx mcporter call tavily.tavily_search \
  query="breaking news" \
  topic=news \
  time_range=day
```

**Extract and analyze:**
```bash
npx mcporter call tavily.tavily_extract urls='["https://url1.com", "https://url2.com"]' --output json
```
</common_patterns>

<success_criteria>
- Search returns relevant results
- Content is extracted cleanly
- Crawl respects depth/limit settings
</success_criteria>
