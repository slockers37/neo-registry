---
name: notebooklm
description: Tools for interacting with Google NotebookLM (Research, Studio, Chat) via MCP.
model: gemini-2.0-flash-exp
argument-hint: Use for all NotebookLM operations including research, podcast generation, and notebook management.
mcp:
  notebooklm:
    command: notebooklm-mcp
---

# NotebookLM Skill

Integration with Google NotebookLM via the `notebooklm-mcp` server.

## Usage

This skill allows you to:
1.  **Manage Notebooks**: Create, list, and organize research notebooks.
2.  **Add Sources**: Ingest URLs, text, and Google Drive files.
3.  **Generate Studio Content**: Create podcasts (Audio Overviews), videos, and slides.
4.  **Deep Research**: Use the research agent to gather information.
5.  **Chat**: Query your notebooks using NotebookLM's RAG capabilities.

## Authentication

Authentication is handled externally. The MCP server expects credentials to be cached locally or configured in its environment.

## Examples

### Create a Podcast from a URL
"Create a podcast about this article: https://example.com/article"

### Start Deep Research
"Start researching 'Quantum Computing trends' in a new notebook"

### Query a Notebook
"What are the key takeaways from the 'Project Alpha' notebook?"
