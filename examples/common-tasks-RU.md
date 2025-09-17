# Общие задачи HiLink API

## Обзор

Этот документ предоставляет практические примеры для общих операций HiLink API, включая полные рабочие процессы и шаблоны обработки ошибок.

## Содержание

- [Управление устройством](#управление-устройством)
- [Управление соединением](#управление-соединением)
- [Операции SMS](#операции-sms)
- [Мониторинг и статус](#мониторинг-и-статус)
- [Конфигурация сети](#конфигурация-сети)
- [Скрипты автоматизации](#скрипты-автоматизации)

## Управление устройством

### Полная проверка информации об устройстве

```bash
#!/bin/bash
# Получение исчерпывающей информации об устройстве

echo "=== Информация об устройстве ==="
echo "Основная информация:"
curl -s http://192.168.8.1/api/device/information | \
    grep -E "<DeviceName>|<SerialNumber>|<Imei>|<SoftwareVersion>" | \
    sed 's/<[^>]*>//g' | sed 's/^[ \t]*//'

echo ""
echo "Текущий статус:"
curl -s http://192.168.8.1/api/monitoring/status | \
    grep -E "<ConnectionStatus>|<SignalStrength>|<CurrentNetworkType>" | \
    sed 's/<[^>]*>//g' | sed 's/^[ \t]*//'

echo ""
echo "Доступные функции:"
curl -s http://192.168.8.1/api/global/module-switch | \
    grep -E "sms_enabled|wifi_enabled|ota_enabled" | \
    sed 's/<[^>]*>//g' | sed 's/^[ \t]*//'
```

### Безопасная перезагрузка устройства

```bash
#!/bin/bash
# Перезагрузка устройства с подтверждением и мониторингом

echo "Подготовка к перезагрузке устройства..."

# Проверка текущего статуса
STATUS=$(curl -s http://192.168.8.1/api/monitoring/status | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')
echo "Текущий статус соединения: $STATUS"

# Отправка команды перезагрузки
echo "Отправка команды перезагрузки..."
RESPONSE=$(curl -s -X POST http://192.168.8.1/api/device/control \
    -H "Content-Type: application/xml" \
    -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>")

if [[ "$RESPONSE" == "OK" ]]; then
    echo "Команда перезагрузки принята"
    echo "Ожидание доступности устройства..."

    # Ожидание возврата устройства в сеть
    for i in {1..60}; do
        sleep 5
        if curl -s --max-time 5 http://192.168.8.1/api/monitoring/status > /dev/null 2>&1; then
            echo "Устройство снова в сети через $(($i * 5)) секунд"
            break
        fi
        echo "Ожидание... (попытка $i/60)"
    done
else
    echo "Команда перезагрузки не удалась: $RESPONSE"
    exit 1
fi
```

## Управление соединением

### Интеллектуальное управление соединением

```bash
#!/bin/bash
# Подключение с мониторингом статуса и логикой повтора

connect_with_retry() {
    local max_attempts=3
    local attempt=1

    while [ $attempt -le $max_attempts ]; do
        echo "Попытка подключения $attempt/$max_attempts"

        # Отправка команды подключения
        RESPONSE=$(curl -s -X POST http://192.168.8.1/api/dialup/dial \
            -H "Content-Type: application/xml" \
            -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>1</Action></request>")

        if [[ "$RESPONSE" == "OK" ]]; then
            echo "Команда подключения отправлена успешно"

            # Мониторинг установления соединения
            for i in {1..30}; do
                sleep 2
                STATUS=$(curl -s http://192.168.8.1/api/monitoring/status | \
                    grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')

                case $STATUS in
                    "901")
                        echo "Подключение успешно"
                        return 0
                        ;;
                    "900")
                        echo "Подключение..."
                        ;;
                    "902")
                        echo "Подключение не удалось"
                        break
                        ;;
                    *)
                        echo "Неизвестный статус: $STATUS"
                        ;;
                esac
            done
        fi

        echo "Попытка $attempt не удалась"
        attempt=$((attempt + 1))
        sleep 5
    done

    echo "Все попытки подключения не удались"
    return 1
}

# Выполнение подключения
connect_with_retry
```

### Проверка здоровья соединения

```bash
#!/bin/bash
# Комплексная оценка здоровья соединения

check_connection_health() {
    echo "=== Проверка здоровья соединения ==="

    # Получение базового статуса
    STATUS_RESPONSE=$(curl -s http://192.168.8.1/api/monitoring/status)

    # Извлечение ключевых метрик
    CONNECTION_STATUS=$(echo "$STATUS_RESPONSE" | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')
    SIGNAL_STRENGTH=$(echo "$STATUS_RESPONSE" | grep -o '<SignalStrength>[^<]*</SignalStrength>' | sed 's/<[^>]*>//g')
    NETWORK_TYPE=$(echo "$STATUS_RESPONSE" | grep -o '<CurrentNetworkType>[^<]*</CurrentNetworkType>' | sed 's/<[^>]*>//g')
    WAN_IP=$(echo "$STATUS_RESPONSE" | grep -o '<WanIPAddress>[^<]*</WanIPAddress>' | sed 's/<[^>]*>//g')

    # Оценка соединения
    case $CONNECTION_STATUS in
        "901")
            echo "✓ Статус соединения: Подключено"
            ;;
        "900")
            echo "⚠ Статус соединения: Подключается"
            ;;
        "902")
            echo "✗ Статус соединения: Отключено"
            ;;
        *)
            echo "? Статус соединения: Неизвестно ($CONNECTION_STATUS)"
            ;;
    esac

    # Оценка силы сигнала
    if [[ -n "$SIGNAL_STRENGTH" && "$SIGNAL_STRENGTH" != "" ]]; then
        if [[ "$SIGNAL_STRENGTH" -ge 3 ]]; then
            echo "✓ Сила сигнала: Хорошая ($SIGNAL_STRENGTH/5)"
        elif [[ "$SIGNAL_STRENGTH" -ge 1 ]]; then
            echo "⚠ Сила сигнала: Слабая ($SIGNAL_STRENGTH/5)"
        else
            echo "✗ Сила сигнала: Очень плохая ($SIGNAL_STRENGTH/5)"
        fi
    else
        echo "? Сила сигнала: Неизвестно"
    fi

    # Оценка типа сети
    case $NETWORK_TYPE in
        "19"|"44"|"45")
            echo "✓ Тип сети: LTE/4G ($NETWORK_TYPE)"
            ;;
        "4"|"5"|"6"|"7"|"9"|"41")
            echo "⚠ Тип сети: 3G ($NETWORK_TYPE)"
            ;;
        "1"|"2"|"3")
            echo "⚠ Тип сети: 2G ($NETWORK_TYPE)"
            ;;
        *)
            echo "? Тип сети: Неизвестно ($NETWORK_TYPE)"
            ;;
    esac

    # Проверка IP-адреса
    if [[ -n "$WAN_IP" && "$WAN_IP" != "" ]]; then
        echo "✓ WAN IP: $WAN_IP"

        # Тест подключения
        if ping -c 1 -W 5 8.8.8.8 > /dev/null 2>&1; then
            echo "✓ Интернет-подключение: Работает"
        else
            echo "✗ Интернет-подключение: Не удалось"
        fi
    else
        echo "✗ WAN IP: Не назначен"
    fi
}

check_connection_health
```

## Операции SMS

### Рабочий процесс управления SMS

```bash
#!/bin/bash
# Полный рабочий процесс управления SMS

sms_management() {
    echo "=== Управление SMS ==="

    # Проверка количества SMS
    echo "Проверка количества SMS..."
    SMS_COUNT_RESPONSE=$(curl -s http://192.168.8.1/api/sms/sms-count)

    UNREAD=$(echo "$SMS_COUNT_RESPONSE" | grep -o '<LocalUnread>[^<]*</LocalUnread>' | sed 's/<[^>]*>//g')
    INBOX=$(echo "$SMS_COUNT_RESPONSE" | grep -o '<LocalInbox>[^<]*</LocalInbox>' | sed 's/<[^>]*>//g')

    echo "Непрочитанные сообщения: $UNREAD"
    echo "Всего во входящих: $INBOX"

    if [[ "$UNREAD" -gt 0 ]]; then
        echo "Получение непрочитанных сообщений..."

        # Получение непрочитанных сообщений
        SMS_LIST_RESPONSE=$(curl -s -X POST http://192.168.8.1/api/sms/sms-list \
            -H "Content-Type: application/xml" \
            -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><PageIndex>1</PageIndex><ReadCount>$UNREAD</ReadCount><BoxType>1</BoxType><SortType>0</SortType><Ascending>0</Ascending><UnreadPreferred>1</UnreadPreferred></request>")

        # Разбор и отображение сообщений (упрощенно)
        echo "Последние сообщения:"
        echo "$SMS_LIST_RESPONSE" | grep -o '<Phone>[^<]*</Phone>' | sed 's/<[^>]*>//g' | head -5
    fi
}

send_sms() {
    local phone="$1"
    local message="$2"
    local length=${#message}
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')

    echo "Отправка SMS на $phone: $message"

    RESPONSE=$(curl -s -X POST http://192.168.8.1/api/sms/send-sms \
        -H "Content-Type: application/xml" \
        -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Index>-1</Index><Phones><Phone>$phone</Phone></Phones><Sca></Sca><Content>$message</Content><Length>$length</Length><Reserved>1</Reserved><Date>$timestamp</Date></request>")

    if [[ "$RESPONSE" == "OK" ]]; then
        echo "SMS отправлено успешно"
        return 0
    else
        echo "Отправка SMS не удалась: $RESPONSE"
        return 1
    fi
}

# Пример использования
sms_management
# send_sms "+1234567890" "Тестовое сообщение из HiLink API"
```

## Мониторинг и статус

### Скрипт непрерывного мониторинга

```bash
#!/bin/bash
# Непрерывный мониторинг устройства с оповещениями

ALERT_THRESHOLD_SIGNAL=2
ALERT_THRESHOLD_RATE=1000000  # 1MB/s

monitor_device() {
    local interval=${1:-30}
    local log_file="hilink_monitor.log"

    echo "Запуск мониторинга устройства HiLink (интервал: ${interval}с)"
    echo "Файл журнала: $log_file"

    while true; do
        timestamp=$(date '+%Y-%m-%d %H:%M:%S')

        # Получение статуса
        status_response=$(curl -s --max-time 10 http://192.168.8.1/api/monitoring/status)
        traffic_response=$(curl -s --max-time 10 http://192.168.8.1/api/monitoring/traffic-statistics)

        if [[ $? -eq 0 && -n "$status_response" ]]; then
            # Извлечение метрик
            connection_status=$(echo "$status_response" | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')
            signal_strength=$(echo "$status_response" | grep -o '<SignalStrength>[^<]*</SignalStrength>' | sed 's/<[^>]*>//g')
            network_type=$(echo "$status_response" | grep -o '<CurrentNetworkType>[^<]*</CurrentNetworkType>' | sed 's/<[^>]*>//g')

            download_rate=$(echo "$traffic_response" | grep -o '<CurrentDownloadRate>[^<]*</CurrentDownloadRate>' | sed 's/<[^>]*>//g')
            upload_rate=$(echo "$traffic_response" | grep -o '<CurrentUploadRate>[^<]*</CurrentUploadRate>' | sed 's/<[^>]*>//g')

            # Журналирование метрик
            echo "$timestamp,Статус:$connection_status,Сигнал:$signal_strength,Сеть:$network_type,Скач:$download_rate,Загр:$upload_rate" >> "$log_file"

            # Проверка оповещений
            if [[ "$connection_status" != "901" ]]; then
                echo "ОПОВЕЩЕНИЕ: Соединение не установлено (Статус: $connection_status)"
            fi

            if [[ -n "$signal_strength" && "$signal_strength" -lt "$ALERT_THRESHOLD_SIGNAL" ]]; then
                echo "ОПОВЕЩЕНИЕ: Низкая сила сигнала ($signal_strength)"
            fi

            if [[ -n "$download_rate" && "$download_rate" -gt "$ALERT_THRESHOLD_RATE" ]]; then
                echo "ИНФО: Высокая скорость скачивания ($download_rate байт/с)"
            fi

            # Вывод в консоль
            printf "\r$timestamp | Статус: $connection_status | Сигнал: $signal_strength | Скач: ${download_rate:-0} | Загр: ${upload_rate:-0} байт/с"
        else
            echo "ОШИБКА: Невозможно подключиться к устройству в $timestamp"
        fi

        sleep "$interval"
    done
}

# Запуск мониторинга
monitor_device 30
```

## Конфигурация сети

### Автоматизация настройки WiFi

```bash
#!/bin/bash
# Автоматизированная конфигурация WiFi

configure_wifi() {
    local ssid="$1"
    local password="$2"
    local channel="${3:-0}"  # Автоканал по умолчанию

    echo "Настройка WiFi-сети: $ssid"

    # Установка основных настроек WiFi
    basic_response=$(curl -s -X POST http://192.168.8.1/api/wlan/basic-settings \
        -H "Content-Type: application/xml" \
        -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><WifiSsid>$ssid</WifiSsid><WifiChannel>$channel</WifiChannel><WifiHide>0</WifiHide><WifiCountry>RU</WifiCountry><WifiMode>b/g/n</WifiMode><WifiRate>0</WifiRate><WifiTxPwrPcnt>100</WifiTxPwrPcnt><WifiMaxassoc>10</WifiMaxassoc><wifiband>2.4GHz</wifiband><wifiautocountryswitch>1</wifiautocountryswitch><wifiantennanum>2</wifiantennanum><wifiguestofftime>0</wifiguestofftime></request>")

    if [[ "$basic_response" == "OK" ]]; then
        echo "✓ Основные настройки WiFi настроены"

        # Установка настроек безопасности
        security_response=$(curl -s -X POST http://192.168.8.1/api/wlan/security-settings \
            -H "Content-Type: application/xml" \
            -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><WifiAuthmode>WPA2PSK</WifiAuthmode><WifiBasicencryptionmodes>AES</WifiBasicencryptionmodes><WifiWpaencryptionmodes>AES</WifiWpaencryptionmodes><WifiWpa2encryptionmodes>AES</WifiWpa2encryptionmodes><WifiKey>$password</WifiKey><WifiRestoredefaults>0</WifiRestoredefaults><WifiKeytype>0</WifiKeytype><wifiwepkeytype>1</wifiwepkeytype><WifiWepKey1></WifiWepKey1><WifiWepKey2></WifiWepKey2><WifiWepKey3></WifiWepKey3><WifiWepKey4></WifiWepKey4><WifiWepKeyIndex>0</WifiWepKeyIndex><WifiWpsStatus>0</WifiWpsStatus><WifiWpsPinCode>12345670</WifiWpsPinCode><wifiwpsversion>0</wifiwpsversion><wifiwpspinmode>0</wifiwpspinmode></request>")

        if [[ "$security_response" == "OK" ]]; then
            echo "✓ Безопасность WiFi настроена"
            echo "WiFi-сеть '$ssid' настроена успешно"
            return 0
        else
            echo "✗ Не удалось настроить безопасность WiFi: $security_response"
            return 1
        fi
    else
        echo "✗ Не удалось настроить основные настройки WiFi: $basic_response"
        return 1
    fi
}

# Пример использования
# configure_wifi "МояДомашняяСеть" "БезопасныйПароль123" "6"
```

## Скрипты автоматизации

### Проверка здоровья и автовосстановление

```bash
#!/bin/bash
# Автоматизированная проверка здоровья с действиями восстановления

AUTO_RECOVERY=true
MAX_RECOVERY_ATTEMPTS=3
RECOVERY_ATTEMPT_COUNT=0

perform_health_check() {
    echo "=== Проверка здоровья $(date) ==="

    # Тест подключения к API
    if ! curl -s --max-time 10 http://192.168.8.1/api/monitoring/status > /dev/null; then
        echo "✗ API не отвечает"
        return 1
    fi

    # Проверка статуса соединения
    status_response=$(curl -s http://192.168.8.1/api/monitoring/status)
    connection_status=$(echo "$status_response" | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')

    if [[ "$connection_status" != "901" ]]; then
        echo "✗ Не подключено (Статус: $connection_status)"
        return 2
    fi

    # Тест интернет-подключения
    if ! ping -c 1 -W 5 8.8.8.8 > /dev/null 2>&1; then
        echo "✗ Нет интернет-подключения"
        return 3
    fi

    echo "✓ Все проверки пройдены"
    RECOVERY_ATTEMPT_COUNT=0  # Сброс счетчика при успехе
    return 0
}

attempt_recovery() {
    local issue="$1"

    if [[ "$AUTO_RECOVERY" != "true" ]]; then
        echo "Автовосстановление отключено"
        return 1
    fi

    if [[ $RECOVERY_ATTEMPT_COUNT -ge $MAX_RECOVERY_ATTEMPTS ]]; then
        echo "Достигнуто максимальное количество попыток восстановления ($MAX_RECOVERY_ATTEMPTS)"
        return 1
    fi

    RECOVERY_ATTEMPT_COUNT=$((RECOVERY_ATTEMPT_COUNT + 1))
    echo "Попытка восстановления $RECOVERY_ATTEMPT_COUNT/$MAX_RECOVERY_ATTEMPTS для проблемы: $issue"

    case $issue in
        2)  # Проблема соединения
            echo "Попытка переподключения..."
            curl -s -X POST http://192.168.8.1/api/dialup/dial \
                -H "Content-Type: application/xml" \
                -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>0</Action></request>" > /dev/null
            sleep 5
            curl -s -X POST http://192.168.8.1/api/dialup/dial \
                -H "Content-Type: application/xml" \
                -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>1</Action></request>" > /dev/null
            ;;
        1)  # Проблема API
            echo "Устройство может нуждаться в перезагрузке"
            # Здесь можно реализовать логику перезагрузки
            ;;
        *)
            echo "Неизвестная проблема, пробуем общее восстановление"
            ;;
    esac

    sleep 30  # Ожидание перед следующей проверкой
    return 0
}

# Основной цикл мониторинга
while true; do
    perform_health_check
    health_status=$?

    if [[ $health_status -ne 0 ]]; then
        attempt_recovery $health_status
    fi

    sleep 60  # Проверка каждую минуту
done
```

## Примеры интеграции

### Интеграция в стиле ModemOpus

```bash
#!/bin/bash
# Пример интеграции, похожий на паттерны ModemOpus

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

# Примеры использования
# HiLinkClient.get_status
# HiLinkClient.restart_device
```

Это завершает документацию по общим задачам с практическими примерами из реального мира, которые могут быть непосредственно использованы или адаптированы под конкретные нужды.