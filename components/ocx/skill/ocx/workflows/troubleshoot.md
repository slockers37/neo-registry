<objective>
Diagnose and fix common OCX issues.
</objective>

<common_issues>

## "Component not found in registry"

**Causes:**
- Registry not added
- Wrong namespace
- Component doesn't exist

**Fix:**
```bash
# Check configured registries
ocx registry list

# Add missing registry
ocx registry add URL --name namespace

# List available components
ocx list namespace
```

---

## "File already exists" / Conflict

**Causes:**
- Component was manually installed
- Different component owns the file

**Fix:**
```bash
# Force overwrite
ocx add namespace/component --force

# Or remove conflicting files first
rm -rf .opencode/skill/conflicting-skill/
```

---

## Ghost mode "symlink error"

**Causes:**
- Permissions issue
- Existing ghost session

**Fix:**
```bash
# Kill any hanging sessions
pkill -f "ocx ghost"

# Reinitialize ghost
ocx ghost init --force
```

---

## "Registry fetch failed"

**Causes:**
- Network issue
- Invalid registry URL
- CORS (for browser-based tools)

**Fix:**
```bash
# Test registry manually
curl -s https://registry-url/index.json

# Check .well-known
curl -s https://registry-url/.well-known/ocx.json

# For GitHub Pages, ensure .nojekyll exists
```

---

## Components not updating

**Causes:**
- Lock file pinning version
- Cache issue

**Fix:**
```bash
# Force update
ocx update namespace/component --force

# Or remove lock and reinstall
rm ocx.lock
ocx add namespace/component
```

</common_issues>

<debug_checklist>
1. [ ] `ocx registry list` - Registry configured?
2. [ ] `curl registry-url/index.json` - Registry accessible?
3. [ ] `ocx list namespace` - Component listed?
4. [ ] `cat ocx.lock` - Check locked versions
5. [ ] `ls .opencode/` - Files actually installed?
</debug_checklist>
