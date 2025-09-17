# HiLink API Error Handling

## Overview

Comprehensive guide to error codes, troubleshooting, and error handling strategies for HiLink API implementations.

## Table of Contents

- [Error Code Categories](#error-code-categories)
- [Complete Error Code Reference](#complete-error-code-reference)
- [Error Handling Strategies](#error-handling-strategies)
- [Troubleshooting Guide](#troubleshooting-guide)
- [Implementation Patterns](#implementation-patterns)

## Error Code Categories

### System Errors (100000-100999)

General system and API errors.

| Code | Description | Severity | Recovery |
|------|-------------|----------|----------|
| **100001** | No support | High | Feature unavailable |
| **100002** | Not available | Medium | Retry later |
| **100003** | Not supported | High | Use alternative |
| **100004** | System busy | Medium | Retry with delay |
| **100005** | Unknown error | High | Check logs |
| **100006** | Invalid parameter | Medium | Validate input |
| **100007** | Write error | High | Check permissions |
| **100008** | Wrong password | Medium | Verify credentials |
| **100009** | Not logged in | Medium | Authenticate first |

### Unknown System Errors (103000-103999)

Unspecified system errors.

| Code | Description | Action |
|------|-------------|--------|
| **103002** | Unknown error | Generic error handling |
| **103015** | Unknown error | Check device status |

### Authentication Errors (108000-108999)

User authentication and session management errors.

| Code | Description | Action |
|------|-------------|--------|
| **108001** | Invalid username | Check username format |
| **108002** | Invalid password | Verify password |
| **108003** | User already logged in | Use existing session |
| **108006** | Invalid username or password | Verify both credentials |
| **108007** | Too many users logged in | Wait for session expiry |

### Device Errors (109000-109999)

Device-specific operational errors.

| Code | Description | Cause | Action |
|------|-------------|-------|--------|
| **109001** | Back up battery low power | Low battery | Charge device |
| **109002** | Back up battery power off | Battery depleted | Connect power |
| **109003** | RTC not available | Clock issue | Check time settings |
| **109004** | Edition error | Firmware issue | Update firmware |
| **109005** | Wrong pins | Incorrect PIN | Verify PIN |
| **109006** | Month limit reached | Data limit | Check plan |
| **109007** | Busy | Device busy | Retry later |
| **109012** | Can't delete default profile | Protected profile | Use different profile |

### Connection Errors (110000-110999)

Device connectivity and communication errors.

| Code | Description | Action |
|------|-------------|--------|
| **110002** | Modem not connected to PC | Check USB connection |

### Sleep Mode Errors (111000-111999)

Power management and sleep mode errors.

| Code | Description | Recovery |
|------|-------------|----------|
| **111019** | Unknown error | Check device status |
| **111020** | Device at sleep mode and can be wake up by SMS or phone | Send wake signal |
| **111021** | Unknown error | Power cycle device |
| **111022** | Device is in low power mode | Check power settings |

### PIN/PUK Errors (112000-112999)

SIM card PIN and PUK related errors.

| Code | Description | Attempts Left | Action |
|------|-------------|---------------|--------|
| **112001** | Wrong PIN | 2 | Enter correct PIN |
| **112002** | PUK required | 0 | Enter PUK |
| **112003** | PUK blocked | 9 | Enter PUK |
| **112004** | PUK authentication failed | Decremented | Try again |
| **112005** | PUK blocked permanently | 0 | Contact carrier |
| **112006** | Card locked | N/A | Replace SIM |

### Storage Errors (113000-113999)

Storage and memory related errors.

| Code | Description | Action |
|------|-------------|--------|
| **113018** | Not enough space | Free up storage |

### SMS Errors (114000-114999)

SMS functionality specific errors.

| Code | Description | Recovery |
|------|-------------|----------|
| **114001** | Invalid number | Check phone number format |
| **114002** | SMS sending failed | Retry sending |
| **114003** | Invalid phone number | Validate number |
| **114004** | Empty SMS content | Add message content |
| **114005** | SMS database full | Delete old messages |
| **114006** | SMS already sent | Check sent messages |

### SMS System Errors (115000-115999)

SMS system level errors.

| Code | Description | Action |
|------|-------------|--------|
| **115001** | Unknown SMS error | Check SMS settings |

### Network Errors (117000-117999)

Network and WiFi related errors.

| Code | Description | Action |
|------|-------------|--------|
| **117001** | Unknown error | Check network settings |
| **117004** | Wrong WiFi password | Verify WiFi password |

### Token Errors (125000-125999)

Session token and authentication token errors.

| Code | Description | Action |
|------|-------------|--------|
| **125001** | Invalid token | Re-authenticate |

## Error Handling Strategies

### Retry Logic Implementation

```bash
api_call_with_retry() {
    local method="$1"
    local endpoint="$2"
    local data="$3"
    local max_attempts="${4:-3}"
    local base_delay="${5:-2}"

    local attempt=1
    local delay="$base_delay"

    while [ $attempt -le $max_attempts ]; do
        echo "Attempt $attempt/$max_attempts: $method $endpoint"

        if [[ "$method" == "GET" ]]; then
            response=$(curl -s --max-time 30 "http://192.168.8.1/api$endpoint")
        else
            response=$(curl -s --max-time 30 -X "$method" "http://192.168.8.1/api$endpoint" \
                -H "Content-Type: application/xml" -d "$data")
        fi

        # Check for successful response
        if [[ $? -eq 0 && -n "$response" ]]; then
            # Check for error codes in response
            if echo "$response" | grep -q "<error>"; then
                error_code=$(echo "$response" | grep -o '<code>[^<]*</code>' | sed 's/<[^>]*>//g')
                handle_api_error "$error_code" "$attempt" "$max_attempts"
                error_handling_result=$?

                case $error_handling_result in
                    0)  # Retry
                        ;;
                    1)  # Abort
                        echo "Aborting due to unrecoverable error: $error_code"
                        return 1
                        ;;
                    2)  # Immediate retry
                        delay=0
                        ;;
                esac
            else
                echo "Success: $response"
                return 0
            fi
        else
            echo "Network error or no response"
        fi

        if [ $attempt -lt $max_attempts ]; then
            echo "Waiting ${delay}s before retry..."
            sleep "$delay"
            delay=$((delay * 2))  # Exponential backoff
        fi

        attempt=$((attempt + 1))
    done

    echo "All attempts failed"
    return 1
}

handle_api_error() {
    local error_code="$1"
    local attempt="$2"
    local max_attempts="$3"

    echo "API Error: $error_code"

    case "$error_code" in
        # Retryable errors
        100002|100004|100007|109007)
            echo "Temporary error, will retry"
            return 0  # Retry
            ;;

        # Authentication errors - may need re-authentication
        100008|100009|108001|108002|108006|125001)
            echo "Authentication error"
            # Could implement re-authentication here
            return 1  # Abort for now
            ;;

        # Immediate retry errors
        103002|103015|111019|111021)
            echo "Unknown error, immediate retry"
            return 2  # Immediate retry
            ;;

        # Unrecoverable errors
        100001|100003|109001|109002|109012|112005|112006)
            echo "Unrecoverable error"
            return 1  # Abort
            ;;

        # PIN/PUK errors - need user intervention
        112001|112002|112003|112004)
            echo "PIN/PUK error - user intervention required"
            return 1  # Abort
            ;;

        # Resource errors
        113018|114005)
            echo "Resource limit reached"
            return 1  # Abort
            ;;

        *)
            echo "Unknown error code, will retry if attempts remain"
            return 0  # Retry
            ;;
    esac
}
```

### Error Context Enrichment

```bash
enrich_error_context() {
    local error_code="$1"
    local operation="$2"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')

    # Get device status for context
    device_status=$(curl -s --max-time 10 http://192.168.8.1/api/monitoring/status 2>/dev/null)

    # Extract relevant status information
    if [[ -n "$device_status" ]]; then
        connection_status=$(echo "$device_status" | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')
        signal_strength=$(echo "$device_status" | grep -o '<SignalStrength>[^<]*</SignalStrength>' | sed 's/<[^>]*>//g')
        sim_status=$(echo "$device_status" | grep -o '<SimStatus>[^<]*</SimStatus>' | sed 's/<[^>]*>//g')
    fi

    # Create enriched error report
    cat << EOF
ERROR REPORT
============
Timestamp: $timestamp
Operation: $operation
Error Code: $error_code
Error Description: $(get_error_description "$error_code")

Device Context:
- Connection Status: ${connection_status:-Unknown}
- Signal Strength: ${signal_strength:-Unknown}
- SIM Status: ${sim_status:-Unknown}

Recommended Action: $(get_error_action "$error_code")
EOF
}

get_error_description() {
    local code="$1"
    case "$code" in
        100001) echo "Feature not supported by device" ;;
        100002) echo "Feature temporarily unavailable" ;;
        100004) echo "Device is busy processing another request" ;;
        100006) echo "Invalid parameters provided" ;;
        100008) echo "Authentication failed - wrong password" ;;
        108003) echo "User already logged in" ;;
        112001) echo "Incorrect PIN entered" ;;
        112002) echo "PUK code required to unlock SIM" ;;
        114002) echo "SMS sending failed" ;;
        114005) echo "SMS storage is full" ;;
        117004) echo "Incorrect WiFi password" ;;
        125001) echo "Session token is invalid or expired" ;;
        *) echo "Unknown error occurred" ;;
    esac
}

get_error_action() {
    local code="$1"
    case "$code" in
        100001) echo "Use alternative API or feature" ;;
        100002) echo "Wait and retry later" ;;
        100004) echo "Wait for current operation to complete" ;;
        100006) echo "Validate and correct request parameters" ;;
        100008) echo "Verify password and re-authenticate" ;;
        108003) echo "Use existing session or logout first" ;;
        112001) echo "Enter correct PIN (2 attempts remaining)" ;;
        112002) echo "Enter PUK code to unlock SIM" ;;
        114002) echo "Check network connection and retry" ;;
        114005) echo "Delete old SMS messages to free space" ;;
        117004) echo "Verify WiFi password and retry" ;;
        125001) echo "Re-authenticate to get new session token" ;;
        *) echo "Check device status and network connectivity" ;;
    esac
}
```

## Troubleshooting Guide

### Connection Issues

```bash
diagnose_connection_issues() {
    echo "=== Connection Diagnostics ==="

    # Test API connectivity
    echo "Testing API connectivity..."
    if curl -s --max-time 5 http://192.168.8.1/api/monitoring/status > /dev/null; then
        echo "✓ API is reachable"
    else
        echo "✗ API is not reachable"
        echo "  - Check device IP address (default: 192.168.8.1)"
        echo "  - Verify network connection to device"
        echo "  - Check if device is powered on"
        return 1
    fi

    # Check device status
    echo "Checking device status..."
    status_response=$(curl -s http://192.168.8.1/api/monitoring/status)

    if [[ -n "$status_response" ]]; then
        connection_status=$(echo "$status_response" | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')
        sim_status=$(echo "$status_response" | grep -o '<SimStatus>[^<]*</SimStatus>' | sed 's/<[^>]*>//g')

        case "$connection_status" in
            901) echo "✓ Device is connected" ;;
            900) echo "⚠ Device is connecting" ;;
            902) echo "✗ Device is disconnected" ;;
            903) echo "⚠ Device is disconnecting" ;;
            *) echo "? Unknown connection status: $connection_status" ;;
        esac

        case "$sim_status" in
            1) echo "✓ SIM card is valid" ;;
            0) echo "✗ No SIM card detected" ;;
            255) echo "✗ Invalid SIM card" ;;
            *) echo "? Unknown SIM status: $sim_status" ;;
        esac
    else
        echo "✗ Could not retrieve device status"
        return 1
    fi

    # Test internet connectivity
    echo "Testing internet connectivity..."
    if ping -c 1 -W 5 8.8.8.8 > /dev/null 2>&1; then
        echo "✓ Internet connectivity working"
    else
        echo "✗ No internet connectivity"
        echo "  - Check mobile data connection"
        echo "  - Verify APN settings"
        echo "  - Check carrier network status"
    fi
}
```

### SMS Issues

```bash
diagnose_sms_issues() {
    echo "=== SMS Diagnostics ==="

    # Check SMS module availability
    module_response=$(curl -s http://192.168.8.1/api/global/module-switch)
    sms_enabled=$(echo "$module_response" | grep -o '<sms_enabled>[^<]*</sms_enabled>' | sed 's/<[^>]*>//g')

    if [[ "$sms_enabled" == "1" ]]; then
        echo "✓ SMS module is enabled"
    else
        echo "✗ SMS module is disabled"
        return 1
    fi

    # Check SMS counts
    sms_count_response=$(curl -s http://192.168.8.1/api/sms/sms-count)
    if [[ -n "$sms_count_response" ]]; then
        local_inbox=$(echo "$sms_count_response" | grep -o '<LocalInbox>[^<]*</LocalInbox>' | sed 's/<[^>]*>//g')
        sim_inbox=$(echo "$sms_count_response" | grep -o '<SimInbox>[^<]*</SimInbox>' | sed 's/<[^>]*>//g')

        echo "SMS Storage Status:"
        echo "  Local storage: $local_inbox messages"
        echo "  SIM storage: $sim_inbox messages"

        # Check if storage is full
        if [[ "$local_inbox" -gt 100 ]]; then
            echo "⚠ Local SMS storage may be getting full"
        fi
    else
        echo "✗ Could not retrieve SMS counts"
    fi

    # Test SMS sending capability
    echo "SMS functionality appears to be working"
    echo "To test SMS sending:"
    echo "  1. Ensure SIM card has SMS capability"
    echo "  2. Check mobile network registration"
    echo "  3. Verify phone number format (+countrycode)"
}
```

### Device Recovery

```bash
attempt_device_recovery() {
    local issue_type="$1"

    echo "=== Device Recovery ==="
    echo "Issue type: $issue_type"

    case "$issue_type" in
        "api_unresponsive")
            echo "Attempting device restart..."
            restart_response=$(curl -s -X POST http://192.168.8.1/api/device/control \
                -H "Content-Type: application/xml" \
                -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>")

            if [[ "$restart_response" == "OK" ]]; then
                echo "Restart command sent, waiting for device..."
                wait_for_device_online
            else
                echo "Could not send restart command"
                echo "Manual power cycle may be required"
            fi
            ;;

        "connection_failed")
            echo "Attempting connection reset..."
            # Disconnect first
            curl -s -X POST http://192.168.8.1/api/dialup/dial \
                -H "Content-Type: application/xml" \
                -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>0</Action></request>" > /dev/null

            sleep 5

            # Reconnect
            connect_response=$(curl -s -X POST http://192.168.8.1/api/dialup/dial \
                -H "Content-Type: application/xml" \
                -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>1</Action></request>")

            if [[ "$connect_response" == "OK" ]]; then
                echo "Connection reset initiated"
                monitor_connection_establishment
            else
                echo "Could not reset connection"
            fi
            ;;

        *)
            echo "Unknown issue type, attempting general recovery"
            ;;
    esac
}

wait_for_device_online() {
    local max_wait=120
    local wait_time=0

    echo "Waiting for device to come online..."

    while [ $wait_time -lt $max_wait ]; do
        sleep 5
        wait_time=$((wait_time + 5))

        if curl -s --max-time 5 http://192.168.8.1/api/monitoring/status > /dev/null 2>&1; then
            echo "Device is online after ${wait_time}s"
            return 0
        fi

        printf "."
    done

    echo ""
    echo "Device did not come online within ${max_wait}s"
    return 1
}
```

## Implementation Patterns

### Robust API Client

```bash
#!/bin/bash
# Robust HiLink API client with comprehensive error handling

class RobustHiLinkClient {
    base_url="http://192.168.8.1/api"
    max_retries=3
    retry_delay=2
    timeout=30
    debug=false

    api_request() {
        local method="$1"
        local endpoint="$2"
        local data="$3"
        local custom_retries="${4:-$max_retries}"

        local attempt=1
        local delay="$retry_delay"

        while [ $attempt -le $custom_retries ]; do
            [[ "$debug" == "true" ]] && echo "DEBUG: $method $endpoint (attempt $attempt/$custom_retries)"

            local response
            local http_code

            if [[ "$method" == "GET" ]]; then
                response=$(curl -s -w "%{http_code}" --max-time "$timeout" "${base_url}${endpoint}")
            else
                response=$(curl -s -w "%{http_code}" --max-time "$timeout" -X "$method" "${base_url}${endpoint}" \
                    -H "Content-Type: application/xml" -d "$data")
            fi

            # Extract HTTP code and response body
            http_code="${response: -3}"
            response="${response%???}"

            # Handle HTTP errors
            if [[ "$http_code" != "200" ]]; then
                echo "HTTP Error: $http_code"
                if [ $attempt -lt $custom_retries ]; then
                    sleep "$delay"
                    delay=$((delay * 2))
                    attempt=$((attempt + 1))
                    continue
                else
                    return 1
                fi
            fi

            # Check for API errors
            if echo "$response" | grep -q "<error>"; then
                local error_code=$(echo "$response" | grep -o '<code>[^<]*</code>' | sed 's/<[^>]*>//g')
                local should_retry=$(should_retry_error "$error_code")

                if [[ "$should_retry" == "true" && $attempt -lt $custom_retries ]]; then
                    echo "Retryable error $error_code, attempt $attempt/$custom_retries"
                    sleep "$delay"
                    delay=$((delay * 2))
                    attempt=$((attempt + 1))
                    continue
                else
                    echo "API Error: $error_code"
                    enrich_error_context "$error_code" "$method $endpoint"
                    return 1
                fi
            fi

            # Success
            echo "$response"
            return 0
        done

        echo "All retry attempts exhausted"
        return 1
    }

    should_retry_error() {
        local error_code="$1"
        case "$error_code" in
            100002|100004|100007|109007|103002|103015|111019|111021)
                echo "true"
                ;;
            *)
                echo "false"
                ;;
        esac
    }

    # High-level methods with error handling
    get_status() {
        api_request "GET" "/monitoring/status"
    }

    restart_device() {
        local response=$(api_request "POST" "/device/control" \
            "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>")

        if [[ "$response" == "OK" ]]; then
            echo "Restart initiated, waiting for device..."
            wait_for_device_online
        else
            echo "Restart failed: $response"
            return 1
        fi
    }

    send_sms() {
        local phone="$1"
        local message="$2"
        local length=${#message}
        local timestamp=$(date '+%Y-%m-%d %H:%M:%S')

        # Validate inputs
        if [[ -z "$phone" || -z "$message" ]]; then
            echo "Error: Phone number and message are required"
            return 1
        fi

        if [[ ${#message} -gt 160 ]]; then
            echo "Warning: Message exceeds 160 characters"
        fi

        local response=$(api_request "POST" "/sms/send-sms" \
            "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Index>-1</Index><Phones><Phone>$phone</Phone></Phones><Sca></Sca><Content>$message</Content><Length>$length</Length><Reserved>1</Reserved><Date>$timestamp</Date></request>")

        if [[ "$response" == "OK" ]]; then
            echo "SMS sent successfully to $phone"
            return 0
        else
            echo "SMS sending failed: $response"
            return 1
        fi
    }
}

# Usage examples:
# RobustHiLinkClient.get_status
# RobustHiLinkClient.send_sms "+1234567890" "Test message"
# RobustHiLinkClient.restart_device
```

This comprehensive error handling guide provides the foundation for building robust HiLink API integrations that can handle the various error conditions and edge cases that may occur in production environments.