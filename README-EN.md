# Huawei E3372 HiLink API Documentation 

## Overview

Comprehensive documentation for the Huawei HiLink API, providing complete reference for controlling HiLink modems through HTTP requests.

HiLink devices usually cannot be programmed using AT commands or libraries prepared for controlling modems. They have a WebUI based on JavaScript, which controls the modem. This can be used to write appropriate scripts or libraries for controlling HiLink modems.

## Table of Contents

### Core Documentation
- [API Overview](overview.md) - General principles and authentication
- [Authentication](authentication.md) - Session management and security

### API Modules
- [Device API](modules/device.md) - Device information and control
- [SMS API](modules/sms.md) - SMS management
- [Monitoring API](modules/monitoring.md) - Status and traffic monitoring
- [Network API](modules/network.md) - Net, WLAN, DHCP configuration
- [Security API](modules/security.md) - Security, PIN management
- [System API](modules/system.md) - Global settings and language
- [Advanced API](modules/advanced.md) - OTA, Diagnosis, Voice features

### Examples and Usage
- [Common Tasks](examples/common-tasks.md) - Practical usage examples
- [Error Handling](examples/error-handling.md) - Error codes and troubleshooting

## Quick Start

### Basic Status Check
```bash
curl -X GET http://192.168.8.1/api/monitoring/status
```

### Device Restart
```bash
curl -X POST http://192.168.8.1/api/device/control \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>"
```

### Send SMS
```bash
curl -X POST http://192.168.8.1/api/sms/send-sms \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Index>-1</Index><Phones><Phone>+1234567890</Phone></Phones><Sca></Sca><Content>Test message</Content><Length>12</Length><Reserved>1</Reserved><Date>$(date '+%Y-%m-%d %H:%M:%S')</Date></request>"
```

## API Variants

The HiLink API operates in two modes:

- **Information Variant**: GET requests to retrieve data
- **Control Variant**: POST requests to modify settings or trigger actions

Most endpoints support both modes using the same URL path.

## Base URL

All API endpoints use the base URL: `http://192.168.8.1/api/`

Alternative: `http://hi.link/api/` (if configured)

## Knowledge Sources

- Device JavaScript files (primary source)
- [Programming and Installing HUAWEI HiLink E3131 under Linux](https://chaddyhv.wordpress.com/2012/08/13/programming-and-installing-huawei-hilink-e3131-under-linux/)
- [BlackyPanther/Huawei-HiLink](https://github.com/BlackyPanther/Huawei-HiLink) - PHP implementation
- [Forum 4PDA](http://4pda.ru/forum/index.php) - Community discussions