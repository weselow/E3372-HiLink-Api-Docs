# Huawei HiLink API Documentation

HiLink devices usually cannot be programmed using AT commands or libraries prepared for controlling modems. They have a WebUI based on JavaScript, which controls the modem. This can be used to write appropriate scripts or libraries for controlling HiLink modems.

## Table of Contents

1. [General Instructions](#general-instructions)
2. [API Pages - Requests and Responses](#api-pages---requests-and-responses)
   - [Cradle Module](#cradle-module)
   - [DDNS Module](#ddns-module)
   - [Device Module](#device-module)
   - [DHCP Module](#dhcp-module)
   - [Diagnosis Module](#diagnosis-module)
   - [Dialup Module](#dialup-module)
   - [Filemanager Module](#filemanager-module)
   - [Global Module](#global-module)
   - [Host Module](#host-module)
   - [Language Module](#language-module)
   - [Monitoring Module](#monitoring-module)
   - [Net Module](#net-module)
   - [Online-update Module](#online-update-module)
   - [OTA Module](#ota-module)
   - [PB Module](#pb-module)
   - [PIN Module](#pin-module)
   - [Redirection Module](#redirection-module)
   - [SDCard Module](#sdcard-module)
   - [Security Module](#security-module)
   - [SMS Module](#sms-module)
   - [SNTP Module](#sntp-module)
   - [STK Module](#stk-module)
   - [User Module](#user-module)
   - [USSD Module](#ussd-module)
   - [Voice Module](#voice-module)
   - [Webserver Module](#webserver-module)
   - [WLAN Module](#wlan-module)
   - [Notes](#notes)
3. [Using the API](#using-the-api)
4. [Error Numbers](#error-numbers)
5. [Other](#other)
   - [WebUI File Modification](#webui-file-modification)
   - [Automatic PIN Entry](#automatic-pin-entry)
   - [HiLink Configuration Pages List](#hilink-configuration-pages-list)
   - [JavaScript Files List](#javascript-files-list)
6. [Knowledge Sources](#knowledge-sources)

## General Instructions

The HiLink API can be divided into two variants:

- **Information variant:**
  This variant usually requires fetching (**GET**) the appropriate page from the modem (http://hi.link or http://192.168.8.1). This means obtaining a **response** immediately.

- **Control/configuration variant:**
  Requires sending (**POST**) a request (**request**) along with the address of the appropriate page to the modem. In response to a positively executed command, we usually get **OK**.

The information variant and the control variant usually have the same addresses.

Some software versions may require providing the current token for session verification (I haven't found any information about when the token changes; the token can be disabled by editing configuration files).

As far as I could determine, the HiLink software is unified. This means that, for example, a HiLink modem contains the same WebUI files as a HiLink router and vice versa. Of course, this doesn't mean we'll enable WiFi on a modem, for example. Some API pages are blocked by hardware or configuration files.

The following list of API addresses is based on JavaScript files contained in the modified WebUI for the E3372s-153 modem. From the same files, requests and responses are determined. The responses given below are supplemented in several cases based on the response from the E3372s-153 modem.

The list has been sorted alphabetically by the name of the module to which the given API refers.

## API Pages - Requests and Responses

### Cradle Module

**Getting basic cradle information**
- URL: `http://192.168.8.1/api/cradle/basic-info`
- Method: GET
- Request: none
- Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <poecat>3</poecat>
    <multipmcat>0</multipmcat>
    <eth_wan_mode>0</eth_wan_mode>
    <eth_wan_status>1</eth_wan_status>
    <cradle_status>0</cradle_status>
</response>
```

**Setting cradle**
- URL: `http://192.168.8.1/api/cradle/basic-info`
- Method: POST
- Request:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <poecat>3</poecat>
    <multipmcat>0</multipmcat>
    <eth_wan_mode>0</eth_wan_mode>
</request>
```
- Response: `OK`

### DDNS Module

**Getting DDNS settings**
- URL: `http://192.168.8.1/api/ddns/ddns-list`
- Method: GET
- Request: none
- Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <ddnslist>
        <index>0</index>
        <server>www.3322.org</server>
        <username></username>
        <password></password>
        <domain></domain>
        <enable>0</enable>
    </ddnslist>
</response>
```

**Setting DDNS**
- URL: `http://192.168.8.1/api/ddns/ddns-list`
- Method: POST
- Request:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <index>0</index>
    <server>www.3322.org</server>
    <username>username</username>
    <password>password</password>
    <domain>domain.3322.org</domain>
    <enable>1</enable>
</request>
```
- Response: `OK`

### Device Module

**Check for new version availability**
- URL: `http://192.168.8.1/api/device/autorun-version`
- Method: GET
- Request: none
- Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <Version>11.0.2.1(H192SP2C1025)</Version>
</response>
```

**Get basic device information**
- URL: `http://192.168.8.1/api/device/information`
- Method: GET
- Request: none
- Response:
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

**Device control (restart/shutdown)**
- URL: `http://192.168.8.1/api/device/control`
- Method: POST
- Request:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Control>1</Control>
</request>
```
- Response: `OK`
- Notes: Control values: 1=restart, 4=shutdown

### DHCP Module

**Get DHCP settings**
- URL: `http://192.168.8.1/api/dhcp/settings`
- Method: GET
- Request: none
- Response:
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

**Set DHCP settings**
- URL: `http://192.168.8.1/api/dhcp/settings`
- Method: POST
- Request:
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
- Response: `OK`

### Diagnosis Module

**PING command**
- URL: `http://192.168.8.1/api/diagnosis/ping`
- Method: POST
- Request:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <host>8.8.8.8</host>
    <timeout>4</timeout>
    <count>4</count>
</request>
```
- Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <result>success</result>
</response>
```

### Dialup Module

**Get connection status**
- URL: `http://192.168.8.1/api/dialup/connection`
- Method: GET
- Request: none
- Response:
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

**Connect/Disconnect**
- URL: `http://192.168.8.1/api/dialup/dial`
- Method: POST
- Request:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Action>1</Action>
</request>
```
- Response: `OK`
- Notes: Action values: 1=connect, 0=disconnect

### Filemanager Module

**Upload file to device**
- URL: `http://192.168.8.1/api/filemanager/upload`
- Method: POST
- Request: ?
- Response: ?
- Notes: The most mysterious API. Apart from the address, nothing is known. It probably works similarly to the equally mysterious api/sdcard/fileupload API, except it probably uploads files to the main device directory.

### Global Module

**Get module state information**
- URL: `http://192.168.8.1/api/global/module-switch`
- Method: GET
- Request: none
- Response:
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

### Monitoring Module

**Get status**
- URL: `http://192.168.8.1/api/monitoring/status`
- Method: GET
- Request: none
- Response:
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

**Get traffic statistics**
- URL: `http://192.168.8.1/api/monitoring/traffic-statistics`
- Method: GET
- Request: none
- Response:
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

### SMS Module

**Get SMS count**
- URL: `http://192.168.8.1/api/sms/sms-count`
- Method: GET
- Request: none
- Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <LocalUnread>1</LocalUnread>
    <LocalInbox>5</LocalInbox>
    <LocalOutbox>3</LocalOutbox>
    <LocalDraft>0</LocalDraft>
    <SimUnread>0</SimUnread>
    <SimInbox>0</SimInbox>
    <SimOutbox>0</SimOutbox>
    <SimDraft>0</SimDraft>
    <NewMsg>1</NewMsg>
</response>
```

**Get SMS list**
- URL: `http://192.168.8.1/api/sms/sms-list`
- Method: POST
- Request:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <PageIndex>1</PageIndex>
    <ReadCount>20</ReadCount>
    <BoxType>1</BoxType>
    <SortType>0</SortType>
    <Ascending>0</Ascending>
    <UnreadPreferred>0</UnreadPreferred>
</request>
```
- Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <Count>5</Count>
    <Messages>
        <Message>
            <Smstat>3</Smstat>
            <Index>40000</Index>
            <Phone>+48500500500</Phone>
            <Content>Test message content</Content>
            <Date>2023-01-01 12:00:00</Date>
            <Sca></Sca>
            <SaveType>3</SaveType>
            <Priority>0</Priority>
            <SmsType>1</SmsType>
        </Message>
    </Messages>
</response>
```

**Send SMS**
- URL: `http://192.168.8.1/api/sms/send-sms`
- Method: POST
- Request:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Index>-1</Index>
    <Phones>
        <Phone>+48500500500</Phone>
    </Phones>
    <Sca></Sca>
    <Content>Test message content</Content>
    <Length>20</Length>
    <Reserved>1</Reserved>
    <Date>2023-01-01 12:00:00</Date>
</request>
```
- Response: `OK`

### WLAN Module

**Get basic WLAN settings**
- URL: `http://192.168.8.1/api/wlan/basic-settings`
- Method: GET
- Request: none
- Response:
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

**Set basic WLAN settings**
- URL: `http://192.168.8.1/api/wlan/basic-settings`
- Method: POST
- Request:
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
- Response: `OK`

**Get security settings**
- URL: `http://192.168.8.1/api/wlan/security-settings`
- Method: GET
- Request: none
- Response:
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

## Using the API

The HiLink API can be easily used in Linux. Both as a command entered on the command line and as a script. In Windows, it starts to get a bit more difficult. You can simplify the task by downloading curl binaries (see the [cURL project page](http://curl.haxx.se/download.html)) and a package of Linux command line tools for Windows (e.g., [UnxUtils](http://unxutils.sourceforge.net/)) from which it's worth getting grep and cut.

Below are a few examples tested on OpenWRT Barrier Breaker (r43618) using the curl command (I think it can be adapted to the wget command without problems), as well as examples of batch files for Windows.

### Example 1a - Modem restart - OpenWRT command line
```bash
curl -X POST http://192.168.8.1/api/device/control \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>"
```

### Example 1b - Modem restart - Windows batch file
```batch
@echo off
curl -X POST http://192.168.8.1/api/device/control ^
     -H "Content-Type: application/xml" ^
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>"
```

### Example 2 - Get connection status
```bash
curl -X GET http://192.168.8.1/api/monitoring/status
```

### Example 3 - Send SMS
```bash
curl -X POST http://192.168.8.1/api/sms/send-sms \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Index>-1</Index><Phones><Phone>+48500500500</Phone></Phones><Sca></Sca><Content>Test message</Content><Length>12</Length><Reserved>1</Reserved><Date>$(date '+%Y-%m-%d %H:%M:%S')</Date></request>"
```

## Error Numbers

Common error codes returned by the API:

- **100001**: No support
- **100002**: Not available
- **100003**: Not supported
- **100004**: System busy
- **100005**: Unknown error
- **100006**: Invalid parameter
- **100007**: Write error
- **100008**: Wrong password
- **100009**: Not logged in
- **103002**: Unknown error
- **103015**: Unknown error
- **108001**: Invalid username
- **108002**: Invalid password
- **108003**: User already logged in
- **108006**: Invalid username or password
- **108007**: Too many users logged in
- **109001**: Back up battery low power
- **109002**: Back up battery power off
- **109003**: RTC not available
- **109004**: Edition error
- **109005**: Wrong pins
- **109006**: Month limit reached
- **109007**: Busy
- **109012**: Can't delete default profile
- **110002**: Modem not connected to PC
- **111019**: Unknown error
- **111020**: Device at sleep mode and can be wake up by SMS or phone
- **111021**: Unknown error
- **111022**: Device is in low power mode
- **112001**: Wrong PIN
- **112002**: PUK required
- **112003**: PUK blocked
- **112004**: PUK authentication failed
- **112005**: PUK blocked permanently
- **112006**: Card locked
- **113018**: Not enough space
- **114001**: Invalid number
- **114002**: SMS sending failed
- **114003**: Invalid phone number
- **114004**: Empty SMS content
- **114005**: SMS database full
- **114006**: SMS already sent
- **115001**: Unknown SMS error
- **117001**: Unknown error
- **117004**: Wrong WiFi password
- **125001**: Invalid token

## Other

### WebUI File Modification

**WARNING**: The procedure described below requires a device with unlocked access via ADB and/or telnet.
**WARNING**: The procedure described below probably works only on the E3372 modem with unlocked ADB (I can't verify on other devices).

First, from the command line, we connect to the modem using ADB and run the shell:

```bash
adb shell
```

Then we navigate to the directory containing the WebUI files:

```bash
cd /app/hilink/config
```

Edit the configuration file (for example, to unlock Polish language):

```bash
vi system.xml
```

Find the section with language settings and modify accordingly.

### Automatic PIN Entry

To automatically enter the PIN at modem startup, you can modify the autorun.sh script:

```bash
adb shell
cd /system/bin
vi autorun.sh
```

Add PIN entry commands to the script.

### HiLink Configuration Pages List

The following configuration pages are available in HiLink devices:

- http://192.168.8.1/index.html
- http://192.168.8.1/home.html
- http://192.168.8.1/statistic.html
- http://192.168.8.1/sms.html
- http://192.168.8.1/phonebook.html
- http://192.168.8.1/ussd.html
- http://192.168.8.1/settings.html
- http://192.168.8.1/security.html
- http://192.168.8.1/help.html

### JavaScript Files List

Key JavaScript files used by the WebUI:

- /js/common.js
- /js/menu.js
- /js/status.js
- /js/statistics.js
- /js/sms.js
- /js/phonebook.js
- /js/ussd.js
- /js/dialup.js
- /js/wlan.js
- /js/security.js

## Knowledge Sources

- The main source of knowledge is the device itself, specifically the JavaScripts contained on the given configuration pages of the modem
- [Programming and Installing HUAWEI HiLink E3131 under Linux](https://chaddyhv.wordpress.com/2012/08/13/programming-and-installing-huawei-hilink-e3131-under-linux/) - page from which I gathered quite a lot of information and created this guide in a similar way
- [BlackyPanther/Huawei-HiLink](https://github.com/BlackyPanther/Huawei-HiLink) - PHP class using HiLink API benefits
- [Forum 4PDA](http://4pda.ru/forum/index.php) - specifically topics dedicated to discussing a given device and its software