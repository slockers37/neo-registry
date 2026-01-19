---
name: n8n-code-python
description: Write Python code in n8n Code nodes. Use when writing Python in n8n, using _input/_json/_node syntax, working with standard library, or need to understand Python limitations in n8n Code nodes.
---

# Python Code Node (Beta)

Expert guidance for writing Python code in n8n Code nodes.

---

## ⚠️ Important: JavaScript First

**Recommendation**: Use **JavaScript for 95% of use cases**. Only use Python when:
- You need specific Python standard library functions
- You're significantly more comfortable with Python syntax
- You're doing data transformations better suited to Python

**Why JavaScript is preferred:**
- Full n8n helper functions ($helpers.httpRequest, etc.)
- Luxon DateTime library for advanced date/time operations
- No external library limitations
- Better n8n documentation and community support

---

## Quick Start

```python
# Basic template for Python Code nodes
items = _input.all()

# Process data
processed = []
for item in items:
    processed.append({
        "json": {
            **item["json"],
            "processed": True,
            "timestamp": datetime.now().isoformat()
        }
    })

return processed
```

### Essential Rules

1. **Consider JavaScript first** - Use Python only when necessary
2. **Access data**: `_input.all()`, `_input.first()`, or `_input.item`
3. **CRITICAL**: Must return `[{"json": {...}}]` format
4. **CRITICAL**: Webhook data is under `_json["body"]` (not `_json` directly)
5. **CRITICAL LIMITATION**: **No external libraries** (no requests, pandas, numpy)
6. **Standard library only**: json, datetime, re, base64, hashlib, urllib.parse, math, random, statistics

---

## Mode Selection Guide

### Run Once for All Items (Recommended - Default)

```python
all_items = _input.all()
total = sum(item["json"].get("amount", 0) for item in all_items)

return [{
    "json": {
        "total": total,
        "count": len(all_items),
        "average": total / len(all_items) if all_items else 0
    }
}]
```

### Run Once for Each Item

```python
item = _input.item

return [{
    "json": {
        **item["json"],
        "processed": True,
        "processed_at": datetime.now().isoformat()
    }
}]
```

---

## Data Access Patterns

### Pattern 1: _input.all() - Most Common

```python
all_items = _input.all()
valid = [item for item in all_items if item["json"].get("status") == "active"]
return [{"json": {"id": item["json"]["id"]}} for item in valid]
```

### Pattern 2: _input.first() - Very Common

```python
first_item = _input.first()
data = first_item["json"]
return [{"json": {"result": process_data(data)}}]
```

### Pattern 3: _node - Reference Other Nodes

```python
webhook_data = _node["Webhook"]["json"]
http_data = _node["HTTP Request"]["json"]
return [{"json": {"combined": {"webhook": webhook_data, "api": http_data}}}]
```

---

## Critical: Webhook Data Structure

```python
# ❌ WRONG - Will raise KeyError
name = _json["name"]

# ✅ CORRECT - Webhook data is under ["body"]
name = _json["body"]["name"]

# ✅ SAFER - Use .get() for safe access
webhook_data = _json.get("body", {})
name = webhook_data.get("name")
```

---

## Critical Limitation: No External Libraries

### What's NOT Available

```python
# ❌ NOT AVAILABLE - Will raise ModuleNotFoundError
import requests  # ❌ No
import pandas    # ❌ No
import numpy     # ❌ No
```

### What IS Available (Standard Library)

```python
# ✅ AVAILABLE
import json          # JSON parsing
import datetime      # Date/time operations
import re            # Regular expressions
import base64        # Base64 encoding/decoding
import hashlib       # Hashing functions
import urllib.parse  # URL parsing
import math          # Math functions
import random        # Random numbers
import statistics    # Statistical functions
```

### Workarounds

**Need HTTP requests?** Use **HTTP Request node** before Code node, or switch to JavaScript

**Need data analysis?** Use Python `statistics` module or switch to JavaScript

---

## Common Patterns

### Data Transformation

```python
items = _input.all()
return [
    {"json": {"id": item["json"].get("id"), "name": item["json"].get("name", "Unknown").upper()}}
    for item in items
]
```

### Filtering & Aggregation

```python
items = _input.all()
total = sum(item["json"].get("amount", 0) for item in items)
valid_items = [item for item in items if item["json"].get("amount", 0) > 0]
return [{"json": {"total": total, "count": len(valid_items)}}]
```

### String Processing with Regex

```python
import re

items = _input.all()
email_pattern = r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b'

all_emails = []
for item in items:
    text = item["json"].get("text", "")
    emails = re.findall(email_pattern, text)
    all_emails.extend(emails)

return [{"json": {"emails": list(set(all_emails))}}]
```

### Statistical Analysis

```python
from statistics import mean, median, stdev

items = _input.all()
values = [item["json"].get("value", 0) for item in items if "value" in item["json"]]

if values:
    return [{"json": {
        "mean": mean(values),
        "median": median(values),
        "stdev": stdev(values) if len(values) > 1 else 0
    }}]
else:
    return [{"json": {"error": "No values found"}}]
```

---

## Error Prevention

### #1: Importing External Libraries
```python
# ❌ WRONG
import requests  # ModuleNotFoundError!

# ✅ Use HTTP Request node or JavaScript
```

### #2: Missing Return
```python
# ❌ WRONG
items = _input.all()
# Forgot to return!

# ✅ CORRECT
return [{"json": item["json"]} for item in items]
```

### #3: KeyError on Dictionary Access
```python
# ❌ WRONG
name = _json["user"]["name"]  # KeyError!

# ✅ CORRECT
name = _json.get("user", {}).get("name", "Unknown")
```

---

## Best Practices

1. **Always use .get() for dictionary access** - Avoids KeyError
2. **Handle None/Null values explicitly** - Default values
3. **Use list comprehensions for filtering** - Pythonic and efficient
4. **Return consistent structure** - All code paths same format
5. **Debug with print()** - Appears in browser console

---

## When to Use Python vs JavaScript

### Use Python When:
- ✅ You need `statistics` module
- ✅ You're more comfortable with Python
- ✅ Logic maps well to list comprehensions

### Use JavaScript When:
- ✅ You need HTTP requests ($helpers.httpRequest())
- ✅ You need advanced date/time (DateTime/Luxon)
- ✅ **For 95% of use cases** (recommended)

---

## Integration with n8n MCP Tools

Find Code node info via mcporter:
```bash
npx mcporter call "n8n.search_nodes(query: 'code')"
npx mcporter call "n8n.get_node(nodeType: 'nodes-base.code')"
```

---

## Related Skills

- **n8n Code JavaScript** - When to use JavaScript instead
- **n8n Expression Syntax** - Expressions vs code
- **n8n Node Configuration** - Mode and language selection
