# Handle OAuth Authentication

<objective>
Authenticate to OAuth-protected MCP servers and manage credentials.
</objective>

<process>
## Step 1: Recognize OAuth Errors

MCPorter auto-detects OAuth when you get:
- 401 Unauthorized
- 403 Forbidden
- Server returns OAuth discovery metadata

## Step 2: Pre-authenticate to a Server

```bash
npx mcporter auth https://mcp-server.example.com
```

This:
1. Discovers OAuth endpoints
2. Opens browser for login
3. Caches credentials locally

## Step 3: Use Full TTY Mode for Interactive OAuth

If browser auth doesn't work in your terminal:
```bash
npx mcporter iterm
```

Then run auth commands in the interactive session.

## Step 4: Clear Cached Credentials

```bash
npx mcporter auth https://mcp-server.example.com --clear
```

Use when:
- Token expired and auto-refresh failed
- You need to switch accounts
- Debugging auth issues

## Step 5: Troubleshooting

**OAuth not starting:**
- Ensure the server URL is HTTPS (HTTP needs `--allow-http`)
- Check if server supports OAuth discovery

**Token refresh failing:**
- Clear credentials and re-authenticate
- Check if refresh token expired (some servers have short expiry)

**Behind proxy:**
- Set `HTTPS_PROXY` environment variable
- Some OAuth flows may not work through proxies

## Credential Storage

Credentials are cached in:
- macOS: Keychain
- Linux: Secret Service / keyring
- Fallback: `~/.mcporter/credentials` (encrypted)
</process>

<success_criteria>
- User can authenticate to OAuth-protected MCP servers
- Credentials are cached and reused automatically
- User can clear and refresh credentials when needed
</success_criteria>
