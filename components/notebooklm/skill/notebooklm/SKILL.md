---
name: notebooklm
description: Tools for interacting with Google NotebookLM (Research, Studio, Chat) via MCP. Use for research, podcast generation, and notebook management.
mcp:
  notebooklm:
    command: notebooklm-mcp
---

<objective>
Manage Google NotebookLM notebooks - create, add sources, generate podcasts, conduct research, and chat with your knowledge base.
</objective>

<prerequisites>
The `notebooklm-mcp` command must be installed and authenticated:
```bash
npm install -g notebooklm-mcp
notebooklm-mcp auth  # One-time authentication
```
</prerequisites>

<tools>

## list_notebooks
List all notebooks in your account.
```
skill_mcp(mcp_name="notebooklm", tool_name="list_notebooks")
```

## create_notebook
Create a new notebook.
```
skill_mcp(mcp_name="notebooklm", tool_name="create_notebook", arguments='{"title": "My Research"}')
```

## add_source
Add a source to a notebook.
```
skill_mcp(mcp_name="notebooklm", tool_name="add_source", arguments='{"notebook_id": "...", "url": "https://..."}')
```

| Parameter | Purpose |
|-----------|---------|
| `notebook_id` | Target notebook ID |
| `url` | URL to ingest (articles, docs, etc.) |

## generate_audio_overview
Generate a podcast from notebook content.
```
skill_mcp(mcp_name="notebooklm", tool_name="generate_audio_overview", arguments='{"notebook_id": "..."}')
```

## research
Conduct deep research using notebook as knowledge base.
```
skill_mcp(mcp_name="notebooklm", tool_name="research", arguments='{"query": "topic to research", "notebook_id": "..."}')
```

## chat
Chat with notebook content using RAG.
```
skill_mcp(mcp_name="notebooklm", tool_name="chat", arguments='{"notebook_id": "...", "message": "What are the key takeaways?"}')
```

</tools>

<capabilities>
| Feature | Description |
|---------|-------------|
| **Manage Notebooks** | Create, list, organize research notebooks |
| **Add Sources** | Ingest URLs, text, Google Drive files |
| **Generate Content** | Podcasts, videos, slides from sources |
| **Deep Research** | AI research agent for information gathering |
| **Chat** | RAG-powered Q&A with your sources |
</capabilities>

<success_criteria>
- Notebook operations complete without errors
- Sources are ingested and searchable
- Podcast generation starts (may take minutes to complete)
- Chat returns relevant answers from sources
</success_criteria>
