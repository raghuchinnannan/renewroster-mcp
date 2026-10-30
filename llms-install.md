# Installing the RenewRoster MCP server (for AI agents such as Cline)

RenewRoster is a **hosted remote server**. Do not clone, build, or run anything locally.

1. Ask the user for a RenewRoster API key. They create one at https://app.renewroster.com in the **API** tab (free sign-up available). Keys start with `rr_live_`. A read-only key is enough to query expiries; use a read + write key to add domains, SSL hosts, clients, or alerts.
2. Add this entry to the MCP settings file (for Cline: `cline_mcp_settings.json`), replacing `rr_live_YOUR_KEY` with the user's key:

```json
{
  "mcpServers": {
    "renewroster": {
      "type": "streamableHttp",
      "url": "https://app.renewroster.com/mcp",
      "headers": { "Authorization": "Bearer rr_live_YOUR_KEY" },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

3. Verify: list the tools and ask for domains or SSL certificates expiring in the next 30 days.

Troubleshooting:
- `401 Unauthorized`: the key is missing, mistyped, or revoked. The header must be exactly `Authorization: Bearer rr_live_...`.
- Write tools fail: the key is read-only.
- Never commit the key to a repository.

Docs: https://renewroster.com/docs/mcp
