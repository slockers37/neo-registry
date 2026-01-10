<required_reading>
Read references/cli-commands.md for full command details.
</required_reading>

<objective>
Install, update, or remove OCX components in the current project.
</objective>

<process>
1. **Ensure OCX initialized**
   ```bash
   ocx init  # If no ocx.jsonc exists
   ```

2. **For installing components:**
   ```bash
   # From registry
   ocx add namespace/component
   ocx add namespace/bundle        # Installs bundle + all deps
   
   # From npm (plugins)
   ocx add npm:package-name
   
   # Multiple at once
   ocx add neo/core neo/plugins neo/debug
   ```

3. **For updating:**
   ```bash
   ocx update                      # All components
   ocx update namespace/component  # Specific one
   ocx diff namespace/component    # Preview changes before update
   ```

4. **For removing:**
   ```bash
   ocx remove namespace/component
   ```

5. **For listing/searching:**
   ```bash
   ocx list                        # Show installed
   ocx list --all                  # Show available from registries
   ocx search query                # Search by name/description
   ```
</process>

<success_criteria>
- Component files appear in `.opencode/` directory
- `ocx.lock` updated with version and hash
- No "component not found" errors
</success_criteria>

<common_issues>
| Issue | Fix |
|-------|-----|
| "Registry not found" | `ocx registry add URL --name alias` first |
| "Component not found" | Check namespace spelling, run `ocx list --all` |
| "File conflict" | Component already exists; use `--force` or remove first |
</common_issues>
