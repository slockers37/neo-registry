# Roadmap: OCX Skills & Ghost Mode Setup

## Milestone: v1.1 - Complete Personal Config System

### Phase 01: OCX Skill Creation
**Objective:** Create comprehensive skill for working with ocx  
**Scope:** Research ocx docs → Create skill with references/workflows → Add to registry  
**Plans:** 2-3  

### Phase 02: Skill MCP Pattern Implementation  
**Objective:** Update tavily, notebooklm skills to use skill_mcp; create n8n-mcp skill  
**Scope:** Understand skill_mcp pattern → Update existing skills → Create n8n skill  
**Plans:** 3-4  

### Phase 03: Ghost Mode Configuration
**Objective:** Initialize ghost mode with default profile  
**Scope:** Run ocx ghost init → Configure ghost.jsonc → Test portable config  
**Plans:** 1-2  

### Phase 04: Registry Update & Verification
**Objective:** Add all new skills to registry, push, verify installs  
**Scope:** Update registry → Push → Test all bundles → Document usage  
**Plans:** 1-2  

---

## Tracking

| Phase | Status | Plans | Notes |
|-------|--------|-------|-------|
| 01 - OCX Skill | pending | 0/3 | Use create-agent-skills |
| 02 - Skill MCP | pending | 0/4 | Security-first approach |
| 03 - Ghost Mode | pending | 0/2 | Bundles: dev, plugins, meta, ocx |
| 04 - Registry | pending | 0/2 | Final verification |

## Dependencies
- Phase 02 depends on understanding skill_mcp from Phase 01 research
- Phase 03 depends on Phase 01 (ocx skill needed in profile)
- Phase 04 depends on all previous phases
