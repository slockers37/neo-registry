---
name: n8n-code-javascript
description: Write JavaScript code in n8n Code nodes. Use when writing JavaScript in n8n, using $input/$json/$node syntax, making HTTP requests with $helpers, working with dates using DateTime, troubleshooting Code node errors, or choosing between Code node modes.
---

# JavaScript Code Node

Expert guidance for writing JavaScript code in n8n Code nodes.

---

## Quick Start

```javascript
// Basic template for Code nodes
const items = $input.all();

// Process data
const processed = items.map(item => ({
  json: {
    ...item.json,
    processed: true,
    timestamp: new Date().toISOString()
  }
}));

return processed;
```

### Essential Rules

1. **Choose "Run Once for All Items" mode** (recommended for most use cases)
2. **Access data**: `$input.all()`, `$input.first()`, or `$input.item`
3. **CRITICAL**: Must return `[{json: {...}}]` format
4. **CRITICAL**: Webhook data is under `$json.body` (not `$json` directly)
5. **Built-ins available**: $helpers.httpRequest(), DateTime (Luxon), $jmespath()

---

## Mode Selection Guide

The Code node offers two execution modes. Choose based on your use case:

### Run Once for All Items (Recommended - Default)

**Use this mode for:** 95% of use cases

- **How it works**: Code executes **once** regardless of input count
- **Data access**: `$input.all()` or `items` array
- **Best for**: Aggregation, filtering, batch processing, transformations, API calls with all data
- **Performance**: Faster for multiple items (single execution)

```javascript
// Example: Calculate total from all items
const allItems = $input.all();
const total = allItems.reduce((sum, item) => sum + (item.json.amount || 0), 0);

return [{
  json: {
    total,
    count: allItems.length,
    average: total / allItems.length
  }
}];
```

### Run Once for Each Item

**Use this mode for:** Specialized cases only

- **How it works**: Code executes **separately** for each input item
- **Data access**: `$input.item` or `$item`
- **Best for**: Item-specific logic, independent operations, per-item validation

```javascript
// Example: Add processing timestamp to each item
const item = $input.item;

return [{
  json: {
    ...item.json,
    processed: true,
    processedAt: new Date().toISOString()
  }
}];
```

---

## Data Access Patterns

### Pattern 1: $input.all() - Most Common

```javascript
const allItems = $input.all();
const valid = allItems.filter(item => item.json.status === 'active');
return valid.map(item => ({json: {id: item.json.id, name: item.json.name}}));
```

### Pattern 2: $input.first() - Very Common

```javascript
const firstItem = $input.first();
const data = firstItem.json;
return [{json: {result: processData(data)}}];
```

### Pattern 3: $node - Reference Other Nodes

```javascript
const webhookData = $node["Webhook"].json;
const httpData = $node["HTTP Request"].json;
return [{json: {combined: {webhook: webhookData, api: httpData}}}];
```

---

## Critical: Webhook Data Structure

**MOST COMMON MISTAKE**: Webhook data is nested under `.body`

```javascript
// ❌ WRONG - Will return undefined
const name = $json.name;

// ✅ CORRECT - Webhook data is under .body
const name = $json.body.name;
```

---

## Return Format Requirements

**CRITICAL RULE**: Always return array of objects with `json` property

```javascript
// ✅ Single result
return [{json: {field1: value1}}];

// ✅ Multiple results
return [
  {json: {id: 1, data: 'first'}},
  {json: {id: 2, data: 'second'}}
];

// ✅ Empty result
return [];
```

---

## Built-in Functions & Helpers

### $helpers.httpRequest()

```javascript
const response = await $helpers.httpRequest({
  method: 'GET',
  url: 'https://api.example.com/data',
  headers: {'Authorization': 'Bearer token'}
});
return [{json: {data: response}}];
```

### DateTime (Luxon)

```javascript
const now = DateTime.now();
const formatted = now.toFormat('yyyy-MM-dd');
const tomorrow = now.plus({days: 1});
return [{json: {today: formatted, tomorrow: tomorrow.toFormat('yyyy-MM-dd')}}];
```

### $jmespath()

```javascript
const data = $input.first().json;
const adults = $jmespath(data, 'users[?age >= `18`]');
return [{json: {adults}}];
```

---

## Error Prevention - Top 5 Mistakes

### #1: Missing Return Statement
```javascript
// ❌ WRONG
const items = $input.all();
// Forgot to return!

// ✅ CORRECT
return items.map(item => ({json: item.json}));
```

### #2: Wrong Return Format
```javascript
// ❌ WRONG
return {json: {result: 'success'}};

// ✅ CORRECT
return [{json: {result: 'success'}}];
```

### #3: Webhook Body Nesting
```javascript
// ❌ WRONG
const email = $json.email;

// ✅ CORRECT
const email = $json.body.email;
```

### #4: Missing Null Checks
```javascript
// ❌ WRONG
const value = item.json.user.email;

// ✅ CORRECT
const value = item.json?.user?.email || 'default';
```

---

## Best Practices

1. **Always validate input data** - Check if items exist before processing
2. **Use try-catch for error handling** - Especially with HTTP requests
3. **Prefer array methods over loops** - map/filter/reduce are cleaner
4. **Filter early, process late** - Reduce dataset before expensive operations
5. **Debug with console.log()** - Appears in browser console

---

## When to Use Code Node

Use Code node when:
- ✅ Complex transformations requiring multiple steps
- ✅ Custom calculations or business logic
- ✅ API response parsing with complex structure
- ✅ Data aggregation across items

Consider other nodes when:
- ❌ Simple field mapping → Use **Set** node
- ❌ Basic filtering → Use **Filter** node
- ❌ Simple conditionals → Use **IF** or **Switch** node

---

## Integration with n8n MCP Tools

Find Code node info via mcporter:
```bash
npx mcporter call "n8n.search_nodes(query: 'code')"
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.code')"
```

---

## Related Skills

- **n8n Expression Syntax** - Expressions vs code
- **n8n Node Configuration** - Mode and language selection
- **n8n Validation Expert** - Validate configurations
