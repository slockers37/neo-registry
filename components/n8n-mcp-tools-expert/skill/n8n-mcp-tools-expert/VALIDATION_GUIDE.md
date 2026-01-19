# Configuration Validation Tools Guide

Complete guide for validating node configurations and workflows via mcporter.

---

## Validation Philosophy

**Validate early, validate often**

Validation is typically iterative with validate → fix cycles

---

## validate_node (UNIFIED VALIDATION)

The `validate_node` tool provides all validation capabilities with different modes.

### Quick Check (mode="minimal")

**Speed**: <50ms

**Use when**: Checking what fields are required

```bash
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{}' \
  mode=minimal
```

**Returns**:
```json
{
  "valid": true,
  "missingRequiredFields": []
}
```

**When to use**: Planning configuration, seeing basic requirements

### Full Validation (mode="full", DEFAULT)

**Speed**: <100ms

**Use when**: Validating actual configuration before deployment

```bash
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{"resource":"channel","operation":"create","channel":"general"}' \
  profile=runtime
```

---

## Validation Profiles

Choose based on your stage:

**minimal** - Only required fields
- Fastest
- Most permissive
- Use: Quick checks during editing

**runtime** - Values + types (**RECOMMENDED**)
- Balanced validation
- Catches real errors
- Use: Pre-deployment validation

**ai-friendly** - Reduce false positives
- For AI-generated configs
- Tolerates minor issues
- Use: When AI configures nodes

**strict** - Maximum validation
- Strictest rules
- May have false positives
- Use: Production deployment

---

## Validation Response

```json
{
  "nodeType": "nodes-base.slack",
  "workflowNodeType": "n8n-nodes-base.slack",
  "displayName": "Slack",
  "valid": false,
  "errors": [
    {
      "type": "missing_required",
      "property": "name",
      "message": "Channel name is required",
      "fix": "Provide a channel name (lowercase, no spaces, 1-80 characters)"
    }
  ],
  "warnings": [
    {
      "type": "best_practice",
      "property": "errorHandling",
      "message": "Slack API can have rate limits",
      "suggestion": "Add onError: 'continueRegularOutput' with retryOnFail"
    }
  ],
  "suggestions": [],
  "summary": {
    "hasErrors": true,
    "errorCount": 1,
    "warningCount": 1,
    "suggestionCount": 0
  }
}
```

### Error Types

- `missing_required` - Must fix
- `invalid_value` - Must fix
- `type_mismatch` - Must fix
- `best_practice` - Should fix (warning)
- `suggestion` - Optional improvement

---

## validate_workflow (STRUCTURE VALIDATION)

**Speed**: 100-500ms

**Use when**: Checking complete workflow before execution

```bash
npx mcporter call n8n.validate_workflow \
  workflow='{"nodes":[...],"connections":{...}}' \
  options='{"validateNodes":true,"validateConnections":true,"validateExpressions":true,"profile":"runtime"}'
```

**Validates**:
- Node configurations
- Connection validity (no broken references)
- Expression syntax ({{ }} patterns)
- Workflow structure (triggers, flow)
- AI connections (8 types)

**Returns**: Comprehensive validation report with errors, warnings, suggestions

### Validate by Workflow ID

```bash
npx mcporter call n8n.n8n_validate_workflow \
  id=workflow-id \
  options='{"validateNodes":true,"validateConnections":true,"validateExpressions":true,"profile":"runtime"}'
```

---

## Validation Loop Pattern

**Typical cycle**: 23s thinking, 58s fixing

```
1. Configure node
   ↓
2. validate_node (23s thinking about errors)
   ↓
3. Fix errors
   ↓
4. validate_node again (58s fixing)
   ↓
5. Repeat until valid
```

**Example**:
```bash
# Iteration 1
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{"resource":"channel","operation":"create"}' \
  profile=runtime
# → Error: Missing "name"

# Iteration 2 (~58s later, after adding name)
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{"resource":"channel","operation":"create","name":"general"}' \
  profile=runtime
# → Valid!
```

---

## Auto-Sanitization System

**When it runs**: On ANY workflow update (create or update_partial)

**What it fixes** (automatically on ALL nodes):
1. Binary operators (equals, contains, greaterThan) → removes `singleValue`
2. Unary operators (isEmpty, isNotEmpty, true, false) → adds `singleValue: true`
3. Invalid operator structures → corrects to proper format
4. IF v2.2+ nodes → adds complete `conditions.options` metadata
5. Switch v3.2+ nodes → adds complete `conditions.options` for all rules

**What it CANNOT fix**:
- Broken connections (references to non-existent nodes)
- Branch count mismatches (3 Switch rules but only 2 outputs)
- Paradoxical corrupt states (API returns corrupt, rejects updates)

**Recovery tools**:
- `cleanStaleConnections` operation - removes broken connections
- `n8n_autofix_workflow` - preview/apply fixes

---

## n8n_autofix_workflow (AUTO-FIX TOOL)

**Use when**: Validation errors need automatic fixes

```bash
# Preview fixes (default - doesn't apply)
npx mcporter call n8n.n8n_autofix_workflow \
  id=workflow-id \
  applyFixes=false \
  confidenceThreshold=medium

# Apply fixes
npx mcporter call n8n.n8n_autofix_workflow \
  id=workflow-id \
  applyFixes=true
```

**Fix Types**:
- `expression-format` - Fix expression syntax
- `typeversion-correction` - Correct typeVersion
- `error-output-config` - Fix error output settings
- `webhook-missing-path` - Add missing webhook paths
- `typeversion-upgrade` - Upgrade to latest version
- `version-migration` - Apply version migrations

---

## Binary vs Unary Operators

**Binary operators** (compare two values):
- equals, notEquals, contains, notContains
- greaterThan, lessThan, startsWith, endsWith
- **Must NOT have** `singleValue: true`

**Unary operators** (check single value):
- isEmpty, isNotEmpty, true, false
- **Must have** `singleValue: true`

**Auto-sanitization fixes these automatically!**

---

## Handling Validation Errors

### Process

```
1. Read error message carefully
2. Check if it's a known false positive
3. Fix real errors
4. Validate again
5. Iterate until clean
```

### Common Errors

**"Required field missing"**
→ Add the field with appropriate value

**"Invalid value"**
→ Check allowed values in get_node output

**"Type mismatch"**
→ Convert to correct type (string/number/boolean)

**"Cannot have singleValue"**
→ Auto-sanitization will fix on next update

**"Missing operator metadata"**
→ Auto-sanitization will fix on next update

### False Positives

Some validation warnings may be acceptable:
- Optional best practices
- Node-specific edge cases
- Profile-dependent issues

Use **ai-friendly** profile to reduce false positives.

---

## Best Practices

### Do

- Use **runtime** profile for pre-deployment
- Validate after every configuration change
- Fix errors immediately (avg 58s)
- Iterate validation loop
- Trust auto-sanitization for operator issues
- Use `mode=minimal` for quick checks
- Use `n8n_autofix_workflow` for bulk fixes
- Activate workflows via API when ready (`activateWorkflow` operation)

### Don't

- Skip validation before deployment
- Ignore error messages
- Use strict profile during development (too many warnings)
- Assume validation passed (check result)
- Try to manually fix auto-sanitization issues

---

## Example: Complete Validation Workflow

```bash
# Step 1: Get node requirements (quick check)
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{}' \
  mode=minimal
# → Know what's required

# Step 2: Configure node (in your code/workflow)

# Step 3: Validate configuration (full validation)
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{"resource":"message","operation":"post","channel":"#general","text":"Hello!"}' \
  profile=runtime

# Step 4: Check result - if valid, continue; if not, fix and retry

# Step 5: Validate in workflow context
npx mcporter call n8n.validate_workflow \
  workflow='{"nodes":[...],"connections":{...}}'

# Step 6: Apply auto-fixes if needed
npx mcporter call n8n.n8n_autofix_workflow \
  id=workflow-id \
  applyFixes=true
```

---

## Summary

**Key Points**:
1. Use **runtime** profile (balanced validation)
2. Validation loop: validate → fix (58s) → validate again
3. Auto-sanitization fixes operator structures automatically
4. Binary operators ≠ singleValue, Unary operators = singleValue: true
5. Iterate until validation passes
6. Use `n8n_autofix_workflow` for automatic fixes

**Tool Selection**:
- **validate_node (mode=minimal)**: Quick required fields check
- **validate_node (profile=runtime)**: Full config validation (**use this!**)
- **validate_workflow**: Complete workflow check
- **n8n_validate_workflow (id=...)**: Validate existing workflow
- **n8n_autofix_workflow (id=...)**: Auto-fix common issues

**Related**:
- [SEARCH_GUIDE.md](SEARCH_GUIDE.md) - Find nodes
- [WORKFLOW_GUIDE.md](WORKFLOW_GUIDE.md) - Build workflows
