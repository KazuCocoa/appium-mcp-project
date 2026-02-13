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
- **Server**: `appium-mcp` via npx
- **Environment**: `CAPABILITIES_CONFIG` points to capabilities file

### Capabilities (`config/capabilities.json`)

Platform-specific Appium capabilities:

- **android**: Android device/emulator capabilities
- **ios**: iOS device/simulator capabilities
- **general**: Platform-agnostic capabilities (passed through to Appium as-is)

## Usage

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
