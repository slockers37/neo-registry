---
name: n8n-workflow-patterns
description: Proven workflow architectural patterns from real n8n workflows. Use when building new workflows, designing workflow structure, choosing workflow patterns, planning workflow architecture, or asking about webhook processing, HTTP API integration, database operations, AI agent workflows, or scheduled tasks.
---

# n8n Workflow Patterns

Proven architectural patterns for building n8n workflows.

---

## The 5 Core Patterns

Based on analysis of real workflow usage:

1. **Webhook Processing** (Most Common)
   - Receive HTTP requests → Process → Output
   - Pattern: Webhook → Validate → Transform → Respond/Notify

2. **HTTP API Integration**
   - Fetch from REST APIs → Transform → Store/Use
   - Pattern: Trigger → HTTP Request → Transform → Action → Error Handler

3. **Database Operations**
   - Read/Write/Sync database data
   - Pattern: Schedule → Query → Transform → Write → Verify

4. **AI Agent Workflow**
   - AI agents with tools and memory
   - Pattern: Trigger → AI Agent (Model + Tools + Memory) → Output

5. **Scheduled Tasks**
   - Recurring automation workflows
   - Pattern: Schedule → Fetch → Process → Deliver → Log

---

## Pattern Selection Guide

**Webhook Processing** - Use when:
- Receiving data from external systems
- Building integrations (Slack commands, form submissions, GitHub webhooks)
- Need instant response to events

**HTTP API Integration** - Use when:
- Fetching data from external APIs
- Synchronizing with third-party services
- Building data pipelines

**Database Operations** - Use when:
- Syncing between databases
- Running database queries on schedule
- ETL workflows

**AI Agent Workflow** - Use when:
- Building conversational AI
- Need AI with tool access
- Multi-step reasoning tasks

**Scheduled Tasks** - Use when:
- Recurring reports or summaries
- Periodic data fetching
- Maintenance tasks

---

## Workflow Creation Checklist

### Planning Phase
- [ ] Identify the pattern (webhook, API, database, AI, scheduled)
- [ ] List required nodes:
  ```bash
  npx mcporter call "n8n.search_nodes(query: 'slack')"
  ```
- [ ] Understand data flow (input → transform → output)
- [ ] Plan error handling strategy

### Implementation Phase
- [ ] Create workflow with appropriate trigger
- [ ] Add data source nodes
- [ ] Configure authentication/credentials
- [ ] Add transformation nodes (Set, Code, IF)
- [ ] Add output/action nodes
- [ ] Configure error handling

### Validation Phase
- [ ] Validate each node:
  ```bash
  npx mcporter call n8n.validate_node nodeType=... config='{...}' profile=runtime
  ```
- [ ] Validate complete workflow:
  ```bash
  npx mcporter call "n8n.n8n_validate_workflow(id: 'workflow-id')"
  ```
- [ ] Test with sample data
- [ ] Handle edge cases (empty data, errors)

### Deployment Phase
- [ ] Review workflow settings
- [ ] Activate workflow:
  ```bash
  npx mcporter call n8n.n8n_update_partial_workflow id=workflow-id operations='[{"type":"activateWorkflow"}]'
  ```
- [ ] Monitor first executions
- [ ] Document workflow purpose and data flow

---

## Data Flow Patterns

### Linear Flow
```
Trigger → Transform → Action → End
```
**Use when**: Simple workflows with single path

### Branching Flow
```
Trigger → IF → [True Path]
             └→ [False Path]
```
**Use when**: Different actions based on conditions

### Parallel Processing
```
Trigger → [Branch 1] → Merge
       └→ [Branch 2] ↗
```
**Use when**: Independent operations that can run simultaneously

### Loop Pattern
```
Trigger → Split in Batches → Process → Loop (until done)
```
**Use when**: Processing large datasets in chunks

### Error Handler Pattern
```
Main Flow → [Success Path]
         └→ [Error Trigger → Error Handler]
```
**Use when**: Need separate error handling workflow

---

## Common Gotchas

### 1. Webhook Data Structure
**Problem**: Can't access webhook payload data

**Solution**: Data is nested under `$json.body`
```javascript
❌ {{$json.email}}
✅ {{$json.body.email}}
```

### 2. Multiple Input Items
**Problem**: Node processes all input items, but I only want one

**Solution**: Use "Execute Once" mode or process first item only

### 3. Authentication Issues
**Problem**: API calls failing with 401/403

**Solution**: Configure credentials properly in node settings

### 4. Expression Errors
**Problem**: Expressions showing as literal text

**Solution**: Use {{}} around expressions

---

## Quick Start Examples

### Example 1: Simple Webhook → Slack
```
1. Webhook (path: "form-submit", POST)
2. Set (map form fields)
3. Slack (post message to #notifications)
```

### Example 2: Scheduled Report
```
1. Schedule (daily at 9 AM)
2. HTTP Request (fetch analytics)
3. Code (aggregate data)
4. Email (send formatted report)
5. Error Trigger → Slack (notify on failure)
```

### Example 3: Database Sync
```
1. Schedule (every 15 minutes)
2. Postgres (query new records)
3. IF (check if records exist)
4. MySQL (insert records)
5. Postgres (update sync timestamp)
```

### Example 4: AI Assistant
```
1. Webhook (receive chat message)
2. AI Agent
   ├─ OpenAI Chat Model (ai_languageModel)
   ├─ HTTP Request Tool (ai_tool)
   └─ Window Buffer Memory (ai_memory)
3. Webhook Response (send AI reply)
```

---

## Using Templates

### Search Templates

```bash
npx mcporter call "n8n.search_templates(query: 'webhook slack', limit: 20)"
```

### Get Template Details

```bash
npx mcporter call "n8n.get_template(templateId: 2947, mode: 'structure')"
```

### Deploy Template

```bash
npx mcporter call n8n.n8n_deploy_template templateId=2947 name="My Workflow" autoFix=true
```

---

## Best Practices

### ✅ Do

- Start with the simplest pattern that solves your problem
- Plan your workflow structure before building
- Use error handling on all workflows
- Test with sample data before activation
- Follow the workflow creation checklist
- Use descriptive node names
- Monitor workflow executions after deployment
- Build workflows iteratively (avg 56s between edits)

### ❌ Don't

- Build workflows in one shot (iterate!)
- Skip validation before activation
- Ignore error scenarios
- Use complex patterns when simple ones suffice
- Hardcode credentials in parameters
- Forget to handle empty data cases
- Deploy without testing

---

## Summary

**Key Points**:
1. **5 core patterns** cover 90%+ of workflow use cases
2. **Webhook processing** is the most common pattern
3. Use the **workflow creation checklist** for every workflow
4. **Plan pattern** → **Select nodes** → **Build** → **Validate** → **Deploy**

**Next Steps**:
1. Identify your use case pattern
2. Find nodes: `npx mcporter call "n8n.search_nodes(query: '...')"`
3. Follow the workflow creation checklist
4. Validate: `npx mcporter call n8n.validate_node ...`

**Related Skills**:
- n8n MCP Tools Expert - Find and configure nodes
- n8n Expression Syntax - Write expressions correctly
- n8n Validation Expert - Validate and fix errors
- n8n Node Configuration - Configure specific operations
