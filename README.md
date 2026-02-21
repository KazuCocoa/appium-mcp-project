# Appium MCP Project

This project provides Appium mobile automation capabilities through the Model Context Protocol (MCP).

## Structure

```
.
├── .vscode/
│   └── mcp.json              # MCP server configuration
└── config/
    └── capabilities.json      # Appium capabilities for different platforms
```

## Configuration

### MCP Server (`.vscode/mcp.json`)

Defines the Appium MCP server connection:

- **Server**: `appium-mcp` (typically configured as a stdio MCP server in VSCode)
- **Environment**: `CAPABILITIES_CONFIG` points to the capabilities file (optional, but recommended)

Note: If your VSCode config uses `npx appium-mcp@latest`, it requires network access. In restricted
environments, prefer a local checkout of `appium/appium-mcp` or the Codex SSE method below.

### Capabilities (`config/capabilities.json`)

Platform-specific Appium capabilities:

- **android**: Android device/emulator capabilities
- **ios**: iOS device/simulator capabilities
- **general**: Platform-agnostic capabilities (passed through to Appium as-is)

## Usage

### Using With Codex (recommended)

In this repo, the most reliable way to use Appium MCP with Codex is to run the MCP server as a
**streamable HTTP (SSE) server** and point Codex at the URL. This avoids Codex managing an MCP
process over stdio (which can show up as `Transport closed`).

1. Start Appium MCP in HTTP stream mode (from a VSCode terminal or anywhere):

```bash
export CAPABILITIES_CONFIG="$PWD/config/capabilities.json"

# Option A: run from a local checkout of https://github.com/appium/appium-mcp
node /path/to/appium-mcp/dist/index.js --httpStream --port=8080

# Option B: if you have it installed on your PATH
# mcp-appium --httpStream --port=8080
```

This exposes an SSE endpoint at `http://127.0.0.1:8080/sse`.

2. Register that URL in Codex:

```bash
codex mcp add appium_http --url http://127.0.0.1:8080/sse
```

3. Restart Codex (new chat/session) so it reloads MCP servers/tools, then use the tools as usual
   (e.g. `create_session`).

### Creating Sessions

**iOS Session:**

```
create_session with platform="ios" and remoteServerUrl="http://127.0.0.1:4723/"
```

**Android Session:**

```
create_session with platform="android" and remoteServerUrl="http://127.0.0.1:4723/"
```

**General Session (custom capabilities):**

```
create_session with platform="general" and remoteServerUrl="http://127.0.0.1:4723/"
```

Uses capabilities from `config/capabilities.json` under "general" key.

### Available Operations

- `create_session`: Start new Appium session
- `delete_session`: End current session
- `appium_screenshot`: Capture screen
- `appium_find_element`: Locate UI elements
- `appium_click`: Click elements
- `appium_set_value`: Enter text
- `appium_swipe`: Perform swipe gestures
- `appium_scroll`: Scroll screen
- `appium_get_page_source`: Get UI hierarchy

## Remote Server

The MCP server connects to a remote Appium server. Ensure Appium is running before creating sessions:

```bash
appium server
```

Default URL: `http://127.0.0.1:4723/`

## Troubleshooting (common MCP server errors)

### `Transport closed` / MCP server exits immediately

If `.vscode/mcp.json` is configured to use `npx` (or any command that requires network access), the MCP server may fail to start and the client will report a transport error.

- Fix: point `.vscode/mcp.json` at the locally installed binary (`${workspaceFolder}/node_modules/.bin/mcp-appium`) and ensure `npm install` has been run.

If you see `Transport closed` specifically when using **Codex**, prefer the SSE setup described in
[Using With Codex (recommended)](#using-with-codex-recommended) so Codex connects to a long-running
MCP server over HTTP instead of spawning it over stdio.

### Session creation fails (`ECONNREFUSED`, `/status` not reachable)

The MCP server needs a running Appium server.

- Fix: run `appium server` and confirm the URL you pass as `remoteServerUrl` is reachable.
- Fix: ensure your Appium driver (e.g. `uiautomator2` / `xcuitest`) is installed and the device/simulator is available.
