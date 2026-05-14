# Lunar Energy Customer AI Tools

The Gridshare Customer MCP server exposes your energy data to AI assistants that support the [Model Context Protocol](https://modelcontextprotocol.io/) (MCP), such as Claude Desktop. Once connected, the AI can answer natural-language questions about your devices, sites, and telemetry — for example: *"What was my battery's average state of charge last week?"* or *"Which sensor IDs should I query for solar production?"*

Authentication uses the same OAuth 2.1 flow as the Customer API — you log in with your Gridshare account credentials. No additional secrets or API keys are needed.

## Prerequisites

* [Claude Desktop](https://claude.ai/download) (or any MCP-compatible client)
* Node.js 18+ installed locally (required for `mcp-remote`)

## Configuration

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
        "https:/<MCP_SERVER_URL>/mcp"
      ]
    }
  }
}
```

> ℹ️ Replace the URL with the one provided to you during onboarding. Contact [developers@gridshare.com](mailto:developers@gridshare.com) if you haven't received it.

After saving the file, restart Claude Desktop. On first use you will be redirected to the Gridshare login page in your browser to authorise the connection. The session is then stored locally — subsequent startups connect automatically.

## Available tools

Once connected, the following tools are available to the AI assistant:

| Tool                        | Description                                                                                                                                                  |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `list_sites`                | List the site IDs the authenticated customer has access to.                                                                                                  |
| `list_devices`              | List all devices the authenticated customer owns.                                                                                                            |
| `show_device`               | Show metadata for a single device (device type, site, publishing partners).                                                                                  |
| `get_device_operation_mode` | Get the current operation mode (`simple`, `schedule`, or `smart`) for a device.                                                                              |
| `get_device_overlay_plan`   | Get the scheduled overlay plan for a device — the sequence of commands (charge, idle, setpoint, heat water) that will execute in order, and when they start. |
| `get_site_topology`         | Get the electrical topology of a site — the sensor IDs and channels needed to query telemetry.                                                               |
| `get_device_telemetry`      | Fetch time-bucketed telemetry for a sensor stream. Accepts ISO 8601 intervals and resolutions (PT15M, PT1H, P1D).                                            |

## Troubleshooting

**No tools appear in Claude Desktop**\
Ensure Node.js is on your PATH (`node --version`). Restart Claude Desktop after editing the config file.

**Re-authorising**\
If you need to log in again (e.g. after a password change), delete the cached token stored by `mcp-remote`:

```bash
rm -rf ~/.mcp-auth
```

Then restart Claude Desktop — the OAuth flow will start again automatically.
