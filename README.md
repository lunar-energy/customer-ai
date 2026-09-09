``` 
                           ++++++         
                        ++++++++++++      
                      ++++++++++++++++    
                     ++++++++++++++++++   
                     ++++++++++++++++++   
   ++++++++++++++++++++++++++++++++++++   
   ++++++++++++++++++                     
   ++++++++++++++++++                     
    ++++++++++++++++                      
      ++++++++++++                        
         ++++++                                                                      
```

# Lunar Energy AI Connector

The Lunar Energy MCP server exposes your energy data to AI assistants that support the [Model Context Protocol](https://modelcontextprotocol.io/) (MCP). Once connected, the AI can answer natural-language questions about your devices, sites, and telemetry — for example: *"What was my battery's average state of charge last week?"*, *"Show me my excess solar for the last week"* or *"Show me my Lunar battery plan for today"*.

Log in with your Lunar Energy account credentials. This uses an OAuth 2.1 flow - no additional secrets or API keys are needed.

Suggested clients:

- [Claude](#claude) - web or desktop
- [ChatGPT](#chatgpt) — web or desktop
- [Gemini CLI](#gemini-cli) - desktop only

Note that some clients require specific subscriptions/tiers to add connectors - please check the documentation for your client.

For a full list clients that can be connected, see [Supported Clients](./supported-clients.md).

---

## Claude

### Configuration

The **Lunar Energy** connector is available in the Anthropic Directory.  Alternatively, you can add it as a custom connector:

1. Go to **Settings** > **Connectors** and **Add** > **Add custom connector**.
2. Name: `Lunar Energy`
3. Remote MCP Server URL: `https://mcp.lunarenergy.com/mcp`
4. Click 'Add' and then click 'Connect' to authenticate with your Lunar Energy credentials.

---

## ChatGPT

### Configuration

1. Open ChatGPT and go to **Plugins → Manage** (gear icon in the top-right).
2. Click on the **MCPs** tab and '**+ Add server**'.
3. Give it a name: `Lunar Energy`
4. Switch the type to **Streamable HTTP**
5. Paste your MCP server URL: `https://mcp.lunarenergy.com/mcp` and click **Save** (no other details required).
6. Click the **Authenticate** button to launch a login page in your browser. Sign in with your Lunar Energy credentials to authorize the connection.

---

## Gemini CLI

### Configuration

Add the following to your Gemini CLI settings file.

**macOS / Linux** — `~/.gemini/settings.json`\
**Windows** — `%USERPROFILE%\.gemini\settings.json`

```json
{
  "mcpServers": {
    "Lunar Energy": {
      "url": "https://mcp.lunarenergy.com/mcp",
      "oauth": {
        "enabled": true
      }
    }
  }
}
```

After saving the file, start or restart the Gemini CLI (`gemini`). Run `/mcp auth` to trigger the OAuth flow — you will be redirected to the Lunar Energy login page in your browser. Once authorized, the connection is cached and reconnects automatically on subsequent sessions.

### Troubleshooting

**No tools appear**\
Run `/mcp` inside the Gemini CLI to list connected servers and their status. Check that the URL in `settings.json` is correct and reachable.

**Re-authorizing**\
Run `/mcp auth` inside the Gemini CLI to restart the OAuth flow.

---

## Available tools

The following tools are available across all supported clients once connected:

| Tool | Description |
| --- | --- |
| `list_sites` | List the homes on your account. |
| `show_site` | Show where a home is and which electricity tariffs it's on. |
| `get_site_topology` | Show what's installed at a home — solar, battery, inverters, EV charger and meters — and how it's wired together. |
| `get_site_current_state` | See what your home is doing right now, and what's scheduled next. |
| `get_site_telemetry` | See your solar, battery, grid and home usage over any period. |
| `list_site_savings` | See how much you've saved on your electricity bill, month by month. |
| `list_site_vpp_programs` | See which grid programs your home takes part in, and what you've earned from them. |
| `list_site_vpp_events` | See when your battery is called on to help the grid — recent and upcoming events. |
| `list_devices` | List the hardware you own — inverters, batteries, EV chargers and meters. |
| `show_device` | Show the details of a single piece of hardware. |
| `get_device_operation_mode` | See how your battery is being controlled. |
| `get_device_overlay_plan` | See what your battery or EV charger is scheduled to do next. |
| `get_device_telemetry` | See readings from a single piece of equipment over any period. |
| `show_tariff` | Show your electricity rates — what you pay, and what you're paid for exporting. |
| `get_data_guide` | Reference notes the AI uses to read your data correctly. |

---

## Privacy Policy

The Privacy Policy governing the AI Connector can be found here: https://www.lunarenergy.com/privacy-policy
