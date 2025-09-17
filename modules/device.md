# Device API Module

## Overview

The Device API module provides access to fundamental device information and control operations. This includes device identification, firmware details, and power management functions.

## Table of Contents

- [Device Information](#device-information)
- [Device Control](#device-control)
- [Version Information](#version-information)
- [Response Formats](#response-formats)
- [Error Handling](#error-handling)

## Device Information

### Get Basic Device Information

Retrieves comprehensive device identification and configuration details.

**Endpoint**: `GET /api/device/information`

**Request**: None

**Response**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <DeviceName>E3372</DeviceName>
    <SerialNumber>G4PDM15709004440</SerialNumber>
    <Imei>861821030014452</Imei>
    <Imsi>26001012345678</Imsi>
    <Iccid>8991101200003204510</Iccid>
    <Msisdn></Msisdn>
    <HardwareVersion>CL1E3372SM</HardwareVersion>
    <SoftwareVersion>21.329.62.00.161</SoftwareVersion>
    <WebUIVersion>17.100.15.02.161</WebUIVersion>
    <MacAddress1>0C:96:BF:89:53:EA</MacAddress1>
    <MacAddress2></MacAddress2>
    <ProductFamily>LTE</ProductFamily>
    <Classify>hilink</Classify>
    <supportmode>LTE|WCDMA|GSM</supportmode>
    <workmode>LTE</workmode>
</response>
```

#### Response Fields

| Field | Description | Example |
|-------|-------------|---------|
| `DeviceName` | Model name | E3372 |
| `SerialNumber` | Device serial number | G4PDM15709004440 |
| `Imei` | International Mobile Equipment Identity | 861821030014452 |
| `Imsi` | International Mobile Subscriber Identity | 26001012345678 |
| `Iccid` | Integrated Circuit Card Identifier | 8991101200003204510 |
| `Msisdn` | Mobile Station ISDN Number (phone number) | |
| `HardwareVersion` | Hardware revision | CL1E3372SM |
| `SoftwareVersion` | Firmware version | 21.329.62.00.161 |
| `WebUIVersion` | Web interface version | 17.100.15.02.161 |
| `MacAddress1` | Primary MAC address | 0C:96:BF:89:53:EA |
| `MacAddress2` | Secondary MAC address (if available) | |
| `ProductFamily` | Technology family | LTE |
| `Classify` | Device classification | hilink |
| `supportmode` | Supported network modes | LTE\|WCDMA\|GSM |
| `workmode` | Current operating mode | LTE |

## Device Control

### Restart Device

Performs a software restart of the device.

**Endpoint**: `POST /api/device/control`

**Request**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Control>1</Control>
</request>
```

**Response**: `OK`

### Shutdown Device

Powers down the device (if supported by hardware).

**Endpoint**: `POST /api/device/control`

**Request**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Control>4</Control>
</request>
```

**Response**: `OK`

#### Control Values

| Value | Action | Description |
|-------|--------|-------------|
| `1` | Restart | Software reboot |
| `4` | Shutdown | Power off (hardware dependent) |

**Note**: Shutdown functionality may not be available on all device models.

## Version Information

### Check for New Version

Queries for available firmware updates.

**Endpoint**: `GET /api/device/autorun-version`

**Request**: None

**Response**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <Version>11.0.2.1(H192SP2C1025)</Version>
</response>
```

#### Response Fields

| Field | Description | Example |
|-------|-------------|---------|
| `Version` | Current or available version | 11.0.2.1(H192SP2C1025) |

## Usage Examples

### Basic Device Information Query

```bash
curl -X GET http://192.168.8.1/api/device/information
```

### Device Restart

```bash
curl -X POST http://192.168.8.1/api/device/control \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>"
```

### Check Firmware Version

```bash
curl -X GET http://192.168.8.1/api/device/autorun-version
```

## Error Handling

### Common Error Codes

- **100004**: System busy (during restart/shutdown)
- **100005**: Unknown error
- **100006**: Invalid parameter (invalid control value)

### Implementation Notes

1. **Restart Behavior**: Device will be temporarily unavailable during restart
2. **Shutdown Support**: Not all devices support software shutdown
3. **Version Checking**: May require internet connection for update queries
4. **Information Caching**: Device information typically doesn't change frequently

## Integration Considerations

### ModemOpus Integration

In ModemOpus, the Device API is used through:
- `HiLinkClient` for HTTP communication
- Device information caching in repositories
- Restart operations in IP change strategies
- Health monitoring through device status

### Best Practices

1. **Cache device information** to reduce API calls
2. **Handle restart gracefully** with appropriate timeouts
3. **Validate control parameters** before sending requests
4. **Monitor device availability** after restart operations

## Related APIs

- [Monitoring API](monitoring.md) - Device status monitoring
- [Security API](security.md) - Device access control
- [System API](system.md) - System-wide configuration