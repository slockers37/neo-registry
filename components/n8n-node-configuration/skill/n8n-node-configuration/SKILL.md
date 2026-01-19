---
name: n8n-node-configuration
description: Operation-aware node configuration guidance. Use when configuring nodes, understanding property dependencies, determining required fields, choosing between get_node detail levels, or learning common configuration patterns by node type.
---

# n8n Node Configuration

Expert guidance for operation-aware node configuration with property dependencies.

---

## Configuration Philosophy

**Progressive disclosure**: Start minimal, add complexity as needed

Configuration best practices:
- `get_node` with `detail: "standard"` is the most used discovery pattern
- 56 seconds average between configuration edits
- Covers 95% of use cases with 1-2K tokens response

**Key insight**: Most configurations need only standard detail, not full schema!

---

## Core Concepts

### 1. Operation-Aware Configuration

**Not all fields are always required** - it depends on operation!

**Example**: Slack node
```javascript
// For operation='post'
{
  "resource": "message",
  "operation": "post",
  "channel": "#general",  // Required for post
  "text": "Hello!"        // Required for post
}

// For operation='update'
{
  "resource": "message",
  "operation": "update",
  "messageId": "123",     // Required for update (different!)
  "text": "Updated!"
  // channel NOT required for update
}
```

### 2. Property Dependencies

**Fields appear/disappear based on other field values**

**Example**: HTTP Request node
```javascript
// When method='GET'
{
  "method": "GET",
  "url": "https://api.example.com"
  // sendBody not shown (GET doesn't have body)
}

// When method='POST'
{
  "method": "POST",
  "url": "https://api.example.com",
  "sendBody": true,       // Now visible!
  "body": {...}           // Required when sendBody=true
}
```

### 3. Progressive Discovery

**Use the right detail level**:

1. **get_node (standard)** - DEFAULT (~1-2K tokens)
   ```bash
   npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack')"
   ```

2. **get_node (search_properties)** - Find specific fields
   ```bash
   npx mcporter call n8n.get_node nodeType=nodes-base.httpRequest mode=search_properties propertyQuery=auth
   ```

3. **get_node (full)** - Complete schema (~3-8K tokens, use sparingly)
   ```bash
   npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack', detail: 'full')"
   ```

---

## Configuration Workflow

### Standard Process

```
1. Identify node type and operation
   ↓
2. Use get_node (standard detail is default)
   npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack')"
   ↓
3. Configure required fields
   ↓
4. Validate configuration
   npx mcporter call n8n.validate_node nodeType=nodes-base.slack config='{...}' profile=runtime
   ↓
5. If field unclear → search_properties mode
   ↓
6. Add optional fields as needed
   ↓
7. Validate again
   ↓
8. Deploy
```

### Example: Configuring HTTP Request

**Step 1**: Identify what you need (POST JSON to API)

**Step 2**: Get node info
```bash
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.httpRequest')"
# Returns: method, url, sendBody, body, authentication required/optional
```

**Step 3**: Minimal config
```javascript
{
  "method": "POST",
  "url": "https://api.example.com/create",
  "authentication": "none"
}
```

**Step 4**: Validate
```bash
npx mcporter call n8n.validate_node nodeType=nodes-base.httpRequest config='{"method":"POST","url":"https://api.example.com"}' profile=runtime
# → Error: "sendBody required for POST"
```

**Step 5**: Add required field, validate again
```bash
npx mcporter call n8n.validate_node nodeType=nodes-base.httpRequest config='{"method":"POST","url":"...","sendBody":true,"body":{"contentType":"json","content":{}}}' profile=runtime
# → Valid! ✅
```

---

## get_node Detail Levels

### Standard Detail (DEFAULT - Use This!)

**Speed**: <10ms | **Size**: ~1-2K tokens

```bash
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack')"
# detail="standard" is the default
```

**Returns**: Required fields, common options, operation list, metadata

**Use**: 95% of configuration needs

### Full Detail (Use Sparingly)

**Speed**: <100ms | **Size**: ~3-8K tokens

```bash
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.slack', detail: 'full')"
```

**Warning**: Large response, use only when standard insufficient

### Search Properties Mode

**Use when**: Looking for specific field

```bash
npx mcporter call n8n.get_node nodeType=nodes-base.httpRequest mode=search_properties propertyQuery=auth
```

**Common searches**: auth, header, body, json, url, method, credential

### Decision Tree

```
Starting new node config?
  → npx mcporter call "n8n.get_node(nodeType: '...')"
     ↓
Standard has what you need?
  → YES: Configure with it
  → NO: Continue
     ↓
Looking for specific field?
  → YES: mode=search_properties
  → NO: detail=full
```

---

## Common Node Patterns

### Pattern 1: Resource/Operation Nodes

**Examples**: Slack, Google Sheets, Airtable

```javascript
{
  "resource": "<entity>",      // What type of thing
  "operation": "<action>",     // What to do with it
  // ... operation-specific fields
}
```

### Pattern 2: HTTP-Based Nodes

**Examples**: HTTP Request, Webhook

```javascript
{
  "method": "<HTTP_METHOD>",
  "url": "<endpoint>",
  "authentication": "<type>",
  // ... method-specific fields
}
```

**Dependencies**:
- POST/PUT/PATCH → sendBody available
- sendBody=true → body required
- authentication != "none" → credentials required

### Pattern 3: Database Nodes

**Examples**: Postgres, MySQL, MongoDB

**Dependencies**:
- operation="executeQuery" → query required
- operation="insert" → table + values required
- operation="update" → table + values + where required

### Pattern 4: Conditional Logic Nodes

**Examples**: IF, Switch, Merge

**Dependencies**:
- Binary operators (equals, contains) → value1 + value2
- Unary operators (isEmpty, isNotEmpty) → value1 only + singleValue: true

---

## Operation-Specific Configuration Examples

### Slack Node

#### Post Message
```javascript
{
  "resource": "message",
  "operation": "post",
  "channel": "#general",      // Required
  "text": "Hello!"            // Required
}
```

#### Update Message
```javascript
{
  "resource": "message",
  "operation": "update",
  "messageId": "1234567890",  // Required (different from post!)
  "text": "Updated!"          // Required
}
```

### HTTP Request Node

#### GET Request
```javascript
{
  "method": "GET",
  "url": "https://api.example.com/users",
  "authentication": "predefinedCredentialType"
}
```

#### POST with JSON
```javascript
{
  "method": "POST",
  "url": "https://api.example.com/users",
  "sendBody": true,
  "body": {
    "contentType": "json",
    "content": {"name": "John", "email": "john@example.com"}
  }
}
```

### IF Node

#### String Comparison (Binary)
```javascript
{
  "conditions": {
    "string": [{
      "value1": "={{$json.status}}",
      "operation": "equals",
      "value2": "active"              // Binary: needs value2
    }]
  }
}
```

#### Empty Check (Unary)
```javascript
{
  "conditions": {
    "string": [{
      "value1": "={{$json.email}}",
      "operation": "isEmpty"
      // No value2 - unary operator
      // singleValue: true (auto-added by sanitization)
    }]
  }
}
```

---

## Configuration Anti-Patterns

### ❌ Don't: Over-configure Upfront

```javascript
// BAD: Adding every possible field
{
  "method": "GET",
  "sendQuery": false,
  "sendHeaders": false,
  "sendBody": false,
  "timeout": 10000,
  // ... 20 more optional fields
}

// GOOD: Start minimal
{
  "method": "GET",
  "url": "...",
  "authentication": "none"
}
```

### ❌ Don't: Skip Validation

```bash
# BAD: Deploy without validating
npx mcporter call n8n.n8n_update_partial_workflow ...  # YOLO

# GOOD: Validate before deploying
npx mcporter call n8n.validate_node nodeType=... config='{...}' profile=runtime
# Then deploy if valid
```

### ❌ Don't: Ignore Operation Context

Different operations need different fields! Always check get_node when changing operation.

---

## Best Practices

### ✅ Do

1. **Start with get_node (standard detail)** - ~1-2K tokens, covers 95% of needs
2. **Validate iteratively** - Configure → Validate → Fix → Repeat
3. **Use search_properties mode when stuck** - Find specific fields
4. **Respect operation context** - Different operations = different requirements
5. **Trust auto-sanitization** - Operator structure fixed automatically

### ❌ Don't

1. **Jump to detail="full" immediately** - Try standard detail first
2. **Configure blindly** - Always validate before deploying
3. **Copy configs without understanding** - Different operations need different fields
4. **Manually fix auto-sanitization issues** - Let system handle operator structure

---

## Summary

**Configuration Strategy**:
1. `npx mcporter call "n8n.get_node(nodeType: '...')"` (standard detail is default)
2. Configure required fields for operation
3. `npx mcporter call n8n.validate_node ...` to validate
4. Search properties if stuck
5. Iterate until valid (avg 2-3 cycles)
6. Deploy with confidence

**Key Principles**:
- **Operation-aware**: Different operations = different requirements
- **Progressive disclosure**: Start minimal, add as needed
- **Dependency-aware**: Understand field visibility rules
- **Validation-driven**: Let validation guide configuration

**Related Skills**:
- **n8n MCP Tools Expert** - How to use discovery tools correctly
- **n8n Validation Expert** - Interpret validation errors
- **n8n Expression Syntax** - Configure expression fields
