<p align="center"><img src="logo.png" width="96" alt="RenewRoster logo"></p>

# RenewRoster MCP Server

Official [Model Context Protocol](https://modelcontextprotocol.io) server for [RenewRoster](https://renewroster.com), the domain and SSL certificate expiry tracker for agencies. Ask your AI assistant what is expiring, add domains and SSL hosts, re-check assets, manage clients, alerts, reminders and webhooks, and pull monthly renewal reports.

This is a **hosted remote server**: nothing to install or run. This repository holds documentation and registry metadata only.

- **Endpoint:** `https://app.renewroster.com/mcp` (Streamable HTTP)
- **Registry name:** `com.renewroster/renewroster`
- **Docs:** https://renewroster.com/docs/mcp · REST API: https://renewroster.com/docs/api · OpenAPI: https://renewroster.com/docs/openapi.yaml

## Get an API key

1. Sign in at https://app.renewroster.com (free sign-up available).
2. Open the **API** tab in your dashboard and create a key.
3. Choose **read-only** or **read + write**. Keys look like `rr_live_...`; treat them like passwords.

Send it as `Authorization: Bearer rr_live_YOUR_KEY`.

## Configure your client

### Claude Code
```bash
claude mcp add --transport http renewroster https://app.renewroster.com/mcp \
  --header "Authorization: Bearer rr_live_YOUR_KEY"
```

### Claude Desktop
Claude Desktop connects to remote servers with headers through `mcp-remote` (`claude_desktop_config.json`):
```json
{
  "mcpServers": {
    "renewroster": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://app.renewroster.com/mcp",
               "--header", "Authorization: Bearer ${RR_API_KEY}"],
      "env": { "RR_API_KEY": "rr_live_YOUR_KEY" }
    }
  }
}
```

### Cursor (`~/.cursor/mcp.json`)
```json
{
  "mcpServers": {
    "renewroster": {
      "url": "https://app.renewroster.com/mcp",
      "headers": { "Authorization": "Bearer rr_live_YOUR_KEY" }
    }
  }
}
```

### VS Code (`.vscode/mcp.json`)
```json
{
  "inputs": [
    { "id": "rr-key", "type": "promptString", "description": "RenewRoster API key", "password": true }
  ],
  "servers": {
    "renewroster": {
      "type": "http",
      "url": "https://app.renewroster.com/mcp",
      "headers": { "Authorization": "Bearer ${input:rr-key}" }
    }
  }
}
```

### Windsurf (`~/.codeium/windsurf/mcp_config.json`)
```json
{
  "mcpServers": {
    "renewroster": {
      "serverUrl": "https://app.renewroster.com/mcp",
      "headers": { "Authorization": "Bearer rr_live_YOUR_KEY" }
    }
  }
}
```

## Tools (28)

Write tools need a read + write key. Destructive tools are annotated so clients ask for confirmation.

| Tool | Access | Description |
|------|--------|-------------|
| `list_expiring` | Read | Domains and certificates expiring within N days (default 30), including expired |
| `list_assets` | Read | All tracked assets with filters: type, client, status, expiring_within |
| `get_asset` | Read | One asset with recent check history |
| `list_clients` | Read | Clients with asset counts |
| `lookup_domain` | Read | Live RDAP/WHOIS lookup of any domain (not saved) |
| `lookup_cert` | Read | Live TLS check of a public host's certificate (not saved) |
| `add_domain` | Write | Start tracking a domain, optionally under a client |
| `add_ssl_host` | Write | Start tracking a host's SSL certificate |
| `check_now` | Write | Re-check one asset now (rate-limited) |
| `add_client` | Write | Create a client |
| `delete_asset` | Write · ⚠️ destructive | Stop tracking an asset. Marked destructive, so clients ask before running it |
| `get_asset_history` | Read | Check history for one asset |
| `lookup_ct` | Read | Latest certificate for a hostname from Certificate Transparency logs |
| `get_agency` | Read | Plan, usage against limits and API rate limit |
| `list_members` | Read | Team members and roles |
| `list_alert_channels` | Read | Alert channels (URLs masked) |
| `get_reminders` | Read | Reminder schedule |
| `get_report` | Read | Monthly renewal report for a YYYY-MM period |
| `list_webhooks` | Read | Webhooks and last delivery |
| `list_webhook_deliveries` | Read | Delivery log for one webhook |
| `update_asset` | Write | Change client, who pays, auto-renew, cost, per-asset reminder days, or rename |
| `import_assets` | Write | Bulk add up to 1000 domains/SSL hosts; refused if it would pass the plan limit |
| `set_reminders` | Write | Set the agency reminder schedule |
| `add_alert_channel` | Write | Add an email, Slack, Discord or webhook alert channel |
| `update_alert_channel` | Write | Change or pause an alert channel |
| `test_alert_channel` | Write | Send a real sample alert to a channel (5/hour) |
| `delete_alert_channel` | Write · ⚠️ destructive | Delete an alert channel. Marked destructive |
| `update_webhook` | Write | Change or pause a webhook |

## Example prompts

- "What domains or SSL certificates expire in the next 30 days?"
- "Start tracking example.com and its SSL certificate under client Acme."
- "Import these 40 domains for client Globex."
- "Re-check shop.example.com now and show its history."
- "Add a Slack alert channel and send a test alert."
- "Give me the renewal report for 2026-10."

## Rate limits

API access is included on every plan:

| Plan | Requests / minute |
|------|-------------------|
| Free & trial | 60 |
| Paid | 300 |

Some tools have extra limits (e.g. `test_alert_channel` 5/hour). Billing, member invites, API key management, branding and agency deletion are dashboard-only.

## Support

support@renewroster.com · https://renewroster.com

## License

Documentation in this repository is MIT licensed. See [LICENSE](LICENSE).
