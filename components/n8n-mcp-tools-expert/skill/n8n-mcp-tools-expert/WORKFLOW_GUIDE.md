# Workflow Management Tools Guide

Complete guide for creating, updating, and managing n8n workflows via mcporter.

---

## Tool Availability

**Requires n8n API**: All tools in this guide need `N8N_API_URL` and `N8N_API_KEY` configured.

If unavailable, use template examples and validation-only workflows.

---

## n8n_create_workflow

**Speed**: 100-500ms

**Use when**: Creating new workflows from scratch

```bash
npx mcporter call n8n.n8n_create_workflow \
  name="Webhook to Slack" \
  nodes='[{"id":"webhook-1","name":"Webhook","type":"n8n-nodes-base.webhook","typeVersion":2,"position":[250,300],"parameters":{"path":"slack-notify","httpMethod":"POST"}},{"id":"slack-1","name":"Slack","type":"n8n-nodes-base.slack","typeVersion":2,"position":[450,300],"parameters":{"resource":"message","operation":"post","channel":"#general","text":"={{$json.body.message}}"}}]' \
  connections='{"Webhook":{"main":[[{"node":"Slack","type":"main","index":0}]]}}'
```

**Notes**:
- Workflows created **inactive** (activate with `activateWorkflow` operation)
- Auto-sanitization runs on creation
- Validate before creating for best results
- Use FULL nodeType prefix: `n8n-nodes-base.slack` (not `nodes-base.slack`)

---

## n8n_update_partial_workflow (MOST USED!)

**Speed**: 50-200ms | **Uses**: 38,287 (most used tool!)

**Use when**: Making incremental changes to workflows

**Common pattern**: 56s average between edits (iterative building!)

### 17 Operation Types

**Node Operations** (6 types):
1. `addNode` - Add new node
2. `removeNode` - Remove node by ID or name
3. `updateNode` - Update node properties (use dot notation)
4. `moveNode` - Change position
5. `enableNode` - Enable disabled node
6. `disableNode` - Disable active node

**Connection Operations** (5 types):
7. `addConnection` - Connect nodes (supports smart params)
8. `removeConnection` - Remove connection (supports ignoreErrors)
9. `rewireConnection` - Change connection target
10. `cleanStaleConnections` - Auto-remove broken connections
11. `replaceConnections` - Replace entire connections object

**Metadata Operations** (4 types):
12. `updateSettings` - Workflow settings
13. `updateName` - Rename workflow
14. `addTag` - Add tag
15. `removeTag` - Remove tag

**Activation Operations** (2 types):
16. `activateWorkflow` - Activate workflow for automatic execution
17. `deactivateWorkflow` - Deactivate workflow

### Intent Parameter (IMPORTANT!)

Always include `intent` for better responses:

```bash
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  intent="Add error handling for API failures" \
  operations='[...]'
```

### Smart Parameters

**IF nodes** - Use semantic branch names:
```json
{
  "type": "addConnection",
  "source": "IF",
  "target": "True Handler",
  "branch": "true"
}
```

**Switch nodes** - Use semantic case numbers:
```json
{
  "type": "addConnection",
  "source": "Switch",
  "target": "Handler A",
  "case": 0
}
```

### AI Connection Types (8 types)

```json
{
  "type": "addConnection",
  "source": "OpenAI Chat Model",
  "target": "AI Agent",
  "sourceOutput": "ai_languageModel"
}
```

All 8 types:
- `ai_languageModel`
- `ai_tool`
- `ai_memory`
- `ai_outputParser`
- `ai_embedding`
- `ai_vectorStore`
- `ai_document`
- `ai_textSplitter`

### Example Usage

```bash
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  intent="Add transform node after IF condition" \
  operations='[{"type":"addNode","node":{"name":"Transform","type":"n8n-nodes-base.set","position":[400,300],"parameters":{}}},{"type":"addConnection","source":"IF","target":"Transform","branch":"true"}]'
```

### Activation Operations

```bash
# Activate workflow
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  intent="Activate workflow for production" \
  operations='[{"type":"activateWorkflow"}]'

# Deactivate workflow
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  intent="Deactivate workflow for maintenance" \
  operations='[{"type":"deactivateWorkflow"}]'
```

### Cleanup & Recovery

```bash
# Clean stale connections
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  operations='[{"type":"cleanStaleConnections"}]'

# Validate before applying
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  operations='[...]' \
  validateOnly=true
```

---

## n8n_deploy_template (QUICK START!)

**Speed**: 200-500ms

**Use when**: Deploying a template directly to n8n instance

```bash
npx mcporter call n8n.n8n_deploy_template \
  templateId=2947 \
  name="My Weather to Slack" \
  autoFix=true \
  autoUpgradeVersions=true \
  stripCredentials=true
```

**Returns**:
- Workflow ID
- Required credentials
- Fixes applied

---

## n8n_workflow_versions (VERSION CONTROL)

**Use when**: Managing workflow history, rollback, cleanup

```bash
# List versions
npx mcporter call n8n.n8n_workflow_versions \
  mode=list \
  workflowId=workflow-id \
  limit=10

# Get specific version
npx mcporter call n8n.n8n_workflow_versions \
  mode=get \
  versionId=123

# Rollback to previous version
npx mcporter call n8n.n8n_workflow_versions \
  mode=rollback \
  workflowId=workflow-id \
  versionId=123 \
  validateBefore=true

# Prune old versions
npx mcporter call n8n.n8n_workflow_versions \
  mode=prune \
  workflowId=workflow-id \
  maxVersions=10
```

---

## n8n_test_workflow (TRIGGER EXECUTION)

**Use when**: Testing workflow execution

**Auto-detects** trigger type (webhook, form, chat)

```bash
# Test webhook workflow
npx mcporter call n8n.n8n_test_workflow \
  workflowId=workflow-id \
  triggerType=webhook \
  httpMethod=POST \
  data='{"message":"Hello!"}' \
  waitForResponse=true \
  timeout=120000

# Test chat workflow
npx mcporter call n8n.n8n_test_workflow \
  workflowId=workflow-id \
  triggerType=chat \
  message="Hello, AI agent!" \
  sessionId=session-123
```

---

## n8n_validate_workflow (by ID)

**Use when**: Validating workflow stored in n8n

```bash
npx mcporter call n8n.n8n_validate_workflow \
  id=workflow-id \
  options='{"validateNodes":true,"validateConnections":true,"validateExpressions":true,"profile":"runtime"}'
```

---

## n8n_get_workflow

**Use when**: Retrieving workflow details

**Modes**:
- `full` (default) - Complete workflow JSON
- `details` - Full + execution stats
- `structure` - Nodes + connections only
- `minimal` - ID, name, active, tags

```bash
# Full workflow
npx mcporter call "n8n.n8n_get_workflow(id: 'workflow-id')"

# Just structure
npx mcporter call "n8n.n8n_get_workflow(id: 'workflow-id', mode: 'structure')"

# Minimal metadata
npx mcporter call "n8n.n8n_get_workflow(id: 'workflow-id', mode: 'minimal')"
```

---

## n8n_executions (EXECUTION MANAGEMENT)

**Use when**: Managing workflow executions

```bash
# Get execution details
npx mcporter call n8n.n8n_executions \
  action=get \
  id=execution-id \
  mode=summary

# Error mode for debugging
npx mcporter call n8n.n8n_executions \
  action=get \
  id=execution-id \
  mode=error \
  includeStackTrace=true

# List executions
npx mcporter call n8n.n8n_executions \
  action=list \
  workflowId=workflow-id \
  status=error \
  limit=100

# Delete execution
npx mcporter call n8n.n8n_executions \
  action=delete \
  id=execution-id
```

---

## Workflow Lifecycle

**Standard pattern**:
```
1. CREATE
   npx mcporter call n8n.n8n_create_workflow name="..." nodes='[...]' connections='{...}'
   → Returns workflow ID

2. VALIDATE
   npx mcporter call n8n.n8n_validate_workflow id=workflow-id
   → Check for errors

3. EDIT (iterative! 56s avg between edits)
   npx mcporter call n8n.n8n_update_partial_workflow id=... intent="..." operations='[...]'
   → Make changes

4. VALIDATE AGAIN
   npx mcporter call n8n.n8n_validate_workflow id=workflow-id
   → Verify changes

5. ACTIVATE
   npx mcporter call n8n.n8n_update_partial_workflow id=... operations='[{"type":"activateWorkflow"}]'
   → Workflow now runs on triggers!

6. MONITOR
   npx mcporter call n8n.n8n_executions action=list workflowId=...
   npx mcporter call n8n.n8n_executions action=get id=execution-id
```

---

## Common Patterns from Telemetry

### Pattern 1: Edit → Validate (7,841 occurrences)
```bash
npx mcporter call n8n.n8n_update_partial_workflow ...
# ↓ 23s (thinking about what to validate)
npx mcporter call n8n.n8n_validate_workflow id=...
```

### Pattern 2: Validate → Fix (7,266 occurrences)
```bash
npx mcporter call n8n.n8n_validate_workflow id=...
# ↓ 58s (fixing errors)
npx mcporter call n8n.n8n_update_partial_workflow ...
```

### Pattern 3: Iterative Building (31,464 occurrences)
```
update → update → update → ... (56s avg between edits)
```

**This shows**: Workflows are built **iteratively**, not in one shot!

---

## Best Practices

### Do

- Build workflows **iteratively** (avg 56s between edits)
- Include **intent** parameter for better responses
- Use **smart parameters** (branch, case) for clarity
- Validate **after** significant changes
- Use **atomic mode** (default) for critical updates
- Specify **sourceOutput** for AI connections
- Clean stale connections after node renames/deletions
- Use `n8n_deploy_template` for quick starts
- Activate workflows via API when ready

### Don't

- Try to build workflows in one shot
- Skip the intent parameter
- Use sourceIndex when branch/case available
- Skip validation before activation
- Forget to test workflows after creation
- Ignore auto-sanitization behavior

---

## Summary

**Most Important**:
1. **n8n_update_partial_workflow** is most-used tool (38,287 uses)
2. Include **intent** parameter for better responses
3. Workflows built **iteratively** (56s avg between edits)
4. Use **smart parameters** (branch="true", case=0) for clarity
5. **AI connections** supported (8 types with sourceOutput)
6. **Workflow activation** supported via API (`activateWorkflow` operation)
7. **Auto-sanitization** runs on all operations
8. Use **n8n_deploy_template** for quick starts

**New Tools**:
- `n8n_deploy_template` - Deploy templates directly
- `n8n_workflow_versions` - Version control & rollback
- `n8n_test_workflow` - Trigger execution
- `n8n_executions` - Manage executions

**Related**:
- [SEARCH_GUIDE.md](SEARCH_GUIDE.md) - Find nodes to add
- [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md) - Validate workflows
