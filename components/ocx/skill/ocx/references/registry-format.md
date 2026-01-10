# Registry Format Reference

## Directory Structure

```
my-registry/
├── index.json                    # Registry manifest
├── .well-known/
│   └── ocx.json                  # Discovery file
├── .nojekyll                     # For GitHub Pages
├── components/
│   ├── my-skill.json             # Packument
│   ├── my-skill/                 # Component files
│   │   └── skill/
│   │       └── my-skill/
│   │           └── SKILL.md
│   ├── my-bundle.json
│   └── my-plugin.json
└── README.md
```

---

## index.json

```json
{
  "name": "My Registry",
  "namespace": "myorg",
  "version": "1.0.0",
  "author": "Your Name",
  "components": [
    {
      "name": "my-skill",
      "type": "ocx:skill",
      "description": "Short description"
    },
    {
      "name": "my-bundle",
      "type": "ocx:bundle",
      "description": "Collection of components"
    }
  ]
}
```

---

## .well-known/ocx.json

```json
{
  "registry": "/index.json"
}
```

Required for registry discovery.

---

## Packument Format

Located at `/components/{name}.json`:

```json
{
  "name": "my-skill",
  "dist-tags": {
    "latest": "1.0.0"
  },
  "versions": {
    "1.0.0": {
      "name": "my-skill",
      "type": "ocx:skill",
      "description": "What this component does",
      "files": [
        "skill/my-skill/SKILL.md",
        "skill/my-skill/references/guide.md"
      ],
      "dependencies": [
        "myorg/other-component"
      ],
      "opencode": {
        "mcp": {
          "my-mcp": {
            "command": "npx",
            "args": ["-y", "my-mcp-server"]
          }
        }
      }
    }
  }
}
```

### Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Component name |
| `dist-tags` | Yes | Version tags (`latest` required) |
| `versions` | Yes | Version objects |
| `versions.X.type` | Yes | One of: `ocx:skill`, `ocx:agent`, `ocx:command`, `ocx:plugin`, `ocx:mcp`, `ocx:bundle` |
| `versions.X.files` | Yes | Array of file paths (relative to `/components/{name}/`) |
| `versions.X.dependencies` | No | Array of `namespace/component` |
| `versions.X.opencode` | No | Config merged into consumer's opencode.jsonc |

---

## File Path Resolution

For packument `/components/tavily.json` with:
```json
"files": ["skill/tavily/SKILL.md"]
```

OCX fetches: `https://registry/components/tavily/skill/tavily/SKILL.md`
Installs to: `.opencode/skill/tavily/SKILL.md`

**Pattern:** `/components/{name}/{file_path}` → `.opencode/{file_path}`

---

## Hosting Options

| Platform | Free Tier | Notes |
|----------|-----------|-------|
| GitHub Pages | Unlimited | Add `.nojekyll` for dotfiles |
| Cloudflare Pages | 100k req/day | Fast CDN |
| Vercel | 100GB/month | Easy deploy |
| Netlify | 100GB/month | Easy deploy |
