# SMS API Module

## Overview

The SMS API module provides comprehensive SMS message management functionality including reading, sending, and organizing messages. It supports both local device storage and SIM card storage.

## Table of Contents

- [SMS Count](#sms-count)
- [SMS List](#sms-list)
- [Send SMS](#send-sms)
- [Message Types](#message-types)
- [Storage Locations](#storage-locations)
- [Error Handling](#error-handling)

## SMS Count

### Get SMS Count

Retrieves the count of SMS messages in various categories and storage locations.

**Endpoint**: `GET /api/sms/sms-count`

**Request**: None

**Response**:
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

#### Response Fields

| Field | Description |
|-------|-------------|
| `LocalUnread` | Unread messages in local storage |
| `LocalInbox` | Total inbox messages in local storage |
| `LocalOutbox` | Sent messages in local storage |
| `LocalDraft` | Draft messages in local storage |
| `SimUnread` | Unread messages on SIM card |
| `SimInbox` | Total inbox messages on SIM card |
| `SimOutbox` | Sent messages on SIM card |
| `SimDraft` | Draft messages on SIM card |
| `NewMsg` | Total new (unread) messages |

## SMS List

### Get SMS Messages

Retrieves a list of SMS messages with pagination and filtering options.

**Endpoint**: `POST /api/sms/sms-list`

**Request**:
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

#### Request Parameters

| Parameter | Description | Values |
|-----------|-------------|--------|
| `PageIndex` | Page number (1-based) | 1, 2, 3... |
| `ReadCount` | Messages per page | 1-50 |
| `BoxType` | Message box type | See [Box Types](#box-types) |
| `SortType` | Sort criteria | 0=Date, 1=Name |
| `Ascending` | Sort order | 0=Descending, 1=Ascending |
| `UnreadPreferred` | Show unread first | 0=No, 1=Yes |

#### Box Types

| Value | Description |
|-------|-------------|
| `1` | Inbox |
| `2` | Sent/Outbox |
| `3` | Drafts |

**Response**:
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

#### Response Fields

| Field | Description | Values |
|-------|-------------|--------|
| `Count` | Total messages returned | Number |
| `Smstat` | Message status | See [Message Status](#message-status) |
| `Index` | Unique message identifier | Number |
| `Phone` | Sender/recipient phone number | Phone number |
| `Content` | Message text content | Text |
| `Date` | Message timestamp | YYYY-MM-DD HH:MM:SS |
| `Sca` | Service Center Address | Usually empty |
| `SaveType` | Storage location | See [Save Types](#save-types) |
| `Priority` | Message priority | 0=Normal, 1=High |
| `SmsType` | Message type | See [SMS Types](#sms-types) |

#### Message Status

| Value | Description |
|-------|-------------|
| `0` | Unread |
| `1` | Read |
| `2` | Unsent |
| `3` | Sent |

#### Save Types

| Value | Description |
|-------|-------------|
| `3` | Local storage |
| `4` | SIM card |

#### SMS Types

| Value | Description |
|-------|-------------|
| `1` | Received message |
| `2` | Sent message |
| `3` | Draft message |

## Send SMS

### Send SMS Message

Sends an SMS message to one or more recipients.

**Endpoint**: `POST /api/sms/send-sms`

**Request**:
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

#### Request Parameters

| Parameter | Description | Notes |
|-----------|-------------|-------|
| `Index` | Message index | Use -1 for new messages |
| `Phones` | Recipient phone numbers | Can contain multiple `<Phone>` elements |
| `Sca` | Service Center Address | Usually empty |
| `Content` | Message text content | UTF-8 encoded |
| `Length` | Content length in characters | Must match actual content length |
| `Reserved` | Reserved field | Always set to 1 |
| `Date` | Send timestamp | YYYY-MM-DD HH:MM:SS format |

**Response**: `OK`

### Multiple Recipients

To send to multiple recipients, include multiple phone elements:

```xml
<Phones>
    <Phone>+48500500501</Phone>
    <Phone>+48500500502</Phone>
    <Phone>+48500500503</Phone>
</Phones>
```

## Usage Examples

### Check SMS Count

```bash
curl -X GET http://192.168.8.1/api/sms/sms-count
```

### Get Latest Messages

```bash
curl -X POST http://192.168.8.1/api/sms/sms-list \
     -H "Content-Type: application/xml" \
     -d '<?xml version="1.0" encoding="UTF-8"?><request><PageIndex>1</PageIndex><ReadCount>10</ReadCount><BoxType>1</BoxType><SortType>0</SortType><Ascending>0</Ascending><UnreadPreferred>1</UnreadPreferred></request>'
```

### Send Simple SMS

```bash
curl -X POST http://192.168.8.1/api/sms/send-sms \
     -H "Content-Type: application/xml" \
     -d '<?xml version="1.0" encoding="UTF-8"?><request><Index>-1</Index><Phones><Phone>+1234567890</Phone></Phones><Sca></Sca><Content>Hello World</Content><Length>11</Length><Reserved>1</Reserved><Date>2023-01-01 12:00:00</Date></request>'
```

## Error Handling

### Common Error Codes

- **114001**: Invalid number
- **114002**: SMS sending failed
- **114003**: Invalid phone number
- **114004**: Empty SMS content
- **114005**: SMS database full
- **114006**: SMS already sent
- **115001**: Unknown SMS error

### Implementation Notes

1. **Content Length**: Must accurately reflect character count
2. **Phone Format**: Use international format (+country code)
3. **Encoding**: Content should be UTF-8 encoded
4. **Storage Limits**: Device may have limited SMS storage
5. **Rate Limiting**: Avoid sending SMS too frequently

## Storage Locations

### Local Storage vs SIM Card

- **Local Storage**: Device internal memory, larger capacity
- **SIM Card**: Limited capacity, portable between devices

### Storage Management

Monitor storage usage to prevent message loss:
- Check counts regularly
- Delete old messages when storage is full
- Consider using local storage for better capacity

## Integration Considerations

### ModemOpus Integration

In ModemOpus, SMS functionality is used through:
- `SmsMonitoringService` for message polling
- Repository pattern for message persistence
- Background services for automatic message retrieval
- Analytics services for message statistics

### Best Practices

1. **Regular Polling**: Check for new messages periodically
2. **Storage Management**: Monitor and clean up old messages
3. **Error Handling**: Implement retry logic for failed sends
4. **Character Encoding**: Handle international characters properly
5. **Rate Limiting**: Respect carrier and device limitations

## Related APIs

- [Monitoring API](monitoring.md) - Device status and connectivity
- [Device API](device.md) - Device information and control
- [Security API](security.md) - Access control and authentication