# Ghost Mode Reference

## Concept

Ghost Mode creates an isolated OpenCode environment using a "symlink farm":

1. Creates temp directory in `$TMPDIR`
2. Symlinks ALL project files to temp dir
3. REPLACES `.opencode/` with your ghost profile's config
4. Sets `GIT_WORK_TREE` to real project (git still works)
5. Launches OpenCode in temp dir

**Result:** Your personal config works in any repo without modifying it.

---

## Configuration Files

### Location
```
~/.config/ocx/ghost/
├── ghost.jsonc              # Global config
├── profiles/
│   ├── default/             # Default profile
│   │   ├── .opencode/       # Your personal OpenCode config
│   │   └── opencode.jsonc
│   ├── minimal/
│   └── full/
```

### ghost.jsonc

```jsonc
{
  // Registries available in ghost mode
  "registries": {
    "neo": { "url": "https://slockers37.github.io/neo-registry" },
    "kdco": { "url": "https://ocx.kdco.dev" }
  },
  
  // Where OpenCode config lives (relative to profile)
  "componentPath": ".opencode",
  
  // Glob patterns to INCLUDE from project
  // (these project files will be available alongside your config)
  "include": [
    "**/AGENTS.md",
    ".opencode/skill/project-specific/**"
  ],
  
  // Glob patterns to EXCLUDE from symlinks
  "exclude": [
    "node_modules/**",
    ".git/**"
  ],
  
  // Active profile name
  "activeProfile": "default"
}
```

---

## Include/Exclude Patterns

### Include (bring project files INTO ghost)
```jsonc
"include": [
  ".opencode/skill/**",           // All project skills
  ".opencode/agent/special.md",   // Specific agent
  "**/AGENTS.md"                  // Project's AGENTS.md
]
```

Without includes, ghost mode completely replaces project's `.opencode/` with yours.

### Exclude (don't symlink these)
```jsonc
"exclude": [
  "node_modules/**",
  ".git/**",
  "dist/**"
]
```

---

## Profile Management

```bash
# Create profile
ocx ghost profile add minimal
ocx ghost profile add full

# Switch profile
ocx ghost profile use minimal

# List profiles
ocx ghost profile list

# Remove profile
ocx ghost profile remove old-profile
```

Each profile has its own `.opencode/` directory with independent components.

---

## Installing to Ghost Profile

```bash
# Install to active profile (not current project)
ocx ghost add neo/core neo/plugins

# Components go to ~/.config/ocx/ghost/profiles/{active}/.opencode/
```

---

## Workflow

```bash
# One-time setup
ocx ghost init
ocx ghost add neo/dev neo/plugins neo/meta

# Daily use - in any project
cd ~/work/some-project
ocx ghost opencode

# OpenCode launches with YOUR config
# Git operations work on the real project
```
