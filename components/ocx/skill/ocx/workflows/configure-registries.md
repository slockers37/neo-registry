<objective>
Connect to component registries to access their components.
</objective>

<process>
1. **Add a registry:**
   ```bash
   ocx registry add https://example.github.io/registry --name myorg
   ```
   
   With authentication (private registries):
   ```bash
   ocx registry add https://internal.company.com --name internal \
     --header "Authorization: Bearer $TOKEN"
   ```

2. **List configured registries:**
   ```bash
   ocx registry list
   ```

3. **Remove a registry:**
   ```bash
   ocx registry remove myorg
   ```

4. **Verify registry works:**
   ```bash
   ocx list myorg  # List components from specific registry
   ```
</process>

<registry_config>
Registries are stored in `ocx.jsonc`:

```jsonc
{
  "registries": {
    "kdco": { "url": "https://ocx.kdco.dev" },
    "neo": { "url": "https://slockers37.github.io/neo-registry" },
    "private": { 
      "url": "https://registry.internal.com",
      "headers": { "Authorization": "Bearer ${REGISTRY_TOKEN}" }
    }
  }
}
```

Environment variables in `${}` are expanded at runtime.
</registry_config>

<success_criteria>
- `ocx registry list` shows the registry
- `ocx list namespace` returns components
- Components install without "registry not found" errors
</success_criteria>
