# Project Brief: OCX Skills & Ghost Mode Setup

## Vision
Complete the neo-registry with essential skills for OCX management, MCP integrations, and configure a robust ghost mode profile for portable personal AI config.

## Current State
- neo-registry deployed at https://slockers37.github.io/neo-registry
- 8 skills, 2 commands, 3 plugins, 7 bundles working
- Ghost mode not configured
- Missing: ocx skill, tavily skill_mcp, notebooklm skill_mcp, n8n-mcp skill

## Goals
1. **OCX Skill**: Comprehensive skill for working with ocx (registries, ghost mode, components)
2. **MCP Skills with skill_mcp**: tavily, notebooklm, n8n-mcp - using skill_mcp pattern to avoid exposing API keys
3. **Ghost Mode Profile**: Default profile with dev, plugins, meta bundles + ocx skill
4. **Security**: No API keys in public registry - use environment variables via skill_mcp

## Constraints
- Public GitHub repo - no secrets
- Skills must use skill_mcp tool pattern for MCP access
- Use create-agent-skills skill for robust skill creation

## Success Criteria
- [ ] ocx skill in registry with full documentation
- [ ] tavily skill updated to use skill_mcp (no hardcoded MCP config)
- [ ] notebooklm skill updated to use skill_mcp
- [ ] n8n-mcp skill created with skill_mcp
- [ ] Ghost mode initialized with default profile
- [ ] Default profile includes: dev, plugins, meta, ocx skill
- [ ] All new skills added to registry and pushed

## Owner
Neo
