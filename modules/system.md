# System API Module

## Overview

The System API module manages global device settings, module switches, and language configuration. It provides control over system-wide features and device capabilities.

## Table of Contents

- [Global Module](#global-module)
- [Language Module](#language-module)
- [SNTP Module](#sntp-module)
- [Host Module](#host-module)

## Global Module

### Get Module State Information

Retrieves the enabled/disabled status of various device modules and features.

**Endpoint**: `GET /api/global/module-switch`

**Request**: None

**Response**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <ussd_enabled>1</ussd_enabled>
    <bbou_enabled>1</bbou_enabled>
    <sms_enabled>1</sms_enabled>
    <sdcard_enabled>1</sdcard_enabled>
    <wifi_enabled>1</wifi_enabled>
    <statistic_enabled>1</statistic_enabled>
    <help_enabled>1</help_enabled>
    <stk_enabled>1</stk_enabled>
    <pb_enabled>1</pb_enabled>
    <dlna_enabled>0</dlna_enabled>
    <ota_enabled>1</ota_enabled>
    <wifioffload_enabled>0</wifioffload_enabled>
    <cradle_enabled>0</cradle_enabled>
    <multissid_enable>0</multissid_enable>
    <ipv6_enabled>0</ipv6_enabled>
    <monthly_volume_enabled>1</monthly_volume_enabled>
    <powersave_enabled>0</powersave_enabled>
    <sntp_enabled>1</sntp_enabled>
    <encrypt_enabled>1</encrypt_enabled>
    <dataswitch_enabled>1</dataswitch_enabled>
    <poweroff_enabled>0</poweroff_enabled>
</response>
```

#### Module Switches

| Field | Description | Values |
|-------|-------------|--------|
| `ussd_enabled` | USSD functionality | 0=Disabled, 1=Enabled |
| `sms_enabled` | SMS functionality | 0=Disabled, 1=Enabled |
| `wifi_enabled` | WiFi functionality | 0=Disabled, 1=Enabled |
| `pb_enabled` | Phonebook functionality | 0=Disabled, 1=Enabled |
| `stk_enabled` | SIM Toolkit | 0=Disabled, 1=Enabled |
| `ota_enabled` | Over-the-air updates | 0=Disabled, 1=Enabled |
| `dlna_enabled` | DLNA media sharing | 0=Disabled, 1=Enabled |
| `sdcard_enabled` | SD card support | 0=Disabled, 1=Enabled |
| `ipv6_enabled` | IPv6 support | 0=Disabled, 1=Enabled |
| `encrypt_enabled` | Encryption support | 0=Disabled, 1=Enabled |
| `poweroff_enabled` | Software power off | 0=Disabled, 1=Enabled |

## Language Module

### Language Configuration

Controls device language settings and localization.

**Note**: Language endpoints vary by firmware version and may require special access.

#### Supported Languages

Common language codes include:
- `en-us` - English (US)
- `en-gb` - English (UK)
- `de-de` - German
- `fr-fr` - French
- `es-es` - Spanish
- `it-it` - Italian
- `ru-ru` - Russian
- `zh-cn` - Chinese (Simplified)
- `ja-jp` - Japanese

#### Language Operations

- **Get current language**: Retrieve active language
- **Set language**: Change device language
- **List supported languages**: Available language options

## SNTP Module

### Network Time Protocol

Manages automatic time synchronization with NTP servers.

#### SNTP Configuration

Typical SNTP settings include:
- **NTP server address**: Time server URL/IP
- **Update interval**: Synchronization frequency
- **Timezone settings**: Local time offset
- **Automatic updates**: Enable/disable sync

#### Time Management

- **Manual time setting**: Set device time manually
- **Automatic synchronization**: NTP-based time updates
- **Timezone configuration**: Regional time settings

## Host Module

### Device Hostname and Network Identity

Manages device network identification and hostname settings.

#### Host Configuration

- **Device hostname**: Network identification name
- **DHCP hostname**: Name broadcast to DHCP clients
- **Network identity**: Device network presence

## Usage Examples

### Check Module Status

```bash
curl -X GET http://192.168.8.1/api/global/module-switch
```

### Check Available Features

```bash
# Parse module switches to determine available functionality
curl -s http://192.168.8.1/api/global/module-switch | grep -E "(sms_enabled|wifi_enabled|ota_enabled)"
```

## System Configuration

### Feature Availability

The module switches indicate which features are:
- **Hardware supported**: Physical capability exists
- **Software enabled**: Feature is active in firmware
- **Configuration allowed**: Can be modified by user

### Dependencies

Some modules have dependencies:
- **WiFi modules**: Require `wifi_enabled=1`
- **SMS modules**: Require `sms_enabled=1`
- **OTA updates**: Require `ota_enabled=1`

## Error Handling

### Common Error Codes

- **100001**: No support (feature not available)
- **100002**: Not available (temporary unavailable)
- **100003**: Not supported (hardware limitation)

### Implementation Notes

1. **Feature Detection**: Check module switches before using features
2. **Fallback Handling**: Provide alternatives for disabled features
3. **Error Recovery**: Handle unsupported operations gracefully

## Integration Considerations

### ModemOpus Integration

System APIs are used in ModemOpus for:
- **Feature detection**: Determine available functionality
- **Capability checking**: Validate operations before execution
- **System monitoring**: Track system-wide status
- **Configuration management**: System-level settings

### Best Practices

1. **Capability Detection**: Check feature availability before use
2. **Graceful Degradation**: Handle missing features appropriately
3. **Configuration Validation**: Verify settings before application
4. **System Monitoring**: Track system health and status

## Related APIs

- [Device API](device.md) - Device control and information
- [Security API](security.md) - System security settings
- [Network API](network.md) - Network configuration