---
name: n8n
description: Interact with n8n workflows via MCP - list, execute, and manage automations. Use for workflow automation and triggering n8n workflows.
---

# n8n Workflow Management

Manage and trigger n8n automation workflows via mcporter.

## Prerequisites

Ensure environment variables are set:
```bash
export N8N_API_URL=https://your-n8n-instance.com/api/v1
export N8N_API_KEY=your-api-key-from-n8n-settings
```

And n8n is configured in mcporter (`~/.mcporter/mcporter.json`).

## Available Tools

Discover all available n8n tools:
```bash
npx mcporter list n8n --schema
```

## Common Operations

### List Workflows

```bash
npx mcporter call n8n.n8n_list_workflows
```

### Get Workflow Details

```bash
npx mcporter call "n8n.n8n_get_workflow(id: 'workflow-id')"
```

### Execute Workflow

```bash
npx mcporter call n8n.n8n_test_workflow \
  workflowId=workflow-id \
  data='{"key": "value"}'
```

| Parameter | Purpose |
|-----------|---------|
| `workflowId` | Workflow ID to execute |
| `data` | Input data passed to workflow (optional) |

### Get Executions

```bash
npx mcporter call n8n.n8n_executions \
  action=list \
  workflowId=workflow-id \
  limit=10
```

### Activate/Deactivate Workflow

```bash
# Activate
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  operations='[{"type":"activateWorkflow"}]'

# Deactivate
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  operations='[{"type":"deactivateWorkflow"}]'
```

## Common Use Cases

| Task | Approach |
|------|----------|
| Trigger automation | `n8n_test_workflow` with input data |
| Check workflow status | `n8n_executions` to see recent runs |
| Debug failed runs | `n8n_executions` then inspect specific execution |
| Enable/disable automation | `n8n_update_partial_workflow` with activate/deactivate |

## Success Criteria

- Workflow list returns without auth errors
- Workflow execution starts and returns execution ID
- Executions show status (success/failed/running)

## Related Skills

- **n8n MCP Tools Expert** - Comprehensive tool usage guide
- **n8n Workflow Patterns** - Architectural patterns
- **n8n Validation Expert** - Validate workflows
