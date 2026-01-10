---
name: n8n
description: Interact with n8n workflows via MCP - list, execute, and manage automations. Use for workflow automation and triggering n8n workflows.
mcp:
  n8n:
    command: npx
    args:
      - "-y"
      - "n8n-mcp"
    env:
      N8N_API_URL: ${N8N_API_URL}
      N8N_API_KEY: ${N8N_API_KEY}
---

<objective>
Manage and trigger n8n automation workflows - list workflows, execute them with data, and monitor executions.
</objective>

<prerequisites>
Set environment variables before use:
```bash
export N8N_API_URL=https://your-n8n-instance.com/api/v1
export N8N_API_KEY=your-api-key-from-n8n-settings
```
</prerequisites>

<tools>

## list_workflows
List all workflows in your n8n instance.
```
skill_mcp(mcp_name="n8n", tool_name="list_workflows")
```

## get_workflow
Get details of a specific workflow.
```
skill_mcp(mcp_name="n8n", tool_name="get_workflow", arguments='{"id": "workflow-id"}')
```

## execute_workflow
Execute a workflow with optional input data.
```
skill_mcp(mcp_name="n8n", tool_name="execute_workflow", arguments='{"id": "workflow-id", "data": {"key": "value"}}')
```

| Parameter | Purpose |
|-----------|---------|
| `id` | Workflow ID to execute |
| `data` | Input data passed to workflow (optional) |

## get_executions
Get recent executions for a workflow.
```
skill_mcp(mcp_name="n8n", tool_name="get_executions", arguments='{"workflow_id": "...", "limit": 10}')
```

## activate_workflow / deactivate_workflow
Toggle workflow active state.
```
skill_mcp(mcp_name="n8n", tool_name="activate_workflow", arguments='{"id": "workflow-id"}')
skill_mcp(mcp_name="n8n", tool_name="deactivate_workflow", arguments='{"id": "workflow-id"}')
```

</tools>

<common_use_cases>
| Task | Approach |
|------|----------|
| Trigger automation | `execute_workflow` with input data |
| Check workflow status | `get_executions` to see recent runs |
| Debug failed runs | `get_executions` then inspect specific execution |
| Enable/disable automation | `activate_workflow` / `deactivate_workflow` |
</common_use_cases>

<success_criteria>
- Workflow list returns without auth errors
- Workflow execution starts and returns execution ID
- Executions show status (success/failed/running)
</success_criteria>
