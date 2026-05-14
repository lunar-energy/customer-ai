```
                                               ##                                                      
                                               ##                                                      
                          ########             ##   #     ##  #######   ######    #####                
                       ##############          ##   ##    ##  ##   ###  ##   ##   ###                  
                     #################         ##   ##    ##  ##    ##    ######  ##                   
                    ###################        ##   ##    ##  ##    ## ####  ###  ##                   
                    ####################       ##   ##   ###  ##    ## ###   ###  ##                   
########################################       ###   #### ##  ##    ##  ##### ### ##                   
####################                                                                                   
####################                                                                                   
 ##################                              #####   ## ####     #####   ######  ##### ## ##     ##
  ###############                              ##    ##  ###   ##  ###   ### ####   ###   ### ###   ###
     ##########                               ########## ##    ##  ######### ###    ##     ##  ###  ## 
                                              #########  ##    ##  ######### ###    ##     ##   ## ##  
                                               ##    ##  ##    ##  ###   ### ###    ##    ###   #####  
                                                ######   ##    ##   #######  ###     ########    ###   
                                                                                          ###    ##    
                                                                                     #######   ####    
```

# Lunar Energy Customer AI Tools

The Gridshare Customer MCP server exposes your energy data to AI assistants that support the [Model Context Protocol](https://modelcontextprotocol.io/) (MCP). Once connected, the AI can answer natural-language questions about your devices, sites, and telemetry — for example: *"What was my battery's average state of charge last week?"* or *"Which sensor IDs should I query for solar production?"*

Authentication uses the same OAuth 2.1 flow as the Customer API — you log in with your Gridshare account credentials. No additional secrets or API keys are needed.

Supported clients:

- [Claude Desktop](#claude-desktop) — macOS and Windows
- [ChatGPT](#chatgpt) — web and desktop (Plus, Pro, Team, Enterprise, or Edu plan required)
- [Gemini CLI](#gemini-cli) — macOS, Windows, and Linux

---

## Claude Desktop

### Prerequisites

* [Claude Desktop](https://claude.ai/download)
* Node.js 18+ installed locally (required for `mcp-remote`)

### Configuration

Add the following block to your Claude Desktop configuration file.

**macOS** — `~/Library/Application Support/Claude/claude_desktop_config.json`\
**Windows** — `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "gridshare-customer": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://<MCP_SERVER_URL>/mcp"
      ]
    }
  }
}
```

> ℹ️ Replace the URL with the one provided to you during onboarding. Contact [developers@gridshare.com](mailto:developers@gridshare.com) if you haven't received it.

After saving the file, restart Claude Desktop. On first use you will be redirected to the Gridshare login page in your browser to authorise the connection. The session is then stored locally — subsequent startups connect automatically.

### Troubleshooting

**No tools appear in Claude Desktop**\
Ensure Node.js is on your PATH (`node --version`). Restart Claude Desktop after editing the config file.

**Re-authorising**\
If you need to log in again (e.g. after a password change), delete the cached token stored by `mcp-remote`:

```bash
rm -rf ~/.mcp-auth
```

Then restart Claude Desktop — the OAuth flow will start again automatically.

---

## ChatGPT

ChatGPT connects to remote MCP servers directly over HTTPS — no local bridge or Node.js installation is needed. This feature requires a **Plus, Pro, Team, Enterprise, or Edu** subscription.

### Configuration

1. Open ChatGPT and go to **Settings → Connectors**.
2. Click **Add custom connector**.
3. Paste your MCP server URL (`https://<MCP_SERVER_URL>/mcp`) and click **Connect**.
4. ChatGPT will open the Gridshare login page in your browser. Sign in with your Gridshare credentials to authorise the connection.
5. Once authorised, select which tools to enable and save.

The connector will appear in the tool picker during conversations. ChatGPT will ask for confirmation before executing any action that modifies data.

> ℹ️ Replace the URL with the one provided to you during onboarding. Contact [developers@gridshare.com](mailto:developers@gridshare.com) if you haven't received it.

### Troubleshooting

**Connector option is not visible**\
Confirm your ChatGPT subscription includes MCP support (Plus or above). The feature may also require **Developer mode** to be enabled: Settings → Connectors → Advanced → toggle **Developer mode** on.

**Re-authorising**\
Go to **Settings → Connectors**, find the Gridshare connector, and click **Disconnect**. Then reconnect using the steps above.

---

## Gemini CLI

### Prerequisites

* [Gemini CLI](https://github.com/google-gemini/gemini-cli) installed (`npm install -g @google/gemini-cli`)

### Configuration

Add the following to your Gemini CLI settings file.

**macOS / Linux** — `~/.gemini/settings.json`\
**Windows** — `%USERPROFILE%\.gemini\settings.json`

```json
{
  "mcpServers": {
    "gridshare-customer": {
      "url": "https://<MCP_SERVER_URL>/mcp",
      "oauth": {
        "enabled": true
      }
    }
  }
}
```

> ℹ️ Replace the URL with the one provided to you during onboarding. Contact [developers@gridshare.com](mailto:developers@gridshare.com) if you haven't received it.

After saving the file, start or restart the Gemini CLI (`gemini`). Run `/mcp auth` to trigger the OAuth flow — you will be redirected to the Gridshare login page in your browser. Once authorised, the connection is cached and reconnects automatically on subsequent sessions.

### Troubleshooting

**No tools appear**\
Run `/mcp` inside the Gemini CLI to list connected servers and their status. Check that the URL in `settings.json` is correct and reachable.

**Re-authorising**\
Run `/mcp auth` inside the Gemini CLI to restart the OAuth flow.

---

## Available tools

The following tools are available across all supported clients once connected:

| Tool | Description |
| --- | --- |
| `list_sites` | List the site IDs the authenticated customer has access to. |
| `list_devices` | List all devices the authenticated customer owns. |
| `show_device` | Show metadata for a single device (device type, site, publishing partners). |
| `get_device_operation_mode` | Get the current operation mode (`simple`, `schedule`, or `smart`) for a device. |
| `get_device_overlay_plan` | Get the scheduled overlay plan for a device — the sequence of commands (charge, idle, setpoint, heat water) that will execute in order, and when they start. |
| `get_site_topology` | Get the electrical topology of a site — the sensor IDs and channels needed to query telemetry. |
| `get_device_telemetry` | Fetch time-bucketed telemetry for a sensor stream. Accepts ISO 8601 intervals and resolutions (PT15M, PT1H, P1D). |
