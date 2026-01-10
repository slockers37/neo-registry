---
name: notebooklm
description: Tools for interacting with Google NotebookLM (Research, Studio, Chat) via MCP.
argument-hint: Use for all NotebookLM operations including research, podcast generation, and notebook management.
---

# NotebookLM Skill

Integration with Google NotebookLM via the `notebooklm-mcp` server.

## Prerequisites
User must have `notebooklm` MCP configured in their `opencode.json`:
```jsonc
"mcp": {
  "notebooklm": {
    "command": "notebooklm-mcp"
  }
}
```

Authentication is handled by the MCP server (cached credentials).

## Usage

Use `skill_mcp` to invoke NotebookLM operations:

### List Notebooks
```
skill_mcp(mcp_name="notebooklm", tool_name="list_notebooks")
```

### Create Notebook
```
skill_mcp(mcp_name="notebooklm", tool_name="create_notebook", arguments='{"title": "My Research"}')
```

### Add Source
```
skill_mcp(mcp_name="notebooklm", tool_name="add_source", arguments='{"notebook_id": "...", "url": "https://..."}')
```

### Generate Podcast (Audio Overview)
```
skill_mcp(mcp_name="notebooklm", tool_name="generate_audio_overview", arguments='{"notebook_id": "..."}')
```

### Deep Research
```
skill_mcp(mcp_name="notebooklm", tool_name="research", arguments='{"query": "Quantum computing trends", "notebook_id": "..."}')
```

### Chat with Notebook
```
skill_mcp(mcp_name="notebooklm", tool_name="chat", arguments='{"notebook_id": "...", "message": "What are the key takeaways?"}')
```

## Capabilities

| Feature | Description |
|---------|-------------|
| **Manage Notebooks** | Create, list, organize research notebooks |
| **Add Sources** | Ingest URLs, text, Google Drive files |
| **Generate Content** | Podcasts, videos, slides |
| **Deep Research** | Research agent for information gathering |
| **Chat** | Query notebooks using RAG |

## Workflows

For podcast generation workflow, see: `Workflows/PodcastGeneration.md`
