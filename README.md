``` 
                                               @@                                                      
                                               @@                                                      
                          @@@@@@@@             @@   @     @@  @@@@@@@    @@@@@    @@@@@                
                       @@@@@@@@@@@@@@          @@   @@    @@  @@   @@@  @@   @@   @@@                  
                     @@@@@@@@@@@@@@@@@         @@   @@    @@  @@    @@    @@@@@@  @@                   
                    @@@@@@@@@@@@@@@@@@@        @@   @@    @@  @@    @@ @@@@@@@@@  @@                   
                    @@@@@@@@@@@@@@@@@@@@       @@   @@   @@@  @@    @@ @@@   @@@  @@                   
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@       @@@  @@@@@@@@  @@    @@  @@@@@ @@@ @@                   
@@@@@@@@@@@@@@@@@@@@                                                                                   
@@@@@@@@@@@@@@@@@@@@                                                                                   
 @@@@@@@@@@@@@@@@@@                              @@@@@   @@ @@@@     @@@@@   @@@@@@   @@@@ @@ @@     @@
  @@@@@@@@@@@@@@@                              @@@   @@  @@@   @@  @@@   @@@ @@@@   @@@   @@@ @@@   @@@
     @@@@@@@@@@                               @@@@@@@@@@ @@    @@  @@@@@@@@@ @@@    @@     @@  @@   @@ 
                                              @@@@@@@@@  @@    @@  @@@@@@@@@ @@@    @@     @@   @@ @@  
                                               @@    @@  @@    @@  @@@   @@@ @@@    @@    @@@   @@@@@  
                                                @@@@@@   @@    @@   @@@@@@@  @@@     @@@@@@@@    @@@   
                                                                                          @@@    @@    
                                                                                    @@@@@@@@   @@@@                                                                     
```

# Lunar Energy Customer AI Tools

The Lunar Gridshare MCP server exposes your energy data to AI assistants that support the [Model Context Protocol](https://modelcontextprotocol.io/) (MCP). Once connected, the AI can answer natural-language questions about your devices, sites, and telemetry — for example: *"What was my battery's average state of charge last week?"*, *"Show me my excess solar for the last week"* or *"Show me my Lunar battery plan for today"*.

Log in with your Lunar System or Gridshare account credentials. This uses an OAuth 2.1 flow - no additional secrets or API keys are needed.

Supported clients:

- [Claude Desktop](#claude-desktop)
- [ChatGPT](#chatgpt) — web and desktop
- [Gemini CLI](#gemini-cli)

---

## Claude Desktop

### Prerequisites

* [Claude Desktop](https://claude.ai/download)
* [Node.js](https://nodejs.org/en/download) 18+ installed (required for `mcp-remote`)

### Configuration

1. Add the following block to your Claude Desktop configuration file.

**macOS** — `~/Library/Application Support/Claude/claude_desktop_config.json`\
**Windows** — `%APPDATA%\Claude\claude_desktop_config.json`\
**Linux** — `~/.config/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "Lunar Gridshare": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.customer.mygridshare.com/mcp"
      ]
    }
  }
}
```

2. After saving the file, restart Claude Desktop.
3. Go to *Customize > Connectors* and next to 'Lunar Gridshare', click 'Connect'.  On first use you will be redirected to the Gridshare login page in your browser to authorise the connection. The session is then stored locally — subsequent startups connect automatically.

### Troubleshooting

**No tools appear in Claude Desktop**\
- Node.js definitely installed?
- Restart Claude Desktop after editing the config file.

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

1. Open ChatGPT and go to **Plugins → Manage** (gear icon in the top-right).
2. Click on the **MCPs** tab and '**+ Add server**'.
3. Give it a name: `Lunar Gridshare`
4. Switch the type to **Streamable HTTP**
5. Paste your MCP server URL: `https://mcp.customer.mygridshare.com/mcp` and click **Save**.
6. Click the **Authenticate** button to launch a login page in your browser. Sign in with your Lunar/Gridshare credentials to authorise the connection.

That's ready to use - you should be able to prompt away.

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
    "Lunar Gridshare": {
      "url": "https://mcp.customer.mygridshare.com/mcp",
      "oauth": {
        "enabled": true
      }
    }
  }
}
```

After saving the file, start or restart the Gemini CLI (`gemini`). Run `/mcp auth` to trigger the OAuth flow — you will be redirected to the Lunar Gridshare login page in your browser. Once authorised, the connection is cached and reconnects automatically on subsequent sessions.

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
| `show_site` | Show a single site's customer-visible attributes — address, site reference, and its configured import/export tariff IDs. |
| `get_site_topology` | Get the electrical topology of a site — the sensor IDs and channels needed to query telemetry. |
| `get_site_current_state` | Get a site's current state — the latest reading for every sensor plus each controllable device's operation mode and overlay plan. |
| `get_site_telemetry` | Fetch time-series telemetry for all sensors at a site over any time window, each stream labelled with its role (battery, pv, grid, inverter, consumption). |
| `list_site_savings` | List a site's cost savings versus a consumption-only baseline, broken down by month. |
| `list_devices` | List all devices the authenticated customer owns. |
| `show_device` | Show metadata for a single device (device type, site, publishing partners). |
| `get_device_operation_mode` | Get the current operation mode (`simple`, `schedule`, or `smart`) for a device. |
| `get_device_overlay_plan` | Get the scheduled overlay plan for a device — the sequence of commands (charge, idle, setpoint, heat water) that will execute in order, and when they start. |
| `get_device_telemetry` | Fetch time-bucketed telemetry for a single sensor stream. Accepts ISO 8601 intervals and resolutions (PT15M, PT1H, P1D). |
| `show_tariff` | Show a tariff configured on one of the customer's sites — its metadata and a page of price timeseries values. |
