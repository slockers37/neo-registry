<required_reading>
Read references/registry-format.md for complete format details.
</required_reading>

<objective>
Create and host your own OCX registry to share components.
</objective>

<process>
**1. Scaffold registry structure:**

```bash
mkdir my-registry && cd my-registry
ocx init --registry --namespace myorg --author "Your Name"
```

This creates:
```
my-registry/
├── index.json
├── components/
├── .well-known/
│   └── ocx.json
└── .nojekyll          # Required for GitHub Pages
```

**2. Add a component:**

Create packument at `components/my-skill.json`:
```json
{
  "name": "my-skill",
  "dist-tags": { "latest": "1.0.0" },
  "versions": {
    "1.0.0": {
      "name": "my-skill",
      "type": "ocx:skill",
      "description": "What it does",
      "files": ["skill/my-skill/SKILL.md"],
      "dependencies": []
    }
  }
}
```

Create the file at `components/my-skill/skill/my-skill/SKILL.md`.

**3. Update index.json:**

```json
{
  "name": "My Registry",
  "namespace": "myorg",
  "version": "1.0.0",
  "components": [
    { "name": "my-skill", "type": "ocx:skill", "description": "What it does" }
  ]
}
```

**4. Host on GitHub Pages:**

```bash
git init && git add -A && git commit -m "Initial registry"
gh repo create my-registry --public --source=. --push

# Enable Pages (main branch, root folder)
gh api repos/USERNAME/my-registry/pages -X POST \
  -f source='{"branch":"main","path":"/"}'
```

**5. Use your registry:**

```bash
ocx registry add https://USERNAME.github.io/my-registry --name myorg
ocx add myorg/my-skill
```
</process>

<file_path_convention>
Files array in packument: paths are relative to `components/{name}/`

Example: For `components/tavily.json` with `files: ["skill/tavily/SKILL.md"]`
- OCX fetches: `/components/tavily/skill/tavily/SKILL.md`
- Installs to: `.opencode/skill/tavily/SKILL.md`
</file_path_convention>

<success_criteria>
- `curl https://your-registry/.well-known/ocx.json` returns `{"registry":"/index.json"}`
- `ocx list yournamespace` shows components
- `ocx add yournamespace/component` installs successfully
</success_criteria>
