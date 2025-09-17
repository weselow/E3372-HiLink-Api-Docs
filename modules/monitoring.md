# Monitoring API Module

## Overview

The Monitoring API module provides real-time access to device status information and traffic statistics. This is essential for monitoring connection health, signal quality, and data usage.

## Table of Contents

- [Device Status](#device-status)
- [Traffic Statistics](#traffic-statistics)
- [Status Fields Reference](#status-fields-reference)
- [Traffic Fields Reference](#traffic-fields-reference)
- [Usage Examples](#usage-examples)

## Device Status

### Get Current Status

Retrieves comprehensive device status including connection state, signal strength, and network information.

**Endpoint**: `GET /api/monitoring/status`

**Request**: None

**Response**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <ConnectionStatus>901</ConnectionStatus>
    <WifiConnectionStatus></WifiConnectionStatus>
    <SignalStrength></SignalStrength>
    <maxsignal>5</maxsignal>
    <CurrentNetworkType>19</CurrentNetworkType>
    <CurrentServiceDomain>3</CurrentServiceDomain>
    <RoamingStatus>0</RoamingStatus>
    <BatteryStatus></BatteryStatus>
    <BatteryLevel></BatteryLevel>
    <simlockStatus>0</simlockStatus>
    <WanIPAddress>10.10.10.10</WanIPAddress>
    <WanIPv6Address></WanIPv6Address>
    <PrimaryDns>8.8.8.8</PrimaryDns>
    <SecondaryDns>8.8.4.4</SecondaryDns>
    <PrimaryIPv6Dns></PrimaryIPv6Dns>
    <SecondaryIPv6Dns></SecondaryIPv6Dns>
    <CurrentWifiUser>0</CurrentWifiUser>
    <TotalWifiUser>32</TotalWifiUser>
    <currenttotalwifiuser>32</currenttotalwifiuser>
    <ServiceStatus>2</ServiceStatus>
    <SimStatus>1</SimStatus>
    <WifiStatus>1</WifiStatus>
    <CurrentNetworkTypeEx>101</CurrentNetworkTypeEx>
    <maxsignal>5</maxsignal>
    <wifiindooronly>0</wifiindooronly>
    <wififrequence>0</wififrequence>
    <classify>hilink</classify>
    <flymode>0</flymode>
    <cellroam>1</cellroam>
</response>
```

## Traffic Statistics

### Get Traffic Statistics

Retrieves detailed traffic usage statistics including current session and total usage.

**Endpoint**: `GET /api/monitoring/traffic-statistics`

**Request**: None

**Response**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <CurrentConnectTime>9380</CurrentConnectTime>
    <CurrentUpload>1024000</CurrentUpload>
    <CurrentDownload>5120000</CurrentDownload>
    <CurrentDownloadRate>0</CurrentDownloadRate>
    <CurrentUploadRate>0</CurrentUploadRate>
    <TotalUpload>2048000</TotalUpload>
    <TotalDownload>10240000</TotalDownload>
    <TotalConnectTime>18760</TotalConnectTime>
    <showtraffic>1</showtraffic>
</response>
```

## Status Fields Reference

### Connection Status

| Field | Description | Values |
|-------|-------------|--------|
| `ConnectionStatus` | Current connection state | See [Connection Status Codes](#connection-status-codes) |
| `ServiceStatus` | Service availability | 0=No service, 1=Limited, 2=Available |
| `SimStatus` | SIM card status | 0=No SIM, 1=Valid SIM, 255=Invalid SIM |
| `RoamingStatus` | Roaming indicator | 0=Home, 1=Roaming |
| `simlockStatus` | SIM lock status | 0=Unlocked, 1=Locked |

#### Connection Status Codes

| Code | Description |
|------|-------------|
| `900` | Connecting |
| `901` | Connected |
| `902` | Disconnected |
| `903` | Disconnecting |

### Network Information

| Field | Description | Values |
|-------|-------------|--------|
| `CurrentNetworkType` | Network technology | See [Network Type Codes](#network-type-codes) |
| `CurrentNetworkTypeEx` | Extended network type | Extended technology info |
| `CurrentServiceDomain` | Service domain | 0=No service, 1=CS only, 2=PS only, 3=CS+PS |
| `WanIPAddress` | Public IP address | IPv4 address |
| `WanIPv6Address` | Public IPv6 address | IPv6 address |
| `PrimaryDns` | Primary DNS server | IPv4 address |
| `SecondaryDns` | Secondary DNS server | IPv4 address |

#### Network Type Codes

| Code | Description |
|------|-------------|
| `0` | No service |
| `1` | GSM |
| `2` | GPRS |
| `3` | EDGE |
| `4` | WCDMA |
| `5` | HSDPA |
| `6` | HSUPA |
| `7` | HSPA |
| `8` | TD-SCDMA |
| `9` | HSPA+ |
| `10` | EVDO rev.0 |
| `11` | EVDO rev.A |
| `12` | EVDO rev.B |
| `13` | 1xRTT |
| `14` | UMB |
| `15` | 1xEVDV |
| `16` | 3xRTT |
| `17` | HSPA+ 64QAM |
| `18` | HSPA+ MIMO |
| `19` | LTE |
| `41` | 3G |
| `44` | 4G |
| `45` | 4G+ |

### Signal Information

| Field | Description | Values |
|-------|-------------|--------|
| `SignalStrength` | Signal strength | 0-5 bars (empty if unavailable) |
| `maxsignal` | Maximum signal bars | Usually 5 |

### WiFi Information

| Field | Description | Values |
|-------|-------------|--------|
| `WifiStatus` | WiFi status | 0=Off, 1=On |
| `WifiConnectionStatus` | WiFi connection state | Connection details |
| `CurrentWifiUser` | Connected WiFi users | Number of connected devices |
| `TotalWifiUser` | Maximum WiFi users | Device limit |
| `wififrequence` | WiFi frequency band | 0=2.4GHz, 1=5GHz |
| `wifiindooronly` | Indoor-only mode | 0=No, 1=Yes |

### Power Information

| Field | Description | Values |
|-------|-------------|--------|
| `BatteryStatus` | Battery status | 0=Not charging, 1=Charging |
| `BatteryLevel` | Battery percentage | 0-100% |

### Device Mode

| Field | Description | Values |
|-------|-------------|--------|
| `flymode` | Airplane mode | 0=Off, 1=On |
| `cellroam` | Cellular roaming | 0=Disabled, 1=Enabled |
| `classify` | Device classification | hilink, stick, etc. |

## Traffic Fields Reference

### Current Session

| Field | Description | Unit |
|-------|-------------|------|
| `CurrentConnectTime` | Session duration | Seconds |
| `CurrentUpload` | Session upload | Bytes |
| `CurrentDownload` | Session download | Bytes |
| `CurrentUploadRate` | Current upload speed | Bytes/second |
| `CurrentDownloadRate` | Current download speed | Bytes/second |

### Total Statistics

| Field | Description | Unit |
|-------|-------------|------|
| `TotalConnectTime` | Total connection time | Seconds |
| `TotalUpload` | Total uploaded data | Bytes |
| `TotalDownload` | Total downloaded data | Bytes |

### Configuration

| Field | Description | Values |
|-------|-------------|--------|
| `showtraffic` | Traffic display enabled | 0=Hidden, 1=Visible |

## Usage Examples

### Check Connection Status

```bash
curl -X GET http://192.168.8.1/api/monitoring/status
```

### Monitor Traffic Usage

```bash
curl -X GET http://192.168.8.1/api/monitoring/traffic-statistics
```

### Continuous Monitoring Script

```bash
#!/bin/bash
while true; do
    echo "=== $(date) ==="
    echo "Status:"
    curl -s http://192.168.8.1/api/monitoring/status | grep -E "(ConnectionStatus|SignalStrength|CurrentNetworkType)"
    echo "Traffic:"
    curl -s http://192.168.8.1/api/monitoring/traffic-statistics | grep -E "(CurrentDownloadRate|CurrentUploadRate)"
    echo ""
    sleep 30
done
```

## Polling Strategies

### Real-time Monitoring

For real-time status updates:
- Poll status every 5-10 seconds
- Poll traffic every 10-30 seconds
- Adjust intervals based on requirements

### Efficient Polling

To minimize device load:
- Cache status when connection is stable
- Increase polling frequency during transitions
- Use traffic statistics for bandwidth monitoring

### Error Handling

Handle polling errors gracefully:
- Implement exponential backoff on failures
- Distinguish between network and device errors
- Maintain last known good state

## Integration Considerations

### ModemOpus Integration

In ModemOpus, monitoring APIs are used through:
- `ModemMonitoringBackgroundService` for continuous monitoring
- `TrafficMonitoringService` for bandwidth tracking
- Real-time updates via SignalR hubs
- Performance profiling and SLA monitoring

### Best Practices

1. **Efficient Polling**: Balance update frequency with device performance
2. **State Management**: Track status changes, not just current values
3. **Error Recovery**: Handle temporary connectivity issues
4. **Data Aggregation**: Combine multiple metrics for health assessment
5. **Threshold Monitoring**: Set alerts for critical status changes

## Related APIs

- [Device API](device.md) - Device control and information
- [Dialup API](network.md#dialup-module) - Connection management
- [SMS API](sms.md) - Message monitoring integration