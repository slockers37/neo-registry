---
name: notebooklm
description: Tools for interacting with Google NotebookLM (Research, Studio, Chat) via MCP.
argument-hint: Use for all NotebookLM operations including research, podcast generation, and notebook management.
mcp:
  notebooklm:
    command: notebooklm-mcp
---

# NotebookLM Skill

Integration with Google NotebookLM via MCP.

## Usage

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

### Generate Podcast
```
skill_mcp(mcp_name="notebooklm", tool_name="generate_audio_overview", arguments='{"notebook_id": "..."}')
```

### Deep Research
```
skill_mcp(mcp_name="notebooklm", tool_name="research", arguments='{"query": "topic", "notebook_id": "..."}')
```

### Chat with Notebook
```
skill_mcp(mcp_name="notebooklm", tool_name="chat", arguments='{"notebook_id": "...", "message": "What are the key takeaways?"}')
```

## Capabilities

| Feature | Description |
|---------|-------------|
| **Manage Notebooks** | Create, list, organize |
| **Add Sources** | URLs, text, Google Drive |
| **Generate Content** | Podcasts, videos, slides |
| **Deep Research** | Research agent |
| **Chat** | RAG-powered Q&A |

## Workflows
For podcast generation: `Workflows/PodcastGeneration.md`
