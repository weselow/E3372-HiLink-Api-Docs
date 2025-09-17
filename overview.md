# HiLink API Overview

## General Principles

The HiLink API provides programmatic access to Huawei HiLink devices through HTTP requests. The API is designed around a unified WebUI system that works across different HiLink device types (modems, routers, etc.).

## API Architecture

### Two Operation Modes

The HiLink API operates in two distinct modes:

#### Information Variant (GET)
- **Purpose**: Retrieve information from the device
- **Method**: HTTP GET
- **Response**: Immediate XML data response
- **Example**: `GET http://192.168.8.1/api/monitoring/status`

#### Control/Configuration Variant (POST)
- **Purpose**: Modify device settings or trigger actions
- **Method**: HTTP POST with XML request body
- **Response**: Usually `OK` for successful operations
- **Example**: Device restart, SMS sending, configuration changes

### Unified Endpoint Structure

Most API endpoints support both GET and POST operations using the same URL path:
- GET: Retrieve current settings/status
- POST: Modify settings or trigger actions

## Request/Response Format

### Request Format (POST)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Parameter1>value1</Parameter1>
    <Parameter2>value2</Parameter2>
</request>
```

### Response Format
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <Field1>value1</Field1>
    <Field2>value2</Field2>
</response>
```

### Success Response
For successful POST operations:
```
OK
```

### Error Response
For failed operations, error codes are returned:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<error>
    <code>100006</code>
    <message>Invalid parameter</message>
</error>
```

## API Base URLs

- **Primary**: `http://192.168.8.1/api/`
- **Alternative**: `http://hi.link/api/` (if configured)

## Device Compatibility

### Hardware Independence
The HiLink software is unified across device types. A modem contains the same WebUI files as a router, but certain features may be:
- Disabled by hardware limitations
- Blocked by configuration files
- Not applicable to the device type

### Version Compatibility
API endpoints and responses may vary between firmware versions. The documentation is based on JavaScript files from E3372s-153 modem firmware.

## Module Organization

The API is organized into logical modules:

### Core Modules
- **Device**: Basic device information and control
- **Monitoring**: Status and traffic statistics
- **SMS**: Message management
- **Dialup**: Connection management

### Network Modules
- **WLAN**: WiFi configuration
- **DHCP**: DHCP server settings
- **Net**: Network configuration

### Security Modules
- **Security**: Authentication and access control
- **PIN**: SIM PIN management

### System Modules
- **Global**: Module switches and global settings
- **Language**: Internationalization settings

### Advanced Modules
- **OTA**: Over-the-air updates
- **Diagnosis**: Network diagnostics
- **Voice**: Voice call features

## Session Management

Some firmware versions require session tokens for API access:
- Tokens may be required for certain operations
- Token validation can be disabled through configuration
- Session timeout behavior varies by firmware

## Content Type Requirements

POST requests must include proper content type:
```
Content-Type: application/xml
```

## Rate Limiting

Devices may implement rate limiting to prevent API abuse. Behavior varies by firmware and device model.

## Related Documentation

- [Authentication](authentication.md) - Session management details
- [Error Handling](examples/error-handling.md) - Complete error code reference
- [Common Tasks](examples/common-tasks.md) - Practical implementation examples