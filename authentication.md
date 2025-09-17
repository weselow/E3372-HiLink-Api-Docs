# HiLink API Authentication

## Overview

HiLink API authentication varies by firmware version and device configuration. Some versions require session tokens for certain operations, while others allow unrestricted access.

## Authentication Methods

### Token-Based Authentication

Some firmware versions implement session token validation:

#### Token Requirements
- Required for write operations (POST requests)
- May be optional for read operations (GET requests)
- Token format and expiration varies by firmware

#### Token Acquisition
Tokens are typically obtained through the WebUI or authentication endpoints. The exact method depends on the firmware version.

#### Token Usage
When required, tokens are typically included in:
- HTTP headers
- Request parameters
- Request body

### Session Management

#### Session Establishment
1. Access device WebUI
2. Perform authentication if required
3. Extract session information
4. Use session data for API calls

#### Session Persistence
- Sessions may timeout after inactivity
- Some operations may extend session lifetime
- Session behavior varies by firmware

## Configuration Options

### Disabling Token Validation

Token validation can often be disabled by modifying configuration files:

**Warning**: This requires device access via ADB or telnet and may void warranty.

#### Access Device Shell
```bash
adb shell
```

#### Navigate to Configuration
```bash
cd /app/hilink/config
```

#### Edit Configuration
```bash
vi system.xml
```

Find token-related settings and disable validation.

## Firmware Variations

### E3372s-153 Behavior
- Basic operations work without authentication
- Some advanced features may require session
- Token validation can be disabled

### Other Models
Authentication requirements may differ significantly between:
- Device models
- Firmware versions
- Regional variants
- Carrier customizations

## Security Considerations

### Access Control
- Default device access is typically unrestricted on local network
- Admin interface may have separate authentication
- Physical device access provides full control

### Network Security
- API operates over HTTP (not HTTPS)
- Traffic is unencrypted by default
- Secure network environment recommended

### Best Practices
1. Change default admin credentials
2. Use secure network infrastructure
3. Implement application-level authentication
4. Monitor API access patterns
5. Regular firmware updates

## Implementation Examples

### Simple API Call (No Authentication)
```bash
curl -X GET http://192.168.8.1/api/monitoring/status
```

### POST Request with Content Type
```bash
curl -X POST http://192.168.8.1/api/device/control \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>"
```

### Token-Based Request (When Required)
```bash
# Exact implementation varies by firmware
curl -X POST http://192.168.8.1/api/device/control \
     -H "Content-Type: application/xml" \
     -H "X-Requested-With: XMLHttpRequest" \
     -H "__RequestVerificationToken: [TOKEN]" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>"
```

## Troubleshooting

### Authentication Errors

Common authentication-related error codes:
- **100009**: Not logged in
- **108001**: Invalid username
- **108002**: Invalid password
- **108003**: User already logged in
- **108006**: Invalid username or password
- **108007**: Too many users logged in
- **125001**: Invalid token

### Resolution Steps
1. Verify device connectivity
2. Check firmware requirements
3. Validate token if required
4. Review session status
5. Restart device if necessary

## Advanced Configuration

### Multiple User Support
Some firmware versions support multiple concurrent sessions:
- Different user roles (admin, user, guest)
- Session isolation
- Role-based access control

### Custom Authentication
For application integration:
- Implement session management
- Handle token refresh
- Error recovery mechanisms
- Fallback strategies

## Related Documentation

- [Overview](overview.md) - General API principles
- [Error Handling](examples/error-handling.md) - Authentication error codes
- [Security API](modules/security.md) - Security-related endpoints