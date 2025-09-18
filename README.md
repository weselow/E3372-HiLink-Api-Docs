# Документация Huawei E3372 HiLink API

[English version](README-EN.md) 

![Huawei E3372](img/e3372.png)

## Обзор

Полная документация по API Huawei HiLink, предоставляющая исчерпывающий справочник по управлению модемами HiLink через HTTP-запросы.

HiLink устройства обычно нельзя программировать с помощью AT-команд или библиотек, предназначенных для управления модемами. У них есть WebUI на основе JavaScript, который управляет модемом. Это можно использовать для написания соответствующих скриптов или библиотек для управления модемами HiLink.

## Содержание

### Основная документация
- [Обзор API](overview-RU.md) - Общие принципы и аутентификация
- [Аутентификация](authentication-RU.md) - Управление сессиями и безопасность

### Модули API

#### Доступные методы API

- **[Device API](modules/device-RU.md)** - Информация об устройстве и управление
  - `GET /api/device/information` - Получение информации об устройстве
  - `POST /api/device/control` - Управление устройством (перезагрузка/выключение)
  - `GET /api/device/autorun-version` - Проверка версии автозапуска

- **[SMS API](modules/sms-RU.md)** - Управление SMS
  - `GET /api/sms/sms-count` - Получение количества SMS
  - `POST /api/sms/sms-list` - Получение списка SMS с фильтрацией
  - `POST /api/sms/send-sms` - Отправка SMS-сообщений

- **[Monitoring API](modules/monitoring-RU.md)** - Мониторинг статуса и трафика
  - `GET /api/monitoring/status` - Получение статуса устройства
  - `GET /api/monitoring/traffic-statistics` - Получение статистики трафика

- **[Network API](modules/network-RU.md)** - Конфигурация сети, WLAN, DHCP
  - `GET /api/dialup/connection` - Получение статуса соединения
  - `POST /api/dialup/dial` - Подключение/отключение мобильных данных
  - `GET /api/wlan/basic-settings` - Получение настроек WiFi
  - `POST /api/wlan/basic-settings` - Настройка параметров WiFi
  - `GET /api/wlan/security-settings` - Получение настроек безопасности WiFi
  - `GET /api/dhcp/settings` - Получение настроек DHCP
  - `POST /api/dhcp/settings` - Настройка параметров DHCP
  - `GET /api/ddns/ddns-list` - Получение настроек DDNS
  - `POST /api/ddns/ddns-list` - Настройка DDNS
  - `GET /api/cradle/basic-info` - Получение информации Cradle
  - `POST /api/cradle/basic-info` - Настройка конфигурации Cradle

- **[Security API](modules/security-RU.md)** - Безопасность, управление PIN
  - `GET /api/user/state-login` - Проверка статуса аутентификации
  - `POST /api/user/login` - Вход в систему
  - `POST /api/pin/operate` - Операции с PIN (разблокировка SIM)

- **[System API](modules/system-RU.md)** - Глобальные настройки и конфигурация
  - `GET /api/global/module-switch` - Получение статуса модулей

- **[Advanced API](modules/advanced-RU.md)** - OTA, диагностика, голосовые функции
  - `POST /api/diagnosis/ping` - Тестирование сетевого подключения
  - `POST /api/filemanager/upload` - Загрузка файлов на устройство
  - `POST /api/ussd/send` - Отправка USSD-кодов

### Примеры и использование
- [Общие задачи](examples/common-tasks-RU.md) - Практические примеры использования
- [Обработка ошибок](examples/error-handling-RU.md) - Коды ошибок и устранение неполадок

## Быстрый старт

### Базовая проверка статуса
```bash
curl -X GET http://192.168.8.1/api/monitoring/status
```

### Перезагрузка устройства
```bash
curl -X POST http://192.168.8.1/api/device/control \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>"
```

### Отправка SMS
```bash
curl -X POST http://192.168.8.1/api/sms/send-sms \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Index>-1</Index><Phones><Phone>+1234567890</Phone></Phones><Sca></Sca><Content>Тестовое сообщение</Content><Length>20</Length><Reserved>1</Reserved><Date>$(date '+%Y-%m-%d %H:%M:%S')</Date></request>"
```

## Варианты API

HiLink API работает в двух режимах:

- **Информационный вариант**: GET-запросы для получения данных
- **Управляющий вариант**: POST-запросы для изменения настроек или запуска действий

Большинство конечных точек поддерживают оба режима, используя один и тот же URL-путь.

## Базовый URL

Все конечные точки API используют базовый URL: `http://192.168.8.1/api/`

Альтернатива: `http://hi.link/api/` (если настроено)

## Источники знаний

- JavaScript файлы устройства (основной источник)
- [Programming and Installing HUAWEI HiLink E3131 under Linux](https://chaddyhv.wordpress.com/2012/08/13/programming-and-installing-huawei-hilink-e3131-under-linux/)
- [BlackyPanther/Huawei-HiLink](https://github.com/BlackyPanther/Huawei-HiLink) - PHP реализация
- [Forum 4PDA](http://4pda.ru/forum/index.php) - Обсуждения сообщества