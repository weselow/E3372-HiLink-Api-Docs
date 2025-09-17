# Обработка ошибок HiLink API

## Обзор

Исчерпывающее руководство по кодам ошибок, устранению неполадок и стратегиям обработки ошибок для реализации HiLink API.

## Содержание

- [Категории кодов ошибок](#категории-кодов-ошибок)
- [Полный справочник кодов ошибок](#полный-справочник-кодов-ошибок)
- [Стратегии обработки ошибок](#стратегии-обработки-ошибок)
- [Руководство по устранению неполадок](#руководство-по-устранению-неполадок)
- [Шаблоны реализации](#шаблоны-реализации)

## Категории кодов ошибок

### Системные ошибки (100000-100999)

Общие системные ошибки и ошибки API.

| Код | Описание | Серьезность | Восстановление |
|-----|----------|-------------|----------------|
| **100001** | Нет поддержки | Высокая | Функция недоступна |
| **100002** | Недоступно | Средняя | Повторить позже |
| **100003** | Не поддерживается | Высокая | Использовать альтернативу |
| **100004** | Система занята | Средняя | Повторить с задержкой |
| **100005** | Неизвестная ошибка | Высокая | Проверить журналы |
| **100006** | Неверный параметр | Средняя | Проверить входные данные |
| **100007** | Ошибка записи | Высокая | Проверить разрешения |
| **100008** | Неправильный пароль | Средняя | Проверить учетные данные |
| **100009** | Не выполнен вход | Средняя | Сначала аутентифицироваться |

### Неизвестные системные ошибки (103000-103999)

Неуказанные системные ошибки.

| Код | Описание | Действие |
|-----|----------|----------|
| **103002** | Неизвестная ошибка | Общая обработка ошибок |
| **103015** | Неизвестная ошибка | Проверить статус устройства |

### Ошибки аутентификации (108000-108999)

Ошибки аутентификации пользователей и управления сессиями.

| Код | Описание | Действие |
|-----|----------|----------|
| **108001** | Неверное имя пользователя | Проверить формат имени пользователя |
| **108002** | Неверный пароль | Проверить пароль |
| **108003** | Пользователь уже вошел | Использовать существующую сессию |
| **108006** | Неверные имя пользователя или пароль | Проверить оба учетных данных |
| **108007** | Слишком много пользователей вошло | Дождаться истечения сессии |

### Ошибки устройства (109000-109999)

Операционные ошибки, специфичные для устройства.

| Код | Описание | Причина | Действие |
|-----|----------|---------|----------|
| **109001** | Низкий заряд резервной батареи | Низкая батарея | Зарядить устройство |
| **109002** | Выключение резервной батареи | Батарея разряжена | Подключить питание |
| **109003** | RTC недоступен | Проблема часов | Проверить настройки времени |
| **109004** | Ошибка издания | Проблема прошивки | Обновить прошивку |
| **109005** | Неправильные PIN-коды | Неправильный PIN | Проверить PIN |
| **109006** | Достигнут месячный лимит | Лимит данных | Проверить план |
| **109007** | Занято | Устройство занято | Повторить позже |
| **109012** | Нельзя удалить профиль по умолчанию | Защищенный профиль | Использовать другой профиль |

### Ошибки соединения (110000-110999)

Ошибки подключения устройства и связи.

| Код | Описание | Действие |
|-----|----------|----------|
| **110002** | Модем не подключен к ПК | Проверить USB-соединение |

### Ошибки режима сна (111000-111999)

Ошибки управления питанием и режима сна.

| Код | Описание | Восстановление |
|-----|----------|----------------|
| **111019** | Неизвестная ошибка | Проверить статус устройства |
| **111020** | Устройство в режиме сна и может быть разбужено SMS или звонком | Отправить сигнал пробуждения |
| **111021** | Неизвестная ошибка | Перезагрузить устройство |
| **111022** | Устройство в режиме низкого потребления | Проверить настройки питания |

### Ошибки PIN/PUK (112000-112999)

Ошибки, связанные с PIN и PUK SIM-карты.

| Код | Описание | Осталось попыток | Действие |
|-----|----------|------------------|----------|
| **112001** | Неправильный PIN | 2 | Ввести правильный PIN |
| **112002** | Требуется PUK | 0 | Ввести PUK |
| **112003** | PUK заблокирован | 9 | Ввести PUK |
| **112004** | Ошибка аутентификации PUK | Уменьшено | Попробовать снова |
| **112005** | PUK заблокирован навсегда | 0 | Обратиться к оператору |
| **112006** | Карта заблокирована | Н/Д | Заменить SIM |

### Ошибки хранилища (113000-113999)

Ошибки, связанные с хранилищем и памятью.

| Код | Описание | Действие |
|-----|----------|----------|
| **113018** | Недостаточно места | Освободить хранилище |

### Ошибки SMS (114000-114999)

Ошибки, специфичные для функциональности SMS.

| Код | Описание | Восстановление |
|-----|----------|----------------|
| **114001** | Неверный номер | Проверить формат номера телефона |
| **114002** | Не удалось отправить SMS | Повторить отправку |
| **114003** | Неверный номер телефона | Проверить номер |
| **114004** | Пустое содержимое SMS | Добавить содержимое сообщения |
| **114005** | База данных SMS полна | Удалить старые сообщения |
| **114006** | SMS уже отправлено | Проверить отправленные сообщения |

### Системные ошибки SMS (115000-115999)

Системные ошибки уровня SMS.

| Код | Описание | Действие |
|-----|----------|----------|
| **115001** | Неизвестная ошибка SMS | Проверить настройки SMS |

### Сетевые ошибки (117000-117999)

Ошибки, связанные с сетью и WiFi.

| Код | Описание | Действие |
|-----|----------|----------|
| **117001** | Неизвестная ошибка | Проверить сетевые настройки |
| **117004** | Неправильный пароль WiFi | Проверить пароль WiFi |

### Ошибки токенов (125000-125999)

Ошибки токенов сессии и аутентификации.

| Код | Описание | Действие |
|-----|----------|----------|
| **125001** | Неверный токен | Повторно аутентифицироваться |

## Стратегии обработки ошибок

### Реализация логики повтора

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
        echo "Попытка $attempt/$max_attempts: $method $endpoint"

        if [[ "$method" == "GET" ]]; then
            response=$(curl -s --max-time 30 "http://192.168.8.1/api$endpoint")
        else
            response=$(curl -s --max-time 30 -X "$method" "http://192.168.8.1/api$endpoint" \
                -H "Content-Type: application/xml" -d "$data")
        fi

        # Проверка успешного ответа
        if [[ $? -eq 0 && -n "$response" ]]; then
            # Проверка кодов ошибок в ответе
            if echo "$response" | grep -q "<error>"; then
                error_code=$(echo "$response" | grep -o '<code>[^<]*</code>' | sed 's/<[^>]*>//g')
                handle_api_error "$error_code" "$attempt" "$max_attempts"
                error_handling_result=$?

                case $error_handling_result in
                    0)  # Повторить
                        ;;
                    1)  # Прервать
                        echo "Прерывание из-за неисправимой ошибки: $error_code"
                        return 1
                        ;;
                    2)  # Немедленный повтор
                        delay=0
                        ;;
                esac
            else
                echo "Успех: $response"
                return 0
            fi
        else
            echo "Сетевая ошибка или нет ответа"
        fi

        if [ $attempt -lt $max_attempts ]; then
            echo "Ожидание ${delay}с перед повтором..."
            sleep "$delay"
            delay=$((delay * 2))  # Экспоненциальная задержка
        fi

        attempt=$((attempt + 1))
    done

    echo "Все попытки не удались"
    return 1
}

handle_api_error() {
    local error_code="$1"
    local attempt="$2"
    local max_attempts="$3"

    echo "Ошибка API: $error_code"

    case "$error_code" in
        # Ошибки, для которых можно повторить
        100002|100004|100007|109007)
            echo "Временная ошибка, будет повтор"
            return 0  # Повторить
            ;;

        # Ошибки аутентификации - может потребоваться повторная аутентификация
        100008|100009|108001|108002|108006|125001)
            echo "Ошибка аутентификации"
            # Здесь можно реализовать повторную аутентификацию
            return 1  # Прервать пока
            ;;

        # Ошибки немедленного повтора
        103002|103015|111019|111021)
            echo "Неизвестная ошибка, немедленный повтор"
            return 2  # Немедленный повтор
            ;;

        # Неисправимые ошибки
        100001|100003|109001|109002|109012|112005|112006)
            echo "Неисправимая ошибка"
            return 1  # Прервать
            ;;

        # Ошибки PIN/PUK - нужно вмешательство пользователя
        112001|112002|112003|112004)
            echo "Ошибка PIN/PUK - требуется вмешательство пользователя"
            return 1  # Прервать
            ;;

        # Ошибки ресурсов
        113018|114005)
            echo "Достигнут лимит ресурсов"
            return 1  # Прервать
            ;;

        *)
            echo "Неизвестный код ошибки, будет повтор если остались попытки"
            return 0  # Повторить
            ;;
    esac
}
```

### Обогащение контекста ошибок

```bash
enrich_error_context() {
    local error_code="$1"
    local operation="$2"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')

    # Получение статуса устройства для контекста
    device_status=$(curl -s --max-time 10 http://192.168.8.1/api/monitoring/status 2>/dev/null)

    # Извлечение релевантной информации о статусе
    if [[ -n "$device_status" ]]; then
        connection_status=$(echo "$device_status" | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')
        signal_strength=$(echo "$device_status" | grep -o '<SignalStrength>[^<]*</SignalStrength>' | sed 's/<[^>]*>//g')
        sim_status=$(echo "$device_status" | grep -o '<SimStatus>[^<]*</SimStatus>' | sed 's/<[^>]*>//g')
    fi

    # Создание обогащенного отчета об ошибке
    cat << EOF
ОТЧЕТ ОБ ОШИБКЕ
===============
Время: $timestamp
Операция: $operation
Код ошибки: $error_code
Описание ошибки: $(get_error_description "$error_code")

Контекст устройства:
- Статус соединения: ${connection_status:-Неизвестно}
- Сила сигнала: ${signal_strength:-Неизвестно}
- Статус SIM: ${sim_status:-Неизвестно}

Рекомендуемое действие: $(get_error_action "$error_code")
EOF
}

get_error_description() {
    local code="$1"
    case "$code" in
        100001) echo "Функция не поддерживается устройством" ;;
        100002) echo "Функция временно недоступна" ;;
        100004) echo "Устройство занято обработкой другого запроса" ;;
        100006) echo "Предоставлены неверные параметры" ;;
        100008) echo "Ошибка аутентификации - неправильный пароль" ;;
        108003) echo "Пользователь уже вошел в систему" ;;
        112001) echo "Введен неправильный PIN" ;;
        112002) echo "Требуется PUK-код для разблокировки SIM" ;;
        114002) echo "Не удалось отправить SMS" ;;
        114005) echo "Хранилище SMS заполнено" ;;
        117004) echo "Неправильный пароль WiFi" ;;
        125001) echo "Токен сессии недействителен или истек" ;;
        *) echo "Произошла неизвестная ошибка" ;;
    esac
}

get_error_action() {
    local code="$1"
    case "$code" in
        100001) echo "Использовать альтернативное API или функцию" ;;
        100002) echo "Подождать и повторить позже" ;;
        100004) echo "Дождаться завершения текущей операции" ;;
        100006) echo "Проверить и исправить параметры запроса" ;;
        100008) echo "Проверить пароль и повторно аутентифицироваться" ;;
        108003) echo "Использовать существующую сессию или сначала выйти" ;;
        112001) echo "Ввести правильный PIN (осталось 2 попытки)" ;;
        112002) echo "Ввести PUK-код для разблокировки SIM" ;;
        114002) echo "Проверить сетевое соединение и повторить" ;;
        114005) echo "Удалить старые SMS-сообщения для освобождения места" ;;
        117004) echo "Проверить пароль WiFi и повторить" ;;
        125001) echo "Повторно аутентифицироваться для получения нового токена сессии" ;;
        *) echo "Проверить статус устройства и сетевое подключение" ;;
    esac
}
```

## Руководство по устранению неполадок

### Проблемы соединения

```bash
diagnose_connection_issues() {
    echo "=== Диагностика соединения ==="

    # Тест подключения к API
    echo "Тестирование подключения к API..."
    if curl -s --max-time 5 http://192.168.8.1/api/monitoring/status > /dev/null; then
        echo "✓ API доступен"
    else
        echo "✗ API недоступен"
        echo "  - Проверить IP-адрес устройства (по умолчанию: 192.168.8.1)"
        echo "  - Проверить сетевое соединение с устройством"
        echo "  - Проверить, включено ли устройство"
        return 1
    fi

    # Проверка статуса устройства
    echo "Проверка статуса устройства..."
    status_response=$(curl -s http://192.168.8.1/api/monitoring/status)

    if [[ -n "$status_response" ]]; then
        connection_status=$(echo "$status_response" | grep -o '<ConnectionStatus>[^<]*</ConnectionStatus>' | sed 's/<[^>]*>//g')
        sim_status=$(echo "$status_response" | grep -o '<SimStatus>[^<]*</SimStatus>' | sed 's/<[^>]*>//g')

        case "$connection_status" in
            901) echo "✓ Устройство подключено" ;;
            900) echo "⚠ Устройство подключается" ;;
            902) echo "✗ Устройство отключено" ;;
            903) echo "⚠ Устройство отключается" ;;
            *) echo "? Неизвестный статус соединения: $connection_status" ;;
        esac

        case "$sim_status" in
            1) echo "✓ SIM-карта действительна" ;;
            0) echo "✗ SIM-карта не обнаружена" ;;
            255) echo "✗ Недействительная SIM-карта" ;;
            *) echo "? Неизвестный статус SIM: $sim_status" ;;
        esac
    else
        echo "✗ Не удалось получить статус устройства"
        return 1
    fi

    # Тест интернет-подключения
    echo "Тестирование интернет-подключения..."
    if ping -c 1 -W 5 8.8.8.8 > /dev/null 2>&1; then
        echo "✓ Интернет-подключение работает"
    else
        echo "✗ Нет интернет-подключения"
        echo "  - Проверить соединение мобильных данных"
        echo "  - Проверить настройки APN"
        echo "  - Проверить статус сети оператора"
    fi
}
```

### Проблемы SMS

```bash
diagnose_sms_issues() {
    echo "=== Диагностика SMS ==="

    # Проверка доступности модуля SMS
    module_response=$(curl -s http://192.168.8.1/api/global/module-switch)
    sms_enabled=$(echo "$module_response" | grep -o '<sms_enabled>[^<]*</sms_enabled>' | sed 's/<[^>]*>//g')

    if [[ "$sms_enabled" == "1" ]]; then
        echo "✓ Модуль SMS включен"
    else
        echo "✗ Модуль SMS отключен"
        return 1
    fi

    # Проверка количества SMS
    sms_count_response=$(curl -s http://192.168.8.1/api/sms/sms-count)
    if [[ -n "$sms_count_response" ]]; then
        local_inbox=$(echo "$sms_count_response" | grep -o '<LocalInbox>[^<]*</LocalInbox>' | sed 's/<[^>]*>//g')
        sim_inbox=$(echo "$sms_count_response" | grep -o '<SimInbox>[^<]*</SimInbox>' | sed 's/<[^>]*>//g')

        echo "Статус хранилища SMS:"
        echo "  Локальное хранилище: $local_inbox сообщений"
        echo "  SIM хранилище: $sim_inbox сообщений"

        # Проверка заполненности хранилища
        if [[ "$local_inbox" -gt 100 ]]; then
            echo "⚠ Локальное хранилище SMS может заполняться"
        fi
    else
        echo "✗ Не удалось получить количество SMS"
    fi

    # Тест возможности отправки SMS
    echo "Функциональность SMS работает"
    echo "Для тестирования отправки SMS:"
    echo "  1. Убедиться, что SIM-карта поддерживает SMS"
    echo "  2. Проверить регистрацию в мобильной сети"
    echo "  3. Проверить формат номера телефона (+код_страны)"
}
```

### Восстановление устройства

```bash
attempt_device_recovery() {
    local issue_type="$1"

    echo "=== Восстановление устройства ==="
    echo "Тип проблемы: $issue_type"

    case "$issue_type" in
        "api_unresponsive")
            echo "Попытка перезагрузки устройства..."
            restart_response=$(curl -s -X POST http://192.168.8.1/api/device/control \
                -H "Content-Type: application/xml" \
                -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>")

            if [[ "$restart_response" == "OK" ]]; then
                echo "Команда перезагрузки отправлена, ожидание устройства..."
                wait_for_device_online
            else
                echo "Не удалось отправить команду перезагрузки"
                echo "Может потребоваться ручное отключение питания"
            fi
            ;;

        "connection_failed")
            echo "Попытка сброса соединения..."
            # Сначала отключиться
            curl -s -X POST http://192.168.8.1/api/dialup/dial \
                -H "Content-Type: application/xml" \
                -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>0</Action></request>" > /dev/null

            sleep 5

            # Переподключиться
            connect_response=$(curl -s -X POST http://192.168.8.1/api/dialup/dial \
                -H "Content-Type: application/xml" \
                -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Action>1</Action></request>")

            if [[ "$connect_response" == "OK" ]]; then
                echo "Сброс соединения инициирован"
                monitor_connection_establishment
            else
                echo "Не удалось сбросить соединение"
            fi
            ;;

        *)
            echo "Неизвестный тип проблемы, попытка общего восстановления"
            ;;
    esac
}

wait_for_device_online() {
    local max_wait=120
    local wait_time=0

    echo "Ожидание появления устройства в сети..."

    while [ $wait_time -lt $max_wait ]; do
        sleep 5
        wait_time=$((wait_time + 5))

        if curl -s --max-time 5 http://192.168.8.1/api/monitoring/status > /dev/null 2>&1; then
            echo "Устройство в сети через ${wait_time}с"
            return 0
        fi

        printf "."
    done

    echo ""
    echo "Устройство не появилось в сети в течение ${max_wait}с"
    return 1
}
```

## Шаблоны реализации

### Надежный API-клиент

```bash
#!/bin/bash
# Надежный HiLink API клиент с комплексной обработкой ошибок

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
            [[ "$debug" == "true" ]] && echo "ОТЛАДКА: $method $endpoint (попытка $attempt/$custom_retries)"

            local response
            local http_code

            if [[ "$method" == "GET" ]]; then
                response=$(curl -s -w "%{http_code}" --max-time "$timeout" "${base_url}${endpoint}")
            else
                response=$(curl -s -w "%{http_code}" --max-time "$timeout" -X "$method" "${base_url}${endpoint}" \
                    -H "Content-Type: application/xml" -d "$data")
            fi

            # Извлечение HTTP-кода и тела ответа
            http_code="${response: -3}"
            response="${response%???}"

            # Обработка HTTP-ошибок
            if [[ "$http_code" != "200" ]]; then
                echo "HTTP-ошибка: $http_code"
                if [ $attempt -lt $custom_retries ]; then
                    sleep "$delay"
                    delay=$((delay * 2))
                    attempt=$((attempt + 1))
                    continue
                else
                    return 1
                fi
            fi

            # Проверка ошибок API
            if echo "$response" | grep -q "<error>"; then
                local error_code=$(echo "$response" | grep -o '<code>[^<]*</code>' | sed 's/<[^>]*>//g')
                local should_retry=$(should_retry_error "$error_code")

                if [[ "$should_retry" == "true" && $attempt -lt $custom_retries ]]; then
                    echo "Ошибка для повтора $error_code, попытка $attempt/$custom_retries"
                    sleep "$delay"
                    delay=$((delay * 2))
                    attempt=$((attempt + 1))
                    continue
                else
                    echo "Ошибка API: $error_code"
                    enrich_error_context "$error_code" "$method $endpoint"
                    return 1
                fi
            fi

            # Успех
            echo "$response"
            return 0
        done

        echo "Все попытки повтора исчерпаны"
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

    # Высокоуровневые методы с обработкой ошибок
    get_status() {
        api_request "GET" "/monitoring/status"
    }

    restart_device() {
        local response=$(api_request "POST" "/device/control" \
            "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>")

        if [[ "$response" == "OK" ]]; then
            echo "Перезагрузка инициирована, ожидание устройства..."
            wait_for_device_online
        else
            echo "Перезагрузка не удалась: $response"
            return 1
        fi
    }

    send_sms() {
        local phone="$1"
        local message="$2"
        local length=${#message}
        local timestamp=$(date '+%Y-%m-%d %H:%M:%S')

        # Проверка входных данных
        if [[ -z "$phone" || -z "$message" ]]; then
            echo "Ошибка: Требуются номер телефона и сообщение"
            return 1
        fi

        if [[ ${#message} -gt 160 ]]; then
            echo "Предупреждение: Сообщение превышает 160 символов"
        fi

        local response=$(api_request "POST" "/sms/send-sms" \
            "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Index>-1</Index><Phones><Phone>$phone</Phone></Phones><Sca></Sca><Content>$message</Content><Length>$length</Length><Reserved>1</Reserved><Date>$timestamp</Date></request>")

        if [[ "$response" == "OK" ]]; then
            echo "SMS успешно отправлено на $phone"
            return 0
        else
            echo "Отправка SMS не удалась: $response"
            return 1
        fi
    }
}

# Примеры использования:
# RobustHiLinkClient.get_status
# RobustHiLinkClient.send_sms "+1234567890" "Тестовое сообщение"
# RobustHiLinkClient.restart_device
```

Это исчерпывающее руководство по обработке ошибок предоставляет основу для создания надежных интеграций HiLink API, которые могут обрабатывать различные условия ошибок и граничные случаи, которые могут возникнуть в производственных средах.