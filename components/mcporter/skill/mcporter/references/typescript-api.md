# MCPorter TypeScript API

## Installation

```bash
npm install mcporter
# or
bun add mcporter
```

---

## Quick Start

```typescript
import { createRuntime, createServerProxy, callOnce } from 'mcporter';

// One-shot call (simplest)
const result = await callOnce('firecrawl.scrape_url', { url: 'https://example.com' });

// Create a reusable proxy
const firecrawl = await createServerProxy('firecrawl');
const page = await firecrawl.scrape_url({ url: 'https://example.com' });
```

---

## Core Functions

### `callOnce(tool, args)`

Single tool invocation. Starts server, calls, shuts down.

```typescript
import { callOnce } from 'mcporter';

const result = await callOnce('tavily.search', { 
  query: 'latest AI news',
  max_results: 5 
});
```

Best for: One-off calls, scripts, simple automation.

### `createServerProxy(serverName)`

Create typed proxy for repeated calls. Keeps connection alive.

```typescript
import { createServerProxy } from 'mcporter';

const github = await createServerProxy('github');

// All methods are typed based on server schema
const issue = await github.get_issue({ owner: 'facebook', repo: 'react', number: 1 });
const prs = await github.list_pull_requests({ owner: 'facebook', repo: 'react' });

// Close when done
await github.close();
```

Best for: Multiple calls to same server, long-running processes.

### `createRuntime(options)`

Full control over server lifecycle.

```typescript
import { createRuntime } from 'mcporter';

const runtime = await createRuntime({
  configPath: './config/mcporter.json',
  timeout: 60000
});

// Access multiple servers
const firecrawl = runtime.getServer('firecrawl');
const tavily = runtime.getServer('tavily');

// Call tools
const page = await firecrawl.call('scrape_url', { url: '...' });

// List available tools
const tools = await runtime.listTools('firecrawl');

// Cleanup
await runtime.close();
```

---

## Generated Types

Generate types for your configured servers:

```bash
npx mcporter emit-ts --mode types --out ./types/mcp.d.ts
```

Then use with full type safety:

```typescript
import type { FirecrawlTools, TavilyTools } from './types/mcp';

const firecrawl = await createServerProxy<FirecrawlTools>('firecrawl');
// All methods fully typed
```

---

## Generated Client

Generate a complete client module:

```bash
npx mcporter emit-ts --mode client --out ./lib/mcp-client.ts
```

```typescript
import { createFirecrawlClient, createTavilyClient } from './lib/mcp-client';

const firecrawl = await createFirecrawlClient();
const result = await firecrawl.scrapeUrl({ url: '...' });
```

---

## Ad-hoc Connections

Connect to servers not in config:

```typescript
import { createServerProxy } from 'mcporter';

// HTTP server
const remote = await createServerProxy({
  baseUrl: 'https://mcp.example.com',
  headers: { Authorization: `Bearer ${token}` }
});

// STDIO server
const local = await createServerProxy({
  command: 'node',
  args: ['./my-server.js']
});
```

---

## Error Handling

```typescript
import { callOnce, MCPError, AuthError, TimeoutError } from 'mcporter';

try {
  await callOnce('server.tool', args);
} catch (error) {
  if (error instanceof AuthError) {
    // Handle OAuth - may need to run `mcporter auth <url>`
  } else if (error instanceof TimeoutError) {
    // Server too slow
  } else if (error instanceof MCPError) {
    // Generic MCP protocol error
    console.error(error.code, error.message);
  }
}
```

---

## Composing Multiple Servers

```typescript
import { createRuntime } from 'mcporter';

async function researchTopic(topic: string) {
  const runtime = await createRuntime();
  
  // Search for content
  const searchResults = await runtime.call('tavily.search', { query: topic });
  
  // Scrape top results
  const pages = await Promise.all(
    searchResults.results.slice(0, 3).map(r =>
      runtime.call('firecrawl.scrape_url', { url: r.url })
    )
  );
  
  await runtime.close();
  return pages;
}
```
