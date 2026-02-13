# Appium Capabilities Configuration

This directory contains Appium capability configurations for the MCP server.

## capabilities.json

Platform-specific Appium capabilities used when creating sessions.

### Structure

```json
{
  "android": { ... },   // Android platform capabilities
  "ios": { ... },       // iOS platform capabilities
  "general": { ... }    // General/custom platform capabilities
}
```

### Platform Keys

#### `android`
Used when calling `create_session` with `platform="android"`

**Common Properties:**
- `appium:app` - Path to .apk file
- `appium:deviceName` - Device name
- `appium:platformVersion` - Android version
- `appium:automationName` - Usually `"UiAutomator2"`
- `appium:udid` - Specific device UDID (optional)

**Example:**
```json
{
  "android": {
    "appium:app": "/path/to/app.apk",
    "appium:deviceName": "Pixel 7",
    "appium:platformVersion": "14.0",
    "appium:automationName": "UiAutomator2"
  }
}
```

#### `ios`
Used when calling `create_session` with `platform="ios"`

**Required Properties:**
- `platformName` - Must be `"iOS"`
- `appium:automationName` - Must be `"XCUITest"`
- `appium:deviceName` - Simulator/device name
- `appium:platformVersion` - iOS version

**Optional Properties:**
- `appium:app` - Path to .app or .ipa file
- `appium:bundleId` - App bundle ID
- `appium:udid` - Specific device UDID

**Example:**
```json
{
  "ios": {
    "platformName": "iOS",
    "appium:deviceName": "iPhone 17",
    "appium:platformVersion": "26.2",
    "appium:automationName": "XCUITest",
    "appium:bundleId": "com.example.myapp"
  }
}
```

#### `general`
Used when calling `create_session` with `platform="general"`

This is a pass-through mode for W3C/Appium capabilities. No platform-specific defaults are applied.

**Required Properties:**
- `platformName` - Platform identifier
- `appium:automationName` - Automation driver name

**Use Cases:**
- Non-Android/iOS platforms (Windows, macOS, tvOS)
- Custom Appium drivers
- Full control over capabilities

**Example:**
```json
{
  "general": {
    "platformName": "iOS",
    "appium:automationName": "XCUITest",
    "appium:deviceName": "iPhone 17",
    "appium:platformVersion": "26.2"
  }
}
```

## Environment Variable

The MCP server reads this file via the `CAPABILITIES_CONFIG` environment variable set in `.vscode/mcp.json`:

```json
{
  "env": {
    "CAPABILITIES_CONFIG": "${workspaceFolder}/config/capabilities.json"
  }
}
```

## Schema

See [capabilities.schema.json](capabilities.schema.json) for the complete JSON schema definition.
