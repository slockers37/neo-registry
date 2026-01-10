<required_reading>
Read references/ghost-mode.md for full configuration options.
</required_reading>

<objective>
Setup Ghost Mode for portable personal AI config that works in any repo without modifying it.
</objective>

<concept>
Ghost Mode creates a temporary "symlink farm" that:
1. Mirrors the real project (all files symlinked)
2. REPLACES `.opencode/` with YOUR personal config
3. Launches OpenCode in this isolated environment
4. Git operations still work on the real project
</concept>

<process>
**Initial setup (once):**

```bash
# Initialize ghost configuration
ocx ghost init

# This creates ~/.config/ocx/ghost/
# with a default profile
```

**Configure your profile:**

```bash
# Open ghost.jsonc in editor
ocx ghost config
```

Edit `ghost.jsonc`:
```jsonc
{
  "registries": {
    "neo": { "url": "https://slockers37.github.io/neo-registry" }
  },
  "componentPath": ".opencode",
  "include": [],   // Patterns to include FROM project
  "exclude": []    // Patterns to exclude from symlinks
}
```

**Install components to your ghost profile:**

```bash
# Components install to ~/.config/ocx/ghost/profiles/default/.opencode/
ocx ghost add neo/dev neo/plugins neo/meta
```

**Use ghost mode:**

```bash
# In any project directory
ocx ghost opencode

# OpenCode launches with YOUR config, not the project's
```

**Multiple profiles:**

```bash
ocx ghost profile add minimal     # Create profile
ocx ghost profile add full
ocx ghost profile use minimal     # Switch active profile
ocx ghost profile list            # Show all profiles
```
</process>

<include_project_components>
To selectively include project components alongside your personal config:

```jsonc
// ghost.jsonc
{
  "include": [
    ".opencode/skill/project-specific/**",  // Include specific skill
    "**/AGENTS.md"                          // Include project's AGENTS.md
  ]
}
```

Include patterns bring back specific project files that would otherwise be replaced.
</include_project_components>

<success_criteria>
- `ocx ghost opencode` launches without errors
- Your personal skills/commands/plugins are available
- Project's OpenCode config is NOT used (unless explicitly included)
- Git operations work normally in the ghost environment
</success_criteria>
