# HiLink API Common Tasks

## Overview

This document provides practical examples for common HiLink API operations, including complete workflows and error handling patterns.

## Table of Contents

- [Device Management](#device-management)
- [Connection Management](#connection-management)
- [SMS Operations](#sms-operations)
- [Monitoring and Status](#monitoring-and-status)
- [Network Configuration](#network-configuration)
- [Automation Scripts](#automation-scripts)

## Device Management

### Complete Device Information Check

```bash
#!/bin/bash
# Get comprehensive device information

echo "=== Device Information ==="
echo "Basic Info:"
curl -s http://192.168.8.1/api/device/information | \
    grep -E "<DeviceName>|<SerialNumber>|<Imei>|<SoftwareVersion>" | \
    sed 's/<[^>]*>//g' | sed 's/^[ \t]*//'

echo ""
echo "Current Status:"
curl -s http://192.168.8.1/api/monitoring/status | \
    grep -E "<ConnectionStatus>|<SignalStrength>|<CurrentNetworkType>" | \
    sed 's/<[^>]*>//g' | sed 's/^[ \t]*//'

echo ""
echo "Available Features:"
curl -s http://192.168.8.1/api/global/module-switch | \
    grep -E "sms_enabled|wifi_enabled|ota_enabled" | \
    sed 's/<[^>]*>//g' | sed 's/^[ \t]*//'
```

### Safe Device Restart

```bash
#!/bin/bash
# Restart device with confirmation and monitoring

echo "Preparing to restart device..."

# Check current status
STATUS=$(curl -s http://192.168.8.1/api/monitoring/status | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')
echo "Current connection status: $STATUS"

# Send restart command
echo "Sending restart command..."
RESPONSE=$(curl -s -X POST http://192.168.8.1/api/device/control \
    -H "Content-Type: application/xml" \
    -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>")

if [[ "$RESPONSE" == "OK" ]]; then
    echo "Restart command accepted"
    echo "Waiting for device to become available..."

    # Wait for device to come back online
    for i in {1..60}; do
        sleep 5
        if curl -s --max-time 5 http://192.168.8.1/api/monitoring/status > /dev/null 2>&1; then
            echo "Device is back online after $(($i * 5)) seconds"
            break
        fi
        echo "Waiting... (attempt $i/60)"
    done
else
    echo "Restart command failed: $RESPONSE"
    exit 1
fi
```

## Connection Management

### Intelligent Connection Control

```bash
#!/bin/bash
# Connect with status monitoring and retry logic

connect_with_retry() {
    local max_attempts=3
    local attempt=1

    while [ $attempt -le $max_attempts ]; do
        echo "Connection attempt $attempt/$max_attempts"

        # Send connect command
        RESPONSE=$(curl -s -X POST http://192.168.8.1/api/dialup/dial \
            -H "Content-Type: application/xml" \
            -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>1</Action></request>")

        if [[ "$RESPONSE" == "OK" ]]; then
            echo "Connect command sent successfully"

            # Monitor connection establishment
            for i in {1..30}; do
                sleep 2
                STATUS=$(curl -s http://192.168.8.1/api/monitoring/status | \
                    grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')

                case $STATUS in
                    "901")
                        echo "Connected successfully"
                        return 0
                        ;;
                    "900")
                        echo "Connecting..."
                        ;;
                    "902")
                        echo "Connection failed"
                        break
                        ;;
                    *)
                        echo "Unknown status: $STATUS"
                        ;;
                esac
            done
        fi

        echo "Attempt $attempt failed"
        attempt=$((attempt + 1))
        sleep 5
    done

    echo "All connection attempts failed"
    return 1
}

# Execute connection
connect_with_retry
```

### Connection Health Check

```bash
#!/bin/bash
# Comprehensive connection health assessment

check_connection_health() {
    echo "=== Connection Health Check ==="

    # Get basic status
    STATUS_RESPONSE=$(curl -s http://192.168.8.1/api/monitoring/status)

    # Extract key metrics
    CONNECTION_STATUS=$(echo "$STATUS_RESPONSE" | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')
    SIGNAL_STRENGTH=$(echo "$STATUS_RESPONSE" | grep -o '<SignalStrength>[^<]*</SignalStrength>' | sed 's/<[^>]*>//g')
    NETWORK_TYPE=$(echo "$STATUS_RESPONSE" | grep -o '<CurrentNetworkType>[^<]*</CurrentNetworkType>' | sed 's/<[^>]*>//g')
    WAN_IP=$(echo "$STATUS_RESPONSE" | grep -o '<WanIPAddress>[^<]*</WanIPAddress>' | sed 's/<[^>]*>//g')

    # Assess connection
    case $CONNECTION_STATUS in
        "901")
            echo "✓ Connection Status: Connected"
            ;;
        "900")
            echo "⚠ Connection Status: Connecting"
            ;;
        "902")
            echo "✗ Connection Status: Disconnected"
            ;;
        *)
            echo "? Connection Status: Unknown ($CONNECTION_STATUS)"
            ;;
    esac

    # Assess signal strength
    if [[ -n "$SIGNAL_STRENGTH" && "$SIGNAL_STRENGTH" != "" ]]; then
        if [[ "$SIGNAL_STRENGTH" -ge 3 ]]; then
            echo "✓ Signal Strength: Good ($SIGNAL_STRENGTH/5)"
        elif [[ "$SIGNAL_STRENGTH" -ge 1 ]]; then
            echo "⚠ Signal Strength: Weak ($SIGNAL_STRENGTH/5)"
        else
            echo "✗ Signal Strength: Very Poor ($SIGNAL_STRENGTH/5)"
        fi
    else
        echo "? Signal Strength: Unknown"
    fi

    # Network type assessment
    case $NETWORK_TYPE in
        "19"|"44"|"45")
            echo "✓ Network Type: LTE/4G ($NETWORK_TYPE)"
            ;;
        "4"|"5"|"6"|"7"|"9"|"41")
            echo "⚠ Network Type: 3G ($NETWORK_TYPE)"
            ;;
        "1"|"2"|"3")
            echo "⚠ Network Type: 2G ($NETWORK_TYPE)"
            ;;
        *)
            echo "? Network Type: Unknown ($NETWORK_TYPE)"
            ;;
    esac

    # IP address check
    if [[ -n "$WAN_IP" && "$WAN_IP" != "" ]]; then
        echo "✓ WAN IP: $WAN_IP"

        # Test connectivity
        if ping -c 1 -W 5 8.8.8.8 > /dev/null 2>&1; then
            echo "✓ Internet Connectivity: Working"
        else
            echo "✗ Internet Connectivity: Failed"
        fi
    else
        echo "✗ WAN IP: Not assigned"
    fi
}

check_connection_health
```

## SMS Operations

### SMS Management Workflow

```bash
#!/bin/bash
# Complete SMS management workflow

sms_management() {
    echo "=== SMS Management ==="

    # Check SMS counts
    echo "Checking SMS counts..."
    SMS_COUNT_RESPONSE=$(curl -s http://192.168.8.1/api/sms/sms-count)

    UNREAD=$(echo "$SMS_COUNT_RESPONSE" | grep -o '<LocalUnread>[^<]*</LocalUnread>' | sed 's/<[^>]*>//g')
    INBOX=$(echo "$SMS_COUNT_RESPONSE" | grep -o '<LocalInbox>[^<]*</LocalInbox>' | sed 's/<[^>]*>//g')

    echo "Unread messages: $UNREAD"
    echo "Total inbox: $INBOX"

    if [[ "$UNREAD" -gt 0 ]]; then
        echo "Retrieving unread messages..."

        # Get unread messages
        SMS_LIST_RESPONSE=$(curl -s -X POST http://192.168.8.1/api/sms/sms-list \
            -H "Content-Type: application/xml" \
            -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><PageIndex>1</PageIndex><ReadCount>$UNREAD</ReadCount><BoxType>1</BoxType><SortType>0</SortType><Ascending>0</Ascending><UnreadPreferred>1</UnreadPreferred></request>")

        # Parse and display messages (simplified)
        echo "Recent messages:"
        echo "$SMS_LIST_RESPONSE" | grep -o '<Phone>[^<]*</Phone>' | sed 's/<[^>]*>//g' | head -5
    fi
}

send_sms() {
    local phone="$1"
    local message="$2"
    local length=${#message}
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')

    echo "Sending SMS to $phone: $message"

    RESPONSE=$(curl -s -X POST http://192.168.8.1/api/sms/send-sms \
        -H "Content-Type: application/xml" \
        -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Index>-1</Index><Phones><Phone>$phone</Phone></Phones><Sca></Sca><Content>$message</Content><Length>$length</Length><Reserved>1</Reserved><Date>$timestamp</Date></request>")

    if [[ "$RESPONSE" == "OK" ]]; then
        echo "SMS sent successfully"
        return 0
    else
        echo "SMS sending failed: $RESPONSE"
        return 1
    fi
}

# Example usage
sms_management
# send_sms "+1234567890" "Test message from HiLink API"
```

## Monitoring and Status

### Continuous Monitoring Script

```bash
#!/bin/bash
# Continuous device monitoring with alerts

ALERT_THRESHOLD_SIGNAL=2
ALERT_THRESHOLD_RATE=1000000  # 1MB/s

monitor_device() {
    local interval=${1:-30}
    local log_file="hilink_monitor.log"

    echo "Starting HiLink device monitoring (interval: ${interval}s)"
    echo "Log file: $log_file"

    while true; do
        timestamp=$(date '+%Y-%m-%d %H:%M:%S')

        # Get status
        status_response=$(curl -s --max-time 10 http://192.168.8.1/api/monitoring/status)
        traffic_response=$(curl -s --max-time 10 http://192.168.8.1/api/monitoring/traffic-statistics)

        if [[ $? -eq 0 && -n "$status_response" ]]; then
            # Extract metrics
            connection_status=$(echo "$status_response" | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')
            signal_strength=$(echo "$status_response" | grep -o '<SignalStrength>[^<]*</SignalStrength>' | sed 's/<[^>]*>//g')
            network_type=$(echo "$status_response" | grep -o '<CurrentNetworkType>[^<]*</CurrentNetworkType>' | sed 's/<[^>]*>//g')

            download_rate=$(echo "$traffic_response" | grep -o '<CurrentDownloadRate>[^<]*</CurrentDownloadRate>' | sed 's/<[^>]*>//g')
            upload_rate=$(echo "$traffic_response" | grep -o '<CurrentUploadRate>[^<]*</CurrentUploadRate>' | sed 's/<[^>]*>//g')

            # Log metrics
            echo "$timestamp,Status:$connection_status,Signal:$signal_strength,Network:$network_type,Down:$download_rate,Up:$upload_rate" >> "$log_file"

            # Check alerts
            if [[ "$connection_status" != "901" ]]; then
                echo "ALERT: Connection not established (Status: $connection_status)"
            fi

            if [[ -n "$signal_strength" && "$signal_strength" -lt "$ALERT_THRESHOLD_SIGNAL" ]]; then
                echo "ALERT: Low signal strength ($signal_strength)"
            fi

            if [[ -n "$download_rate" && "$download_rate" -gt "$ALERT_THRESHOLD_RATE" ]]; then
                echo "INFO: High download rate ($download_rate bytes/s)"
            fi

            # Console output
            printf "\r$timestamp | Status: $connection_status | Signal: $signal_strength | Down: ${download_rate:-0} | Up: ${upload_rate:-0} bytes/s"
        else
            echo "ERROR: Unable to connect to device at $timestamp"
        fi

        sleep "$interval"
    done
}

# Start monitoring
monitor_device 30
```

## Network Configuration

### WiFi Setup Automation

```bash
#!/bin/bash
# Automated WiFi configuration

configure_wifi() {
    local ssid="$1"
    local password="$2"
    local channel="${3:-0}"  # Auto channel by default

    echo "Configuring WiFi network: $ssid"

    # Set basic WiFi settings
    basic_response=$(curl -s -X POST http://192.168.8.1/api/wlan/basic-settings \
        -H "Content-Type: application/xml" \
        -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><WifiSsid>$ssid</WifiSsid><WifiChannel>$channel</WifiChannel><WifiHide>0</WifiHide><WifiCountry>US</WifiCountry><WifiMode>b/g/n</WifiMode><WifiRate>0</WifiRate><WifiTxPwrPcnt>100</WifiTxPwrPcnt><WifiMaxassoc>10</WifiMaxassoc><wifiband>2.4GHz</wifiband><wifiautocountryswitch>1</wifiautocountryswitch><wifiantennanum>2</wifiantennanum><wifiguestofftime>0</wifiguestofftime></request>")

    if [[ "$basic_response" == "OK" ]]; then
        echo "✓ Basic WiFi settings configured"

        # Set security settings
        security_response=$(curl -s -X POST http://192.168.8.1/api/wlan/security-settings \
            -H "Content-Type: application/xml" \
            -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><WifiAuthmode>WPA2PSK</WifiAuthmode><WifiBasicencryptionmodes>AES</WifiBasicencryptionmodes><WifiWpaencryptionmodes>AES</WifiWpaencryptionmodes><WifiWpa2encryptionmodes>AES</WifiWpa2encryptionmodes><WifiKey>$password</WifiKey><WifiRestoredefaults>0</WifiRestoredefaults><WifiKeytype>0</WifiKeytype><wifiwepkeytype>1</wifiwepkeytype><WifiWepKey1></WifiWepKey1><WifiWepKey2></WifiWepKey2><WifiWepKey3></WifiWepKey3><WifiWepKey4></WifiWepKey4><WifiWepKeyIndex>0</WifiWepKeyIndex><WifiWpsStatus>0</WifiWpsStatus><WifiWpsPinCode>12345670</WifiWpsPinCode><wifiwpsversion>0</wifiwpsversion><wifiwpspinmode>0</wifiwpspinmode></request>")

        if [[ "$security_response" == "OK" ]]; then
            echo "✓ WiFi security configured"
            echo "WiFi network '$ssid' configured successfully"
            return 0
        else
            echo "✗ Failed to configure WiFi security: $security_response"
            return 1
        fi
    else
        echo "✗ Failed to configure basic WiFi settings: $basic_response"
        return 1
    fi
}

# Example usage
# configure_wifi "MyHomeNetwork" "SecurePassword123" "6"
```

## Automation Scripts

### Health Check and Auto-Recovery

```bash
#!/bin/bash
# Automated health check with recovery actions

AUTO_RECOVERY=true
MAX_RECOVERY_ATTEMPTS=3
RECOVERY_ATTEMPT_COUNT=0

perform_health_check() {
    echo "=== Health Check $(date) ==="

    # Test API connectivity
    if ! curl -s --max-time 10 http://192.168.8.1/api/monitoring/status > /dev/null; then
        echo "✗ API not responding"
        return 1
    fi

    # Check connection status
    status_response=$(curl -s http://192.168.8.1/api/monitoring/status)
    connection_status=$(echo "$status_response" | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')

    if [[ "$connection_status" != "901" ]]; then
        echo "✗ Not connected (Status: $connection_status)"
        return 2
    fi

    # Test internet connectivity
    if ! ping -c 1 -W 5 8.8.8.8 > /dev/null 2>&1; then
        echo "✗ No internet connectivity"
        return 3
    fi

    echo "✓ All checks passed"
    RECOVERY_ATTEMPT_COUNT=0  # Reset counter on success
    return 0
}

attempt_recovery() {
    local issue="$1"

    if [[ "$AUTO_RECOVERY" != "true" ]]; then
        echo "Auto-recovery disabled"
        return 1
    fi

    if [[ $RECOVERY_ATTEMPT_COUNT -ge $MAX_RECOVERY_ATTEMPTS ]]; then
        echo "Max recovery attempts reached ($MAX_RECOVERY_ATTEMPTS)"
        return 1
    fi

    RECOVERY_ATTEMPT_COUNT=$((RECOVERY_ATTEMPT_COUNT + 1))
    echo "Recovery attempt $RECOVERY_ATTEMPT_COUNT/$MAX_RECOVERY_ATTEMPTS for issue: $issue"

    case $issue in
        2)  # Connection issue
            echo "Attempting to reconnect..."
            curl -s -X POST http://192.168.8.1/api/dialup/dial \
                -H "Content-Type: application/xml" \
                -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>0</Action></request>" > /dev/null
            sleep 5
            curl -s -X POST http://192.168.8.1/api/dialup/dial \
                -H "Content-Type: application/xml" \
                -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>1</Action></request>" > /dev/null
            ;;
        1)  # API issue
            echo "Device may need restart"
            # Could implement restart logic here
            ;;
        *)
            echo "Unknown issue, trying generic recovery"
            ;;
    esac

    sleep 30  # Wait before next check
    return 0
}

# Main monitoring loop
while true; do
    perform_health_check
    health_status=$?

    if [[ $health_status -ne 0 ]]; then
        attempt_recovery $health_status
    fi

    sleep 60  # Check every minute
done
```

## Integration Examples

### ModemOpus-Style Integration

```bash
#!/bin/bash
# Example integration similar to ModemOpus patterns

class HiLinkClient {
    base_url="http://192.168.8.1/api"
    timeout=30

    api_call() {
        local method="$1"
        local endpoint="$2"
        local data="$3"

        local url="${base_url}${endpoint}"

        if [[ "$method" == "GET" ]]; then
            curl -s --max-time "$timeout" "$url"
        else
            curl -s --max-time "$timeout" -X "$method" "$url" \
                -H "Content-Type: application/xml" \
                -d "$data"
        fi
    }

    get_device_info() {
        api_call "GET" "/device/information"
    }

    get_status() {
        api_call "GET" "/monitoring/status"
    }

    restart_device() {
        api_call "POST" "/device/control" \
            "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>"
    }

    connect() {
        api_call "POST" "/dialup/dial" \
            "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>1</Action></request>"
    }

    disconnect() {
        api_call "POST" "/dialup/dial" \
            "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>0</Action></request>"
    }
}

# Usage examples
# HiLinkClient.get_status
# HiLinkClient.restart_device
```

This completes the common tasks documentation with practical, real-world examples that can be directly used or adapted for specific needs.