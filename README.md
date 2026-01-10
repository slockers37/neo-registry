# Neo Registry

Personal OpenCode component registry.

## Components

### Plugins (3)
- `oh-my-opencode` - Enhanced OpenCode with agents, MCPs, tools
- `dcp` - Directory copy plugin
- `md-table-formatter` - Markdown table formatting

### Skills (8)
- `tavily` - Web search via Tavily API
- `notebooklm` - Google NotebookLM integration
- `brainstorming` - Pre-implementation thinking
- `systematic-debugging` - Structured debugging
- `test-driven-development` - TDD workflow
- `writing-plans` - Create project plans
- `executing-plans` - Execute plans
- `create-agent-skills` - Create/refine skills

### Commands (13)
- `heal-skill` - Fix broken skills
- `consider/*` - 12 mental model commands

### Bundles
- `plugins` - All essential plugins
- `core` - Daily essentials (tavily + notebooklm)
- `dev` - Development workflow (core + brainstorming + planning)
- `debug` - Debugging toolkit
- `testing` - TDD
- `meta` - Skill creation/repair
- `thinking` - Mental models

## Usage

```bash
# Add this registry
ocx registry add https://<username>.github.io/neo-registry --namespace neo

# Install bundles
ocx add neo/plugins  # Essential plugins
ocx add neo/core     # Daily essentials
ocx add neo/dev      # Full dev workflow
ocx add neo/debug    # When stuck
ocx add neo/thinking # Mental models
```
