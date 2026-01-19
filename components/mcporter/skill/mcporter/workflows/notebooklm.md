# NotebookLM Operations

<objective>
Use NotebookLM MCP for notebook management, source ingestion, podcast generation, deep research, and RAG chat.
</objective>

<available_tools>
| Tool | Description |
|------|-------------|
| `list_notebooks` | List all notebooks |
| `create_notebook` | Create a new notebook |
| `add_source` | Add URL/text/Drive source to notebook |
| `generate_audio_overview` | Generate podcast from notebook |
| `research` | Deep research using notebook sources |
| `chat` | RAG chat with notebook content |
</available_tools>

<process>
## List Notebooks

```bash
npx mcporter call notebooklm.list_notebooks
```

## Create a Notebook

```bash
npx mcporter call notebooklm.create_notebook title="My Research Project"
```

## Add Sources

Add URL:
```bash
npx mcporter call notebooklm.add_source \
  notebook_id="<id>" \
  url="https://example.com/article"
```

Add text:
```bash
npx mcporter call notebooklm.add_source \
  notebook_id="<id>" \
  text="Your content here..."
```

## Generate Podcast (Audio Overview)

```bash
npx mcporter call notebooklm.generate_audio_overview \
  notebook_id="<id>"
```

Options:
```bash
npx mcporter call notebooklm.generate_audio_overview \
  notebook_id="<id>" \
  instructions="Focus on practical applications, keep it conversational"
```

## Deep Research

```bash
npx mcporter call notebooklm.research \
  notebook_id="<id>" \
  query="What are the key trends in this space?"
```

## Chat with Notebook

```bash
npx mcporter call notebooklm.chat \
  notebook_id="<id>" \
  message="Summarize the main findings"
```
</process>

<common_patterns>
**Full research workflow:**
```bash
# 1. Create notebook
npx mcporter call notebooklm.create_notebook title="AI Research 2024"

# 2. Add sources (use notebook_id from step 1)
npx mcporter call notebooklm.add_source notebook_id="<id>" url="https://..."
npx mcporter call notebooklm.add_source notebook_id="<id>" url="https://..."

# 3. Research
npx mcporter call notebooklm.research notebook_id="<id>" query="Key takeaways"

# 4. Generate podcast
npx mcporter call notebooklm.generate_audio_overview notebook_id="<id>"
```

**Quick chat:**
```bash
npx mcporter call notebooklm.chat notebook_id="<id>" message="What does this say about X?"
```
</common_patterns>

<success_criteria>
- Notebooks created and accessible
- Sources ingested successfully
- Podcast generated (check for audio URL in response)
- Research returns relevant insights from sources
</success_criteria>
