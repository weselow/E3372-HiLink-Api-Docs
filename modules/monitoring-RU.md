# Модуль Monitoring API

## Обзор

Модуль Monitoring API предоставляет доступ в реальном времени к информации о статусе устройства и статистике трафика. Это необходимо для мониторинга здоровья соединения, качества сигнала и использования данных.

## Содержание

- [Статус устройства](#статус-устройства)
- [Статистика трафика](#статистика-трафика)
- [Справочник полей статуса](#справочник-полей-статуса)
- [Справочник полей трафика](#справочник-полей-трафика)
- [Примеры использования](#примеры-использования)

## Статус устройства

### Получение текущего статуса

Получает исчерпывающий статус устройства, включая состояние соединения, силу сигнала и сетевую информацию.

**Конечная точка**: `GET /api/monitoring/status`

**Запрос**: Отсутствует

**Ответ**:
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

## Статистика трафика

### Получение статистики трафика

Получает подробную статистику использования трафика, включая текущую сессию и общее использование.

**Конечная точка**: `GET /api/monitoring/traffic-statistics`

**Запрос**: Отсутствует

**Ответ**:
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

## Справочник полей статуса

### Статус соединения

| Поле | Описание | Значения |
|------|----------|----------|
| `ConnectionStatus` | Текущее состояние соединения | См. [Коды статуса соединения](#коды-статуса-соединения) |
| `ServiceStatus` | Доступность сервиса | 0=Нет сервиса, 1=Ограниченный, 2=Доступен |
| `SimStatus` | Статус SIM-карты | 0=Нет SIM, 1=Действительная SIM, 255=Недействительная SIM |
| `RoamingStatus` | Индикатор роуминга | 0=Домашняя сеть, 1=Роуминг |
| `simlockStatus` | Статус блокировки SIM | 0=Разблокирована, 1=Заблокирована |

#### Коды статуса соединения

| Код | Описание |
|-----|----------|
| `900` | Подключается |
| `901` | Подключено |
| `902` | Отключено |
| `903` | Отключается |

### Сетевая информация

| Поле | Описание | Значения |
|------|----------|----------|
| `CurrentNetworkType` | Сетевая технология | См. [Коды типов сети](#коды-типов-сети) |
| `CurrentNetworkTypeEx` | Расширенный тип сети | Расширенная информация о технологии |
| `CurrentServiceDomain` | Домен сервиса | 0=Нет сервиса, 1=Только CS, 2=Только PS, 3=CS+PS |
| `WanIPAddress` | Публичный IP-адрес | IPv4 адрес |
| `WanIPv6Address` | Публичный IPv6 адрес | IPv6 адрес |
| `PrimaryDns` | Основной DNS-сервер | IPv4 адрес |
| `SecondaryDns` | Вторичный DNS-сервер | IPv4 адрес |

#### Коды типов сети

| Код | Описание |
|-----|----------|
| `0` | Нет сервиса |
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

### Информация о сигнале

| Поле | Описание | Значения |
|------|----------|----------|
| `SignalStrength` | Сила сигнала | 0-5 делений (пусто, если недоступно) |
| `maxsignal` | Максимальные деления сигнала | Обычно 5 |

### Информация о WiFi

| Поле | Описание | Значения |
|------|----------|----------|
| `WifiStatus` | Статус WiFi | 0=Выключен, 1=Включен |
| `WifiConnectionStatus` | Состояние соединения WiFi | Детали соединения |
| `CurrentWifiUser` | Подключенные пользователи WiFi | Количество подключенных устройств |
| `TotalWifiUser` | Максимум пользователей WiFi | Лимит устройства |
| `wififrequence` | Частотный диапазон WiFi | 0=2.4ГГц, 1=5ГГц |
| `wifiindooronly` | Режим только для помещений | 0=Нет, 1=Да |

### Информация о питании

| Поле | Описание | Значения |
|------|----------|----------|
| `BatteryStatus` | Статус батареи | 0=Не заряжается, 1=Заряжается |
| `BatteryLevel` | Процент заряда батареи | 0-100% |

### Режим устройства

| Поле | Описание | Значения |
|------|----------|----------|
| `flymode` | Режим полета | 0=Выключен, 1=Включен |
| `cellroam` | Сотовый роуминг | 0=Отключен, 1=Включен |
| `classify` | Классификация устройства | hilink, stick, и т.д. |

## Справочник полей трафика

### Текущая сессия

| Поле | Описание | Единица |
|------|----------|---------|
| `CurrentConnectTime` | Длительность сессии | Секунды |
| `CurrentUpload` | Загружено в сессии | Байты |
| `CurrentDownload` | Скачано в сессии | Байты |
| `CurrentUploadRate` | Текущая скорость загрузки | Байты/секунда |
| `CurrentDownloadRate` | Текущая скорость скачивания | Байты/секунда |

### Общая статистика

| Поле | Описание | Единица |
|------|----------|---------|
| `TotalConnectTime` | Общее время соединения | Секунды |
| `TotalUpload` | Всего загружено данных | Байты |
| `TotalDownload` | Всего скачано данных | Байты |

### Конфигурация

| Поле | Описание | Значения |
|------|----------|----------|
| `showtraffic` | Отображение трафика включено | 0=Скрыто, 1=Видимо |

## Примеры использования

### Проверка статуса соединения

```bash
curl -X GET http://192.168.8.1/api/monitoring/status
```

### Мониторинг использования трафика

```bash
curl -X GET http://192.168.8.1/api/monitoring/traffic-statistics
```

### Скрипт непрерывного мониторинга

```bash
#!/bin/bash
while true; do
    echo "=== $(date) ==="
    echo "Статус:"
    curl -s http://192.168.8.1/api/monitoring/status | grep -E "(ConnectionStatus|SignalStrength|CurrentNetworkType)"
    echo "Трафик:"
    curl -s http://192.168.8.1/api/monitoring/traffic-statistics | grep -E "(CurrentDownloadRate|CurrentUploadRate)"
    echo ""
    sleep 30
done
```

## Стратегии опроса

### Мониторинг в реальном времени

Для обновлений статуса в реальном времени:
- Опрашивать статус каждые 5-10 секунд
- Опрашивать трафик каждые 10-30 секунд
- Корректировать интервалы в зависимости от требований

### Эффективный опрос

Для минимизации нагрузки на устройство:
- Кэшировать статус при стабильном соединении
- Увеличивать частоту опроса во время переходов
- Использовать статистику трафика для мониторинга пропускной способности

### Обработка ошибок

Корректно обрабатывать ошибки опроса:
- Реализовать экспоненциальную задержку при сбоях
- Различать сетевые ошибки и ошибки устройства
- Поддерживать последнее известное хорошее состояние

## Соображения интеграции

### Интеграция с ModemOpus

В ModemOpus API мониторинга используются через:
- `ModemMonitoringBackgroundService` для непрерывного мониторинга
- `TrafficMonitoringService` для отслеживания пропускной способности
- Обновления в реальном времени через SignalR хабы
- Профилирование производительности и мониторинг SLA

### Лучшие практики

1. **Эффективный опрос**: Балансировать частоту обновлений с производительностью устройства
2. **Управление состоянием**: Отслеживать изменения статуса, а не только текущие значения
3. **Восстановление после ошибок**: Обрабатывать временные проблемы с подключением
4. **Агрегация данных**: Объединять несколько метрик для оценки здоровья
5. **Мониторинг пороговых значений**: Устанавливать оповещения для критических изменений статуса

## Связанные API

- [Device API](device-RU.md) - Управление устройством и информация
- [Dialup API](network-RU.md#модуль-dialup) - Управление соединением
- [SMS API](sms-RU.md) - Интеграция мониторинга сообщений