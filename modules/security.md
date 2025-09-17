# Security API Module

## Overview

The Security API module manages authentication, access control, and PIN management for HiLink devices. This includes SIM PIN operations, user authentication, and security-related device settings.

## Table of Contents

- [PIN Module](#pin-module)
- [Security Module](#security-module)
- [User Module](#user-module)
- [Error Handling](#error-handling)
- [Best Practices](#best-practices)

## PIN Module

### PIN Operations

The PIN module manages SIM card PIN verification and management operations.

**Note**: PIN operations are critical security functions. Incorrect PIN entries can lock the SIM card.

#### PIN Status Codes

| Code | Description | Action Required |
|------|-------------|-----------------|
| `0` | No PIN required | None |
| `1` | PIN required | Enter PIN |
| `2` | PUK required | Enter PUK and new PIN |
| `3` | PIN blocked | Contact carrier |

#### Common PIN Endpoints

While specific PIN endpoints vary by firmware, typical operations include:

- **Check PIN Status**: Determine if PIN entry is required
- **Enter PIN**: Unlock SIM with PIN
- **Change PIN**: Modify existing PIN
- **Enable/Disable PIN**: Toggle PIN requirement
- **Enter PUK**: Unlock blocked SIM with PUK

### PIN Error Codes

| Code | Description | Attempts Remaining |
|------|-------------|-------------------|
| `112001` | Wrong PIN | 2 attempts left |
| `112002` | PUK required | PIN blocked |
| `112003` | PUK blocked | Contact carrier |
| `112004` | PUK authentication failed | Decremented |
| `112005` | PUK blocked permanently | No recovery |
| `112006` | Card locked | Hardware issue |

## Security Module

### Security Settings

Security settings control device access and authentication requirements.

#### Access Control

Typical security endpoints include:

- **Authentication status**: Check login requirements
- **Session management**: Handle user sessions
- **Access permissions**: Control feature access
- **Security policies**: Configure security rules

#### Security Configuration

Security settings may include:

- **Password policies**: Minimum length, complexity
- **Session timeouts**: Automatic logout periods
- **Access restrictions**: IP-based limitations
- **Feature locks**: Disable specific functions

### Login Management

#### User Authentication Flow

1. **Check authentication status**
2. **Submit credentials if required**
3. **Receive session token/cookie**
4. **Include authentication in subsequent requests**
5. **Handle session expiration**

#### Session Security

- **Session tokens**: Temporary authentication
- **Session timeout**: Automatic expiration
- **Concurrent sessions**: Multiple user support
- **Session validation**: Token verification

## User Module

### User Management

The User module handles device user accounts and permissions.

#### User Types

| Type | Permissions | Description |
|------|-------------|-------------|
| `Admin` | Full access | Complete device control |
| `User` | Limited access | Basic operations only |
| `Guest` | Read-only | View status only |

#### User Operations

Common user management operations:

- **List users**: Enumerate user accounts
- **Add user**: Create new account
- **Modify user**: Change permissions/password
- **Delete user**: Remove account
- **Current user**: Check logged-in user

### User Session Management

#### Session Information

Typical session details include:

- **Username**: Current logged-in user
- **Session ID**: Unique session identifier
- **Login time**: Session start timestamp
- **Last activity**: Recent activity time
- **Permissions**: User access level

## Error Handling

### Authentication Errors

| Code | Description | Resolution |
|------|-------------|------------|
| `100008` | Wrong password | Verify credentials |
| `100009` | Not logged in | Authenticate first |
| `108001` | Invalid username | Check username |
| `108002` | Invalid password | Verify password |
| `108003` | User already logged in | Use existing session |
| `108006` | Invalid credentials | Check username/password |
| `108007` | Too many users | Wait for session expiry |
| `125001` | Invalid token | Re-authenticate |

### PIN Errors

| Code | Description | Action |
|------|-------------|--------|
| `109005` | Wrong PIN | Retry with correct PIN |
| `112001` | Wrong PIN | 2 attempts remaining |
| `112002` | PUK required | Use PUK to unlock |
| `112003` | PUK blocked | Contact carrier |

## Security Best Practices

### PIN Management

1. **Secure PIN Storage**: Never store PINs in plain text
2. **PIN Validation**: Validate PIN format before submission
3. **Attempt Monitoring**: Track failed PIN attempts
4. **PUK Handling**: Implement PUK entry carefully
5. **Carrier Communication**: Direct users to carrier for PUK issues

### Authentication Security

1. **Strong Passwords**: Enforce password complexity
2. **Session Management**: Implement proper session handling
3. **Token Security**: Protect authentication tokens
4. **Timeout Handling**: Implement automatic logout
5. **Error Handling**: Don't expose sensitive information

### Network Security

1. **HTTPS**: Use encryption when possible
2. **VPN**: Consider VPN for remote access
3. **Firewall**: Implement network-level protection
4. **Access Control**: Restrict API access by IP
5. **Monitoring**: Log security events

## Implementation Examples

### Check Authentication Status

```bash
# Check if authentication is required
curl -X GET http://192.168.8.1/api/user/state-login
```

### PIN Entry Example

```bash
# Enter PIN (exact endpoint varies by firmware)
curl -X POST http://192.168.8.1/api/pin/operate \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><OperType>0</OperType><CurrentPin>1234</CurrentPin></request>"
```

### Session Management Example

```bash
# Login with credentials
curl -X POST http://192.168.8.1/api/user/login \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Username>admin</Username><Password>password</Password></request>"
```

## Integration Considerations

### ModemOpus Integration

Security APIs are integrated in ModemOpus through:

- **Authentication middleware**: Handle device login
- **PIN management**: SIM unlock operations
- **Session tracking**: Maintain device sessions
- **Security monitoring**: Track access attempts
- **Error handling**: Graceful security error management

### Development Considerations

1. **Error Recovery**: Implement robust error handling
2. **Security Logging**: Log security events appropriately
3. **Credential Management**: Secure credential storage
4. **Session Persistence**: Handle session state properly
5. **Timeout Handling**: Manage session expiration

### Production Deployment

1. **Default Credentials**: Change default passwords
2. **Access Control**: Implement proper authentication
3. **Network Security**: Secure network environment
4. **Monitoring**: Monitor security events
5. **Updates**: Keep firmware updated

## Related APIs

- [Device API](device.md) - Device information and control
- [User Management](system.md) - System-wide user settings
- [Monitoring API](monitoring.md) - Security event monitoring

## Firmware Variations

**Important**: Security API implementations vary significantly between:

- Device models
- Firmware versions
- Regional variants
- Carrier customizations

Always test security functionality with your specific device and firmware version before production deployment.