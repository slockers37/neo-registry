---
name: n8n
description: Interact with n8n workflows via MCP - list, execute, and manage automations.
argument-hint: Use for workflow automation, triggering n8n workflows, and managing automation tasks.
---

# n8n Workflow Automation

Integration with n8n self-hosted automation platform via MCP.

## Prerequisites
User must have `n8n` MCP configured in their `opencode.json`:
```jsonc
"mcp": {
  "n8n": {
    "command": "npx",
    "args": ["-y", "n8n-mcp"],
    "env": {
      "N8N_API_URL": "${N8N_API_URL}",
      "N8N_API_KEY": "${N8N_API_KEY}"
    }
  }
}
```

Set environment variables:
- `N8N_API_URL`: Your n8n instance URL (e.g., `https://n8n.yourdomain.com/api/v1`)
- `N8N_API_KEY`: API key from n8n settings

## Usage

Use `skill_mcp` to invoke n8n operations:

### List Workflows
```
skill_mcp(mcp_name="n8n", tool_name="list_workflows")
```

### Get Workflow Details
```
skill_mcp(mcp_name="n8n", tool_name="get_workflow", arguments='{"id": "workflow-id"}')
```

### Execute Workflow
```
skill_mcp(mcp_name="n8n", tool_name="execute_workflow", arguments='{"id": "workflow-id", "data": {"key": "value"}}')
```

### Get Executions
```
skill_mcp(mcp_name="n8n", tool_name="get_executions", arguments='{"workflow_id": "...", "limit": 10}')
```

### Activate/Deactivate Workflow
```
skill_mcp(mcp_name="n8n", tool_name="activate_workflow", arguments='{"id": "workflow-id"}')
skill_mcp(mcp_name="n8n", tool_name="deactivate_workflow", arguments='{"id": "workflow-id"}')
```

## Common Use Cases

| Task | Approach |
|------|----------|
| Trigger automation | `execute_workflow` with input data |
| Check workflow status | `get_executions` to see recent runs |
| Debug failed runs | `get_execution` with execution ID |
| Batch operations | Loop through `list_workflows` results |

## Security
- API credentials configured via environment variables
- Never hardcode keys - they stay in user's local config
- Use n8n's built-in permissions for workflow access control
