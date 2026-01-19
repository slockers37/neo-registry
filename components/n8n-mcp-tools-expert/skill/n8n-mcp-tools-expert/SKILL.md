---
name: n8n-mcp-tools-expert
description: Expert guide for using n8n-mcp MCP tools via mcporter. Use when searching for nodes, validating configurations, accessing templates, managing workflows, or using any n8n-mcp tool. Provides tool selection guidance, parameter formats, and common patterns.
---

# n8n MCP Tools Expert

Master guide for using n8n-mcp MCP server tools to build workflows.

## Calling n8n Tools via mcporter

All n8n MCP tools are called through mcporter CLI:

```bash
# Discover available tools
npx mcporter list n8n --schema

# Call a tool
npx mcporter call "n8n.<tool_name>(param: value)"
# OR
npx mcporter call n8n.<tool_name> param=value
```

**Examples:**
```bash
# Search for nodes
npx mcporter call "n8n.search_nodes(query: 'slack')"

# Get node details
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack')"

# Validate configuration
npx mcporter call n8n.validate_node nodeType=nodes-base.slack config='{"resource":"channel"}' profile=runtime
```

---

## Tool Categories

n8n-mcp provides tools organized into categories:

1. **Node Discovery** - [SEARCH_GUIDE.md](SEARCH_GUIDE.md)
2. **Configuration Validation** - [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md)
3. **Workflow Management** - [WORKFLOW_GUIDE.md](WORKFLOW_GUIDE.md)
4. **Template Library** - Search and deploy 2,700+ real workflows
5. **Documentation & Guides** - Tool docs, AI agent guide, Code node guides

---

## Quick Reference

### Most Used Tools (by success rate)

| Tool | Use When | Speed |
|------|----------|-------|
| `search_nodes` | Finding nodes by keyword | <20ms |
| `get_node` | Understanding node operations (detail="standard") | <10ms |
| `validate_node` | Checking configurations (mode="full") | <100ms |
| `n8n_create_workflow` | Creating workflows | 100-500ms |
| `n8n_update_partial_workflow` | Editing workflows (MOST USED!) | 50-200ms |
| `validate_workflow` | Checking complete workflow | 100-500ms |
| `n8n_deploy_template` | Deploy template to n8n instance | 200-500ms |

---

## Tool Selection Guide

### Finding the Right Node

**Workflow**:
```bash
# 1. Search
npx mcporter call "n8n.search_nodes(query: 'keyword')"

# 2. Get details
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.name')"

# 3. [Optional] Get docs
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.name', mode: 'docs')"
```

**Example**:
```bash
# Step 1: Search
npx mcporter call "n8n.search_nodes(query: 'slack')"
# Returns: nodes-base.slack

# Step 2: Get details
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack')"
# Returns: operations, properties, examples (standard detail)

# Step 3: Get readable documentation
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack', mode: 'docs')"
# Returns: markdown documentation
```

**Common pattern**: search -> get_node (18s average)

### Validating Configuration

**Workflow**:
```bash
# 1. Check required fields
npx mcporter call n8n.validate_node nodeType=nodes-base.slack config='{}' mode=minimal

# 2. Full validation
npx mcporter call n8n.validate_node nodeType=nodes-base.slack config='{"resource":"channel"}' profile=runtime

# 3. Fix errors, validate again
```

**Common pattern**: validate -> fix -> validate (23s thinking, 58s fixing per cycle)

### Managing Workflows

**Workflow**:
```bash
# 1. Create workflow
npx mcporter call n8n.n8n_create_workflow name="My Workflow" nodes='[...]' connections='{}'

# 2. Validate
npx mcporter call "n8n.n8n_validate_workflow(id: 'workflow-id')"

# 3. Update iteratively
npx mcporter call n8n.n8n_update_partial_workflow id=workflow-id operations='[{"type":"addNode","node":{...}}]'

# 4. Validate again
npx mcporter call "n8n.n8n_validate_workflow(id: 'workflow-id')"

# 5. Activate
npx mcporter call n8n.n8n_update_partial_workflow id=workflow-id operations='[{"type":"activateWorkflow"}]'
```

**Common pattern**: iterative updates (56s average between edits)

---

## Critical: nodeType Formats

**Two different formats** for different tools!

### Format 1: Search/Validate Tools
```
# Use SHORT prefix
nodes-base.slack
nodes-base.httpRequest
nodes-base.webhook
nodes-langchain.agent
```

**Tools that use this**:
- search_nodes (returns this format)
- get_node
- validate_node
- validate_workflow

### Format 2: Workflow Tools
```
# Use FULL prefix
n8n-nodes-base.slack
n8n-nodes-base.httpRequest
n8n-nodes-base.webhook
@n8n/n8n-nodes-langchain.agent
```

**Tools that use this**:
- n8n_create_workflow
- n8n_update_partial_workflow

### Conversion

search_nodes returns BOTH formats:
```json
{
  "nodeType": "nodes-base.slack",           // For search/validate tools
  "workflowNodeType": "n8n-nodes-base.slack"  // For workflow tools
}
```

---

## Common Mistakes

### Mistake 1: Wrong nodeType Format

**Problem**: "Node not found" error

```bash
# WRONG
npx mcporter call "n8n.get_node(nodeType: 'slack')"  # Missing prefix
npx mcporter call "n8n.get_node(nodeType: 'n8n-nodes-base.slack')"  # Wrong prefix

# CORRECT
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack')"
```

### Mistake 2: Using detail="full" by Default

**Problem**: Huge payload, slower response, token waste

```bash
# WRONG - Returns 3-8K tokens, use sparingly
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack', detail: 'full')"

# CORRECT - Returns 1-2K tokens, covers 95% of use cases
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack')"  # detail="standard" is default
```

**When to use detail="full"**:
- Debugging complex configuration issues
- Need complete property schema with all nested options
- Exploring advanced features

**Better alternatives**:
```bash
# For operations list (default)
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack', detail: 'standard')"

# For readable documentation
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack', mode: 'docs')"

# For specific property
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.httpRequest', mode: 'search_properties', propertyQuery: 'auth')"
```

### Mistake 3: Not Using Validation Profiles

**Problem**: Too many false positives OR missing real errors

**Profiles**:
- `minimal` - Only required fields (fast, permissive)
- `runtime` - Values + types (recommended for pre-deployment)
- `ai-friendly` - Reduce false positives (for AI configuration)
- `strict` - Maximum validation (for production)

```bash
# WRONG - Uses default profile
npx mcporter call n8n.validate_node nodeType=nodes-base.slack config='{...}'

# CORRECT - Explicit profile
npx mcporter call n8n.validate_node nodeType=nodes-base.slack config='{...}' profile=runtime
```

### Mistake 4: Ignoring Auto-Sanitization

**What happens**: ALL nodes sanitized on ANY workflow update

**Auto-fixes**:
- Binary operators (equals, contains) -> removes singleValue
- Unary operators (isEmpty, isNotEmpty) -> adds singleValue: true
- IF/Switch nodes -> adds missing metadata

**Cannot fix**:
- Broken connections
- Branch count mismatches
- Paradoxical corrupt states

```bash
# After ANY update, auto-sanitization runs on ALL nodes
npx mcporter call n8n.n8n_update_partial_workflow id=workflow-id operations='[...]'
# -> Automatically fixes operator structures
```

### Mistake 5: Not Using Smart Parameters

**Problem**: Complex sourceIndex calculations for multi-output nodes

**Old way** (manual):
```json
{
  "type": "addConnection",
  "source": "IF",
  "target": "Handler",
  "sourceIndex": 0
}
```

**New way** (smart parameters):
```json
{
  "type": "addConnection",
  "source": "IF",
  "target": "True Handler",
  "branch": "true"
}
```

```json
{
  "type": "addConnection",
  "source": "Switch",
  "target": "Handler A",
  "case": 0
}
```

### Mistake 6: Not Using intent Parameter

**Problem**: Less helpful tool responses

```bash
# WRONG - No context for response
npx mcporter call n8n.n8n_update_partial_workflow id=abc operations='[{"type":"addNode","node":{...}}]'

# CORRECT - Better AI responses
npx mcporter call n8n.n8n_update_partial_workflow id=abc intent="Add error handling for API failures" operations='[{"type":"addNode","node":{...}}]'
```

---

## Tool Usage Patterns

### Pattern 1: Node Discovery (Most Common)

**Common workflow**: 18s average between steps

```bash
# Step 1: Search (fast!)
npx mcporter call "n8n.search_nodes(query: 'slack', mode: 'OR', limit: 20)"
# -> Returns: nodes-base.slack, nodes-base.slackTrigger

# Step 2: Get details (~18s later, user reviewing results)
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack', includeExamples: true)"
# -> Returns: operations, properties, metadata
```

### Pattern 2: Validation Loop

**Typical cycle**: 23s thinking, 58s fixing

```bash
# Step 1: Validate
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{"resource":"channel","operation":"create"}' \
  profile=runtime

# Step 2: Check errors (~23s thinking)
# If errors: "Missing required field: name"

# Step 3: Fix config (~58s fixing)
# Add the missing field

# Step 4: Validate again
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{"resource":"channel","operation":"create","name":"general"}' \
  profile=runtime
```

### Pattern 3: Workflow Editing

**Most used update tool**: 99.0% success rate, 56s average between edits

```bash
# Iterative workflow building (NOT one-shot!)

# Edit 1
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  intent="Add webhook trigger" \
  operations='[{"type":"addNode","node":{...}}]'

# ~56s later...

# Edit 2
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  intent="Connect webhook to processor" \
  operations='[{"type":"addConnection","source":"...","target":"..."}]'

# ~56s later...

# Edit 3 (validation)
npx mcporter call "n8n.n8n_validate_workflow(id: 'workflow-id')"

# Ready? Activate!
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  intent="Activate workflow for production" \
  operations='[{"type":"activateWorkflow"}]'
```

---

## Detailed Guides

### Node Discovery Tools
See [SEARCH_GUIDE.md](SEARCH_GUIDE.md) for:
- search_nodes
- get_node with detail levels (minimal, standard, full)
- get_node modes (info, docs, search_properties, versions)

### Validation Tools
See [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md) for:
- Validation profiles explained
- validate_node with modes (minimal, full)
- validate_workflow complete structure
- Auto-sanitization system
- Handling validation errors

### Workflow Management
See [WORKFLOW_GUIDE.md](WORKFLOW_GUIDE.md) for:
- n8n_create_workflow
- n8n_update_partial_workflow (17 operation types!)
- Smart parameters (branch, case)
- AI connection types (8 types)
- Workflow activation (activateWorkflow/deactivateWorkflow)
- n8n_deploy_template
- n8n_workflow_versions

---

## Template Usage

### Search Templates

```bash
# Search by keyword (default mode)
npx mcporter call "n8n.search_templates(query: 'webhook slack', limit: 20)"

# Search by node types
npx mcporter call n8n.search_templates \
  searchMode=by_nodes \
  nodeTypes='["n8n-nodes-base.httpRequest","n8n-nodes-base.slack"]'

# Search by task type
npx mcporter call "n8n.search_templates(searchMode: 'by_task', task: 'webhook_processing')"

# Search by metadata (complexity, setup time)
npx mcporter call "n8n.search_templates(searchMode: 'by_metadata', complexity: 'simple', maxSetupMinutes: 15)"
```

### Get Template Details

```bash
# Structure only (nodes+connections)
npx mcporter call "n8n.get_template(templateId: 2947, mode: 'structure')"

# Full workflow JSON
npx mcporter call "n8n.get_template(templateId: 2947, mode: 'full')"
```

### Deploy Template Directly

```bash
# Deploy template to your n8n instance
npx mcporter call n8n.n8n_deploy_template \
  templateId=2947 \
  name="My Weather to Slack" \
  autoFix=true \
  autoUpgradeVersions=true
# Returns: workflow ID, required credentials, fixes applied
```

---

## Self-Help Tools

### Get Tool Documentation

```bash
# Overview of all tools
npx mcporter call n8n.tools_documentation

# Specific tool details
npx mcporter call "n8n.tools_documentation(topic: 'search_nodes', depth: 'full')"

# Code node guides
npx mcporter call "n8n.tools_documentation(topic: 'javascript_code_node_guide', depth: 'full')"
npx mcporter call "n8n.tools_documentation(topic: 'python_code_node_guide', depth: 'full')"
```

### AI Agent Guide

```bash
# Comprehensive AI workflow guide
npx mcporter call n8n.ai_agents_guide
# Returns: Architecture, connections, tools, validation, best practices
```

### Health Check

```bash
# Quick health check
npx mcporter call n8n.n8n_health_check

# Detailed diagnostics
npx mcporter call "n8n.n8n_health_check(mode: 'diagnostic')"
# -> Returns: status, env vars, tool status, API connectivity
```

---

## Tool Availability

**Always Available** (no n8n API needed):
- search_nodes, get_node
- validate_node, validate_workflow
- search_templates, get_template
- tools_documentation, ai_agents_guide

**Requires n8n API** (N8N_API_URL + N8N_API_KEY):
- n8n_create_workflow
- n8n_update_partial_workflow
- n8n_validate_workflow (by ID)
- n8n_list_workflows, n8n_get_workflow
- n8n_test_workflow
- n8n_executions
- n8n_deploy_template
- n8n_workflow_versions
- n8n_autofix_workflow

If API tools unavailable, use templates and validation-only workflows.

---

## Unified Tool Reference

### get_node (Unified Node Information)

**Detail Levels** (mode="info", default):
- `minimal` (~200 tokens) - Basic metadata only
- `standard` (~1-2K tokens) - Essential properties + operations (RECOMMENDED)
- `full` (~3-8K tokens) - Complete schema (use sparingly)

**Operation Modes**:
- `info` (default) - Node schema with detail level
- `docs` - Readable markdown documentation
- `search_properties` - Find specific properties (use with propertyQuery)
- `versions` - List all versions with breaking changes
- `compare` - Compare two versions
- `breaking` - Show only breaking changes
- `migrations` - Show auto-migratable changes

```bash
# Standard (recommended)
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.httpRequest')"

# Get documentation
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.webhook', mode: 'docs')"

# Search for properties
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.httpRequest', mode: 'search_properties', propertyQuery: 'auth')"

# Check versions
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.executeWorkflow', mode: 'versions')"
```

### validate_node (Unified Validation)

**Modes**:
- `full` (default) - Comprehensive validation with errors/warnings/suggestions
- `minimal` - Quick required fields check only

**Profiles** (for mode="full"):
- `minimal` - Very lenient
- `runtime` - Standard (default, recommended)
- `ai-friendly` - Balanced for AI workflows
- `strict` - Most thorough (production)

```bash
# Full validation with runtime profile
npx mcporter call n8n.validate_node nodeType=nodes-base.slack config='{...}' profile=runtime

# Quick required fields check
npx mcporter call "n8n.validate_node(nodeType: 'nodes-base.webhook', config: {}, mode: 'minimal')"
```

---

## Performance Characteristics

| Tool | Response Time | Payload Size |
|------|---------------|--------------|
| search_nodes | <20ms | Small |
| get_node (standard) | <10ms | ~1-2KB |
| get_node (full) | <100ms | 3-8KB |
| validate_node (minimal) | <50ms | Small |
| validate_node (full) | <100ms | Medium |
| validate_workflow | 100-500ms | Medium |
| n8n_create_workflow | 100-500ms | Medium |
| n8n_update_partial_workflow | 50-200ms | Small |
| n8n_deploy_template | 200-500ms | Medium |

---

## Best Practices

### Do
- Use `get_node` with `detail: "standard"` for most use cases
- Specify validation profile explicitly (`profile: "runtime"`)
- Use smart parameters (`branch`, `case`) for clarity
- Include `intent` parameter in workflow updates
- Follow search -> get_node -> validate workflow
- Iterate workflows (avg 56s between edits)
- Validate after every significant change
- Use `includeExamples: true` for real configs
- Use `n8n_deploy_template` for quick starts

### Don't
- Use `detail: "full"` unless necessary (wastes tokens)
- Forget nodeType prefix (`nodes-base.*`)
- Skip validation profiles
- Try to build workflows in one shot (iterate!)
- Ignore auto-sanitization behavior
- Use full prefix (`n8n-nodes-base.*`) with search/validate tools
- Forget to activate workflows after building

---

## Summary

**Most Important**:
1. All tools called via: `npx mcporter call n8n.<tool> ...`
2. Use **get_node** with `detail: "standard"` (default) - covers 95% of use cases
3. nodeType formats differ: `nodes-base.*` (search/validate) vs `n8n-nodes-base.*` (workflows)
4. Specify **validation profiles** (`runtime` recommended)
5. Use **smart parameters** (`branch="true"`, `case=0`)
6. Include **intent parameter** in workflow updates
7. **Auto-sanitization** runs on ALL nodes during updates
8. Workflows can be **activated via API** (`activateWorkflow` operation)
9. Workflows are built **iteratively** (56s avg between edits)

**Common Workflow**:
```bash
# 1. Find node
npx mcporter call "n8n.search_nodes(query: 'slack')"

# 2. Understand config
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack')"

# 3. Check config
npx mcporter call n8n.validate_node nodeType=nodes-base.slack config='{...}' profile=runtime

# 4. Build workflow
npx mcporter call n8n.n8n_create_workflow name="My Workflow" nodes='[...]' connections='{}'

# 5. Verify
npx mcporter call "n8n.n8n_validate_workflow(id: 'workflow-id')"

# 6. Iterate
npx mcporter call n8n.n8n_update_partial_workflow id=workflow-id operations='[...]'

# 7. Go live!
npx mcporter call n8n.n8n_update_partial_workflow id=workflow-id operations='[{"type":"activateWorkflow"}]'
```

For details, see:
- [SEARCH_GUIDE.md](SEARCH_GUIDE.md) - Node discovery
- [VALIDATION_GUIDE.md](VALIDATION_GUIDE.md) - Configuration validation
- [WORKFLOW_GUIDE.md](WORKFLOW_GUIDE.md) - Workflow management

---

**Related Skills**:
- n8n Expression Syntax - Write expressions in workflow fields
- n8n Workflow Patterns - Architectural patterns from templates
- n8n Validation Expert - Interpret validation errors
- n8n Node Configuration - Operation-specific requirements
- n8n Code JavaScript - Write JavaScript in Code nodes
- n8n Code Python - Write Python in Code nodes
