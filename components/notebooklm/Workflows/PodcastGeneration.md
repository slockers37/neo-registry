# Podcast Generation Workflow

## Purpose
Streamlined process to go from a URL or Topic to a finished Audio Overview (Podcast).

## Steps

1. **Initialize**
   - Check if a relevant notebook exists (`notebook_list`).
   - If not, create one: `notebook_create(title="Podcast: [Topic]")`.

2. **Ingest**
   - Add the target content: `notebook_add_url(notebookId=..., url=...)`.
   - Validate source is added (check output).

3. **Generate**
   - Initiate audio generation: `audio_overview_create(notebookId=...)`.
   - Store the `generationId`.

4. **Monitor**
   - Loop every 30 seconds:
     - Call `studio_status(generationId=...)`.
     - If `status` == "completed", break loop.
     - If `status` == "failed", report error.

5. **Deliver**
   - Retrieve the audio URL/content from the final status.
   - Present to user.
