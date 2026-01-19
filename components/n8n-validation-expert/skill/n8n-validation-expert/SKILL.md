---
name: n8n-validation-expert
description: Interpret validation errors and guide fixing them. Use when encountering validation errors, validation warnings, false positives, operator structure issues, or need help understanding validation results. Also use when asking about validation profiles, error types, or the validation loop process.
---

# n8n Validation Expert

Expert guide for interpreting and fixing n8n validation errors.

---

## Validation Philosophy

**Validate early, validate often**

Validation is typically iterative:
- Expect validation feedback loops
- Usually 2-3 validate → fix cycles
- Average: 23s thinking about errors, 58s fixing them

---

## Error Severity Levels

### 1. Errors (Must Fix)
**Blocks workflow execution** - Must be resolved before activation

**Types**:
- `missing_required` - Required field not provided
- `invalid_value` - Value doesn't match allowed options
- `type_mismatch` - Wrong data type
- `invalid_reference` - Referenced node doesn't exist
- `invalid_expression` - Expression syntax error

### 2. Warnings (Should Fix)
**Doesn't block execution** - Workflow can be activated but may have issues

### 3. Suggestions (Optional)
**Nice to have** - Improvements that could enhance workflow

---

## The Validation Loop

### Pattern from Telemetry (7,841 occurrences)

```
1. Configure node
   ↓
2. Validate (23 seconds thinking about errors)
   npx mcporter call n8n.validate_node nodeType=... config='{...}' profile=runtime
   ↓
3. Read error messages carefully
   ↓
4. Fix errors
   ↓
5. Validate again (58 seconds fixing)
   ↓
6. Repeat until valid (usually 2-3 iterations)
```

### Example

```bash
# Iteration 1
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{"resource":"channel","operation":"create"}' \
  profile=runtime
# → Error: Missing "name"

# ⏱️ 23 seconds thinking...

# Iteration 2
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{"resource":"channel","operation":"create","name":"general"}' \
  profile=runtime
# → Valid! ✅
```

---

## Validation Profiles

Choose the right profile for your stage:

### minimal
**Use when**: Quick checks during editing
- Only required fields, basic structure
- Fastest, most permissive

### runtime (RECOMMENDED)
**Use when**: Pre-deployment validation
- Required fields, value types, allowed values, basic dependencies
- Balanced, catches real errors

### ai-friendly
**Use when**: AI-generated configurations
- Same as runtime, reduces false positives
- More tolerant of minor issues

### strict
**Use when**: Production deployment, critical workflows
- Everything including best practices, performance, security
- Maximum safety, may have false positives

---

## Common Error Types

### 1. missing_required

```bash
# Error: Missing "channel"

# Fix: Add the field
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{"resource":"message","operation":"post","channel":"#general","text":"Hello"}' \
  profile=runtime
```

### 2. invalid_value

```bash
# Error: Operation must be one of: post, update, delete

# Fix: Use valid operation
# Change "send" to "post"
```

### 3. type_mismatch

```bash
# Error: Expected number, got string

# Fix: Convert "100" to 100
```

### 4. invalid_expression

```bash
# Error: Invalid expression: $json.name

# Fix: Add {{ }}
# Change $json.name to ={{$json.name}}
```

### 5. invalid_reference

```bash
# Error: Node 'HTTP Requets' does not exist

# Fix: Correct typo to 'HTTP Request'
```

---

## Auto-Sanitization System

### What It Does
**Automatically fixes common operator structure issues** on ANY workflow update

**Runs when**:
- `n8n_create_workflow`
- `n8n_update_partial_workflow`

### What It Fixes

1. **Binary Operators** (equals, contains, greaterThan, etc.)
   - Removes `singleValue` property

2. **Unary Operators** (isEmpty, isNotEmpty, true, false)
   - Adds `singleValue: true`

3. **IF/Switch Metadata**
   - Adds complete `conditions.options` metadata

### What It CANNOT Fix

1. **Broken Connections** - References to non-existent nodes
   - Solution: Use `cleanStaleConnections` operation

2. **Branch Count Mismatches** - 3 Switch rules but only 2 outputs
   - Solution: Add missing connections or remove extra rules

3. **Paradoxical Corrupt States**
   - Solution: May require manual intervention

---

## Workflow Validation

### validate_workflow (Complete Structure)

```bash
npx mcporter call n8n.validate_workflow \
  workflow='{"nodes":[...],"connections":{...}}' \
  options='{"validateNodes":true,"validateConnections":true,"validateExpressions":true,"profile":"runtime"}'
```

### Validate by Workflow ID

```bash
npx mcporter call n8n.n8n_validate_workflow \
  id=workflow-id \
  options='{"validateNodes":true,"validateConnections":true,"profile":"runtime"}'
```

### Common Workflow Errors

- **Broken Connections**: Remove stale connection or create missing node
- **Circular Dependencies**: Restructure workflow to remove loop
- **Multiple Start Nodes**: Remove extra triggers or split workflows
- **Disconnected Nodes**: Connect node or remove if unused

---

## Recovery Strategies

### Strategy 1: Start Fresh
**When**: Configuration is severely broken

1. Note required fields from `get_node`
2. Create minimal valid configuration
3. Add features incrementally
4. Validate after each addition

### Strategy 2: Clean Stale Connections
**When**: "Node not found" errors

```bash
npx mcporter call n8n.n8n_update_partial_workflow \
  id=workflow-id \
  operations='[{"type":"cleanStaleConnections"}]'
```

### Strategy 3: Use Auto-fix
**When**: Operator structure errors

```bash
# Preview fixes first
npx mcporter call n8n.n8n_autofix_workflow id=workflow-id applyFixes=false

# Apply fixes
npx mcporter call n8n.n8n_autofix_workflow id=workflow-id applyFixes=true
```

---

## False Positives

### What Are They?
Validation warnings that are technically "wrong" but acceptable in your use case

### Common False Positives

- **"Missing error handling"** - Acceptable for simple/test workflows
- **"No retry logic"** - Acceptable for idempotent operations
- **"Missing rate limiting"** - Acceptable for internal APIs
- **"Unbounded query"** - Acceptable for small known datasets

### Reducing False Positives

```bash
npx mcporter call n8n.validate_node \
  nodeType=nodes-base.slack \
  config='{...}' \
  profile=ai-friendly  # Fewer false positives
```

---

## Best Practices

### ✅ Do

- Validate after every significant change
- Read error messages completely
- Fix errors iteratively (one at a time)
- Use `runtime` profile for pre-deployment
- Check `valid` field before assuming success
- Trust auto-sanitization for operator issues
- Use `get_node` when unclear about requirements

### ❌ Don't

- Skip validation before activation
- Try to fix all errors at once
- Ignore error messages
- Use `strict` profile during development (too noisy)
- Assume validation passed (always check result)
- Manually fix auto-sanitization issues
- Deploy with unresolved errors

---

## Summary

**Key Points**:
1. **Validation is iterative** (avg 2-3 cycles, 23s + 58s)
2. **Errors must be fixed**, warnings are optional
3. **Auto-sanitization** fixes operator structures automatically
4. **Use runtime profile** for balanced validation
5. **False positives exist** - learn to recognize them
6. **Read error messages** - they contain fix guidance

**Validation Process**:
1. `npx mcporter call n8n.validate_node ...`
2. Read errors → Fix → Validate again
3. Repeat until valid (usually 2-3 iterations)
4. Review warnings and decide if acceptable
5. Deploy with confidence

**Related Skills**:
- n8n MCP Tools Expert - Use validation tools correctly
- n8n Expression Syntax - Fix expression errors
- n8n Node Configuration - Understand required fields
