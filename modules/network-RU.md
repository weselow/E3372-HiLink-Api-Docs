# Модуль Network API

## Обзор

Модуль Network API охватывает сетевую функциональность, включая управление соединением (Dialup), конфигурацию WiFi (WLAN) и настройки DHCP-сервера. Этот модуль необходим для сетевого подключения и конфигурации точки доступа.

## Содержание

- [Модуль Dialup](#модуль-dialup)
- [Модуль WLAN](#модуль-wlan)
- [Модуль DHCP](#модуль-dhcp)
- [Дополнительные сетевые модули](#дополнительные-сетевые-модули)

## Модуль Dialup

### Получение статуса соединения

Получает исчерпывающий статус соединения, включая сетевые детали и информацию о сервисе.

**Конечная точка**: `GET /api/dialup/connection`

**Запрос**: Отсутствует

**Ответ**:
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

### Подключение/Отключение

Управляет состоянием соединения мобильных данных.

**Конечная точка**: `POST /api/dialup/dial`

**Запрос на подключение**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Action>1</Action>
</request>
```

**Запрос на отключение**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Action>0</Action>
</request>
```

**Ответ**: `OK`

#### Значения Action

| Значение | Действие | Описание |
|----------|----------|----------|
| `1` | Подключить | Установить соединение мобильных данных |
| `0` | Отключить | Прервать соединение мобильных данных |

## Модуль WLAN

### Получение основных настроек WLAN

Получает конфигурацию WiFi-сети, включая SSID, канал и настройки мощности.

**Конечная точка**: `GET /api/wlan/basic-settings`

**Запрос**: Отсутствует

**Ответ**:
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

#### Поля ответа

| Поле | Описание | Значения |
|------|----------|----------|
| `WifiSsid` | Имя сети | Строка (до 32 символов) |
| `WifiChannel` | WiFi канал | 0=Авто, 1-14=Конкретный канал |
| `WifiHide` | Скрытая сеть | 0=Видимая, 1=Скрытая |
| `WifiCountry` | Код страны | CE, US, JP, и т.д. |
| `WifiMode` | Стандарты WiFi | b, g, n, b/g, b/g/n |
| `WifiRate` | Скорость передачи данных | 0=Авто, конкретные Мбит/с |
| `WifiTxPwrPcnt` | Мощность передачи | 0-100% |
| `WifiMaxassoc` | Макс. соединений | 1-32 устройства |
| `wifiband` | Частотный диапазон | 2.4GHz, 5GHz |
| `wifiautocountryswitch` | Авто определение страны | 0=Отключено, 1=Включено |
| `wifiantennanum` | Количество антенн | Количество антенн |
| `wifiguestofftime` | Тайм-аут гостя | Минуты (0=отключено) |

### Настройка основных параметров WLAN

Настраивает параметры WiFi-сети.

**Конечная точка**: `POST /api/wlan/basic-settings`

**Запрос**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <WifiSsid>МОЯ-WIFI-СЕТЬ</WifiSsid>
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

**Ответ**: `OK`

### Получение настроек безопасности

Получает конфигурацию безопасности WiFi, включая настройки шифрования и аутентификации.

**Конечная точка**: `GET /api/wlan/security-settings`

**Запрос**: Отсутствует

**Ответ**:
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

#### Поля безопасности

| Поле | Описание | Значения |
|------|----------|----------|
| `WifiAuthmode` | Режим аутентификации | OPEN, WEP, WPA, WPA2PSK, WPA/WPA2 |
| `WifiBasicencryptionmodes` | Базовое шифрование | NONE, WEP, TKIP, AES |
| `WifiWpaencryptionmodes` | Шифрование WPA | TKIP, AES, TKIP/AES |
| `WifiWpa2encryptionmodes` | Шифрование WPA2 | TKIP, AES, TKIP/AES |
| `WifiKey` | Пароль WiFi | Строка (8-63 символа для WPA) |
| `WifiKeytype` | Тип ключа | 0=Парольная фраза, 1=Hex |
| `WifiWpsStatus` | Статус WPS | 0=Отключен, 1=Включен |
| `WifiWpsPinCode` | PIN WPS | 8-значный PIN |

## Модуль DHCP

### Получение настроек DHCP

Получает конфигурацию DHCP-сервера, включая диапазон IP и настройки аренды.

**Конечная точка**: `GET /api/dhcp/settings`

**Запрос**: Отсутствует

**Ответ**:
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

#### Поля DHCP

| Поле | Описание | Значения |
|------|----------|----------|
| `DhcpStatus` | Статус DHCP-сервера | 0=Отключен, 1=Включен |
| `DhcpStartIPAddress` | Начальный IP-адрес | IPv4 адрес |
| `DhcpEndIPAddress` | Конечный IP-адрес | IPv4 адрес |
| `DhcpLeaseTime` | Время аренды | Секунды (86400 = 24 часа) |
| `PrimaryDns` | Основной DNS-сервер | IPv4 адрес |
| `SecondaryDns` | Вторичный DNS-сервер | IPv4 адрес |

### Настройка параметров DHCP

Настраивает параметры DHCP-сервера.

**Конечная точка**: `POST /api/dhcp/settings`

**Запрос**:
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

**Ответ**: `OK`

## Дополнительные сетевые модули

### Модуль DDNS

**Получение настроек DDNS**: `GET /api/ddns/ddns-list`
**Настройка DDNS**: `POST /api/ddns/ddns-list`

### Модуль Cradle

**Получение информации Cradle**: `GET /api/cradle/basic-info`
**Настройка конфигурации Cradle**: `POST /api/cradle/basic-info`

## Примеры использования

### Проверка статуса соединения

```bash
curl -X GET http://192.168.8.1/api/dialup/connection
```

### Подключение к мобильной сети

```bash
curl -X POST http://192.168.8.1/api/dialup/dial \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>1</Action></request>"
```

### Настройка WiFi-сети

```bash
curl -X POST http://192.168.8.1/api/wlan/basic-settings \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><WifiSsid>МояСеть</WifiSsid><WifiChannel>6</WifiChannel><WifiHide>0</WifiHide><WifiCountry>RU</WifiCountry><WifiMode>b/g/n</WifiMode><WifiRate>0</WifiRate><WifiTxPwrPcnt>100</WifiTxPwrPcnt><WifiMaxassoc>10</WifiMaxassoc><wifiband>2.4GHz</wifiband><wifiautocountryswitch>1</wifiautocountryswitch><wifiantennanum>2</wifiantennanum><wifiguestofftime>0</wifiguestofftime></request>"
```

### Обновление диапазона DHCP

```bash
curl -X POST http://192.168.8.1/api/dhcp/settings \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><DhcpStatus>1</DhcpStatus><DhcpStartIPAddress>192.168.8.50</DhcpStartIPAddress><DhcpEndIPAddress>192.168.8.150</DhcpEndIPAddress><DhcpLeaseTime>43200</DhcpLeaseTime><PrimaryDns>8.8.8.8</PrimaryDns><SecondaryDns>8.8.4.4</SecondaryDns></request>"
```

## Обработка ошибок

### Распространенные коды ошибок

- **100006**: Неверный параметр (неправильно сформированный запрос)
- **117004**: Неправильный пароль WiFi
- **100004**: Система занята (во время изменений соединения)

### Лучшие практики

1. **Управление соединением**: Дать время для изменений состояния соединения
2. **Конфигурация WiFi**: Проверить параметры перед применением
3. **Настройки DHCP**: Убедиться, что диапазоны IP не конфликтуют с IP устройства
4. **Настройки безопасности**: Использовать сильные пароли и современное шифрование

## Соображения интеграции

### Интеграция с ModemOpus

Network API используются в ModemOpus через:
- Управление соединением в стратегиях смены IP
- Конфигурация WiFi для настройки точки доступа
- Управление DHCP для подключения клиентов
- Мониторинг сети и отслеживание статуса

### Проверка конфигурации

Перед применением сетевых настроек:
- Проверить диапазоны IP-адресов
- Проверить совместимость параметров WiFi
- Убедиться, что настройки безопасности поддерживаются
- Протестировать подключение после изменений

## Связанные API

- [Monitoring API](monitoring-RU.md) - Мониторинг статуса сети
- [Device API](device-RU.md) - Управление устройством и перезагрузка
- [Security API](security-RU.md) - Контроль доступа и аутентификация