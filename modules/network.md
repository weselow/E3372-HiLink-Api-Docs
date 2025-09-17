# Network API Module

## Overview

The Network API module encompasses networking functionality including connection management (Dialup), WiFi configuration (WLAN), and DHCP server settings. This module is essential for network connectivity and access point configuration.

## Table of Contents

- [Dialup Module](#dialup-module)
- [WLAN Module](#wlan-module)
- [DHCP Module](#dhcp-module)
- [Additional Network Modules](#additional-network-modules)

## Dialup Module

### Get Connection Status

Retrieves comprehensive connection status including network details and service information.

**Endpoint**: `GET /api/dialup/connection`

**Request**: None

**Response**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <RoamingStatus>0</RoamingStatus>
    <ConnectionStatus>901</ConnectionStatus>
    <WifiConnectionStatus></WifiConnectionStatus>
    <SignalStrength></SignalStrength>
    <maxsignal>5</maxsignal>
    <CurrentNetworkType>19</CurrentNetworkType>
    <CurrentServiceDomain>3</CurrentServiceDomain>
    <RoamingStatus>0</RoamingStatus>
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

### Connect/Disconnect

Controls the mobile data connection state.

**Endpoint**: `POST /api/dialup/dial`

**Connect Request**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Action>1</Action>
</request>
```

**Disconnect Request**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Action>0</Action>
</request>
```

**Response**: `OK`

#### Action Values

| Value | Action | Description |
|-------|--------|-------------|
| `1` | Connect | Establish mobile data connection |
| `0` | Disconnect | Terminate mobile data connection |

## WLAN Module

### Get Basic WLAN Settings

Retrieves WiFi network configuration including SSID, channel, and power settings.

**Endpoint**: `GET /api/wlan/basic-settings`

**Request**: None

**Response**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <WifiSsid>HUAWEI-E3372-53EA</WifiSsid>
    <WifiChannel>0</WifiChannel>
    <WifiHide>0</WifiHide>
    <WifiCountry>CE</WifiCountry>
    <WifiMode>b/g/n</WifiMode>
    <WifiRate>0</WifiRate>
    <WifiTxPwrPcnt>100</WifiTxPwrPcnt>
    <WifiMaxassoc>32</WifiMaxassoc>
    <wifiband>2.4GHz</wifiband>
    <wifiautocountryswitch>1</wifiautocountryswitch>
    <wifiantennanum>2</wifiantennanum>
    <wifiguestofftime>0</wifiguestofftime>
</response>
```

#### Response Fields

| Field | Description | Values |
|-------|-------------|--------|
| `WifiSsid` | Network name | String (up to 32 chars) |
| `WifiChannel` | WiFi channel | 0=Auto, 1-14=Specific channel |
| `WifiHide` | Hidden network | 0=Visible, 1=Hidden |
| `WifiCountry` | Country code | CE, US, JP, etc. |
| `WifiMode` | WiFi standards | b, g, n, b/g, b/g/n |
| `WifiRate` | Data rate | 0=Auto, specific Mbps |
| `WifiTxPwrPcnt` | Transmission power | 0-100% |
| `WifiMaxassoc` | Max connections | 1-32 devices |
| `wifiband` | Frequency band | 2.4GHz, 5GHz |
| `wifiautocountryswitch` | Auto country detect | 0=Disabled, 1=Enabled |
| `wifiantennanum` | Antenna count | Number of antennas |
| `wifiguestofftime` | Guest timeout | Minutes (0=disabled) |

### Set Basic WLAN Settings

Configures WiFi network parameters.

**Endpoint**: `POST /api/wlan/basic-settings`

**Request**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <WifiSsid>MY-WIFI-NETWORK</WifiSsid>
    <WifiChannel>0</WifiChannel>
    <WifiHide>0</WifiHide>
    <WifiCountry>CE</WifiCountry>
    <WifiMode>b/g/n</WifiMode>
    <WifiRate>0</WifiRate>
    <WifiTxPwrPcnt>100</WifiTxPwrPcnt>
    <WifiMaxassoc>32</WifiMaxassoc>
    <wifiband>2.4GHz</wifiband>
    <wifiautocountryswitch>1</wifiautocountryswitch>
    <wifiantennanum>2</wifiantennanum>
    <wifiguestofftime>0</wifiguestofftime>
</request>
```

**Response**: `OK`

### Get Security Settings

Retrieves WiFi security configuration including encryption and authentication settings.

**Endpoint**: `GET /api/wlan/security-settings`

**Request**: None

**Response**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <WifiAuthmode>WPA2PSK</WifiAuthmode>
    <WifiBasicencryptionmodes>AES</WifiBasicencryptionmodes>
    <WifiWpaencryptionmodes>AES</WifiWpaencryptionmodes>
    <WifiWpa2encryptionmodes>AES</WifiWpa2encryptionmodes>
    <WifiKey>12345678</WifiKey>
    <WifiRestoredefaults>0</WifiRestoredefaults>
    <WifiKeytype>0</WifiKeytype>
    <wifiwepkeytype>1</wifiwepkeytype>
    <WifiWepKey1></WifiWepKey1>
    <WifiWepKey2></WifiWepKey2>
    <WifiWepKey3></WifiWepKey3>
    <WifiWepKey4></WifiWepKey4>
    <WifiWepKeyIndex>0</WifiWepKeyIndex>
    <WifiWpsStatus>0</WifiWpsStatus>
    <WifiWpsPinCode>12345670</WifiWpsPinCode>
    <wifiwpsversion>0</wifiwpsversion>
    <wifiwpspinmode>0</wifiwpspinmode>
</response>
```

#### Security Fields

| Field | Description | Values |
|-------|-------------|--------|
| `WifiAuthmode` | Authentication mode | OPEN, WEP, WPA, WPA2PSK, WPA/WPA2 |
| `WifiBasicencryptionmodes` | Basic encryption | NONE, WEP, TKIP, AES |
| `WifiWpaencryptionmodes` | WPA encryption | TKIP, AES, TKIP/AES |
| `WifiWpa2encryptionmodes` | WPA2 encryption | TKIP, AES, TKIP/AES |
| `WifiKey` | WiFi password | String (8-63 chars for WPA) |
| `WifiKeytype` | Key type | 0=Passphrase, 1=Hex |
| `WifiWpsStatus` | WPS status | 0=Disabled, 1=Enabled |
| `WifiWpsPinCode` | WPS PIN | 8-digit PIN |

## DHCP Module

### Get DHCP Settings

Retrieves DHCP server configuration including IP range and lease settings.

**Endpoint**: `GET /api/dhcp/settings`

**Request**: None

**Response**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <DhcpStatus>1</DhcpStatus>
    <DhcpStartIPAddress>192.168.8.100</DhcpStartIPAddress>
    <DhcpEndIPAddress>192.168.8.200</DhcpEndIPAddress>
    <DhcpLeaseTime>86400</DhcpLeaseTime>
    <PrimaryDns>192.168.8.1</PrimaryDns>
    <SecondaryDns>192.168.8.1</SecondaryDns>
</response>
```

#### DHCP Fields

| Field | Description | Values |
|-------|-------------|--------|
| `DhcpStatus` | DHCP server status | 0=Disabled, 1=Enabled |
| `DhcpStartIPAddress` | Start IP address | IPv4 address |
| `DhcpEndIPAddress` | End IP address | IPv4 address |
| `DhcpLeaseTime` | Lease duration | Seconds (86400 = 24 hours) |
| `PrimaryDns` | Primary DNS server | IPv4 address |
| `SecondaryDns` | Secondary DNS server | IPv4 address |

### Set DHCP Settings

Configures DHCP server parameters.

**Endpoint**: `POST /api/dhcp/settings`

**Request**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <DhcpStatus>1</DhcpStatus>
    <DhcpStartIPAddress>192.168.8.100</DhcpStartIPAddress>
    <DhcpEndIPAddress>192.168.8.200</DhcpEndIPAddress>
    <DhcpLeaseTime>86400</DhcpLeaseTime>
    <PrimaryDns>192.168.8.1</PrimaryDns>
    <SecondaryDns>192.168.8.1</SecondaryDns>
</request>
```

**Response**: `OK`

## Additional Network Modules

### DDNS Module

**Get DDNS Settings**: `GET /api/ddns/ddns-list`
**Set DDNS Settings**: `POST /api/ddns/ddns-list`

### Cradle Module

**Get Cradle Info**: `GET /api/cradle/basic-info`
**Set Cradle Config**: `POST /api/cradle/basic-info`

## Usage Examples

### Check Connection Status

```bash
curl -X GET http://192.168.8.1/api/dialup/connection
```

### Connect to Mobile Network

```bash
curl -X POST http://192.168.8.1/api/dialup/dial \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>1</Action></request>"
```

### Configure WiFi Network

```bash
curl -X POST http://192.168.8.1/api/wlan/basic-settings \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><WifiSsid>MyNetwork</WifiSsid><WifiChannel>6</WifiChannel><WifiHide>0</WifiHide><WifiCountry>US</WifiCountry><WifiMode>b/g/n</WifiMode><WifiRate>0</WifiRate><WifiTxPwrPcnt>100</WifiTxPwrPcnt><WifiMaxassoc>10</WifiMaxassoc><wifiband>2.4GHz</wifiband><wifiautocountryswitch>1</wifiautocountryswitch><wifiantennanum>2</wifiantennanum><wifiguestofftime>0</wifiguestofftime></request>"
```

### Update DHCP Range

```bash
curl -X POST http://192.168.8.1/api/dhcp/settings \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><DhcpStatus>1</DhcpStatus><DhcpStartIPAddress>192.168.8.50</DhcpStartIPAddress><DhcpEndIPAddress>192.168.8.150</DhcpEndIPAddress><DhcpLeaseTime>43200</DhcpLeaseTime><PrimaryDns>8.8.8.8</PrimaryDns><SecondaryDns>8.8.4.4</SecondaryDns></request>"
```

## Error Handling

### Common Error Codes

- **100006**: Invalid parameter (malformed request)
- **117004**: Wrong WiFi password
- **100004**: System busy (during connection changes)

### Best Practices

1. **Connection Management**: Allow time for connection state changes
2. **WiFi Configuration**: Validate parameters before applying
3. **DHCP Settings**: Ensure IP ranges don't conflict with device IP
4. **Security Settings**: Use strong passwords and modern encryption

## Integration Considerations

### ModemOpus Integration

Network APIs are used in ModemOpus through:
- Connection management in IP change strategies
- WiFi configuration for access point setup
- DHCP management for client connectivity
- Network monitoring and status tracking

### Configuration Validation

Before applying network settings:
- Validate IP address ranges
- Check WiFi parameter compatibility
- Ensure security settings are supported
- Test connectivity after changes

## Related APIs

- [Monitoring API](monitoring.md) - Network status monitoring
- [Device API](device.md) - Device control and restart
- [Security API](security.md) - Access control and authentication