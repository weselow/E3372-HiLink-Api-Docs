# Документация Huawei HiLink API

HiLink устройства обычно нельзя программировать с помощью AT-команд или библиотек, предназначенных для управления модемами. У них есть WebUI на основе JavaScript, который управляет модемом. Это можно использовать для написания соответствующих скриптов или библиотек для управления модемами HiLink.

## Содержание

1. [Общие инструкции](#общие-инструкции)
2. [Страницы API - запросы и ответы](#страницы-api---запросы-и-ответы)
   - [Модуль Cradle](#модуль-cradle)
   - [Модуль DDNS](#модуль-ddns)
   - [Модуль Device](#модуль-device)
   - [Модуль DHCP](#модуль-dhcp)
   - [Модуль Diagnosis](#модуль-diagnosis)
   - [Модуль Dialup](#модуль-dialup)
   - [Модуль Filemanager](#модуль-filemanager)
   - [Модуль Global](#модуль-global)
   - [Модуль Host](#модуль-host)
   - [Модуль Language](#модуль-language)
   - [Модуль Monitoring](#модуль-monitoring)
   - [Модуль Net](#модуль-net)
   - [Модуль Online-update](#модуль-online-update)
   - [Модуль OTA](#модуль-ota)
   - [Модуль PB](#модуль-pb)
   - [Модуль PIN](#модуль-pin)
   - [Модуль Redirection](#модуль-redirection)
   - [Модуль SDCard](#модуль-sdcard)
   - [Модуль Security](#модуль-security)
   - [Модуль SMS](#модуль-sms)
   - [Модуль SNTP](#модуль-sntp)
   - [Модуль STK](#модуль-stk)
   - [Модуль User](#модуль-user)
   - [Модуль USSD](#модуль-ussd)
   - [Модуль Voice](#модуль-voice)
   - [Модуль Webserver](#модуль-webserver)
   - [Модуль WLAN](#модуль-wlan)
   - [Примечания](#примечания)
3. [Использование API](#использование-api)
4. [Номера ошибок](#номера-ошибок)
5. [Прочее](#прочее)
   - [Модификация файлов WebUI](#модификация-файлов-webui)
   - [Автоматический ввод PIN](#автоматический-ввод-pin)
   - [Список страниц конфигурации HiLink](#список-страниц-конфигурации-hilink)
   - [Список файлов JavaScript](#список-файлов-javascript)
6. [Источники знаний](#источники-знаний)

## Общие инструкции

API HiLink можно разделить на два варианта:

- **Информационный вариант:**
  Этот вариант обычно требует загрузки (**GET**) соответствующей страницы с модема (http://hi.link или http://192.168.8.1). То есть получения **ответа** сразу.

- **Управляющий/конфигурационный вариант:**
  Требует отправки (**POST**) запроса (**request**) вместе с адресом соответствующей страницы на модем. В ответе на положительно выполненную команду мы обычно получаем **OK**.

Информационный вариант и управляющий вариант обычно имеют одинаковые адреса.

Некоторые версии программного обеспечения могут требовать предоставления текущего токена для проверки сессии (я не нашел никакой информации о том, когда изменяется токен; токен можно отключить, редактируя конфигурационные файлы).

Насколько я смог определить, программное обеспечение HiLink унифицировано. Это означает, что, например, модем HiLink содержит те же файлы WebUI, что и маршрутизатор HiLink, и наоборот. Конечно, это не означает, что мы включим WiFi на модеме, например. Некоторые страницы API заблокированы аппаратно или конфигурационными файлами.

Следующий список адресов API основан на файлах JavaScript, содержащихся в модифицированном WebUI для модема E3372s-153. Из тех же файлов определяются запросы и ответы. Ответы, приведенные ниже, в нескольких случаях дополнены на основе ответа модема E3372s-153.

Список отсортирован в алфавитном порядке по названию модуля, к которому относится данное API.

## Страницы API - запросы и ответы

### Модуль Cradle

**Получение основной информации о cradle**
- URL: `http://192.168.8.1/api/cradle/basic-info`
- Метод: GET
- Запрос: отсутствует
- Ответ:
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

**Настройка cradle**
- URL: `http://192.168.8.1/api/cradle/basic-info`
- Метод: POST
- Запрос:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <poecat>3</poecat>
    <multipmcat>0</multipmcat>
    <eth_wan_mode>0</eth_wan_mode>
</request>
```
- Ответ: `OK`

### Модуль DDNS

**Получение настроек DDNS**
- URL: `http://192.168.8.1/api/ddns/ddns-list`
- Метод: GET
- Запрос: отсутствует
- Ответ:
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

**Настройка DDNS**
- URL: `http://192.168.8.1/api/ddns/ddns-list`
- Метод: POST
- Запрос:
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
- Ответ: `OK`

### Модуль Device

**Проверка доступности новой версии**
- URL: `http://192.168.8.1/api/device/autorun-version`
- Метод: GET
- Запрос: отсутствует
- Ответ:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <Version>11.0.2.1(H192SP2C1025)</Version>
</response>
```

**Получение основной информации об устройстве**
- URL: `http://192.168.8.1/api/device/information`
- Метод: GET
- Запрос: отсутствует
- Ответ:
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

**Управление устройством (перезагрузка/выключение)**
- URL: `http://192.168.8.1/api/device/control`
- Метод: POST
- Запрос:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Control>1</Control>
</request>
```
- Ответ: `OK`
- Примечания: Значения Control: 1=перезагрузка, 4=выключение

### Модуль DHCP

**Получение настроек DHCP**
- URL: `http://192.168.8.1/api/dhcp/settings`
- Метод: GET
- Запрос: отсутствует
- Ответ:
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

**Настройка DHCP**
- URL: `http://192.168.8.1/api/dhcp/settings`
- Метод: POST
- Запрос:
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
- Ответ: `OK`

### Модуль Diagnosis

**Команда PING**
- URL: `http://192.168.8.1/api/diagnosis/ping`
- Метод: POST
- Запрос:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <host>8.8.8.8</host>
    <timeout>4</timeout>
    <count>4</count>
</request>
```
- Ответ:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <result>success</result>
</response>
```

### Модуль Dialup

**Получение статуса соединения**
- URL: `http://192.168.8.1/api/dialup/connection`
- Метод: GET
- Запрос: отсутствует
- Ответ:
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

**Подключение/Отключение**
- URL: `http://192.168.8.1/api/dialup/dial`
- Метод: POST
- Запрос:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Action>1</Action>
</request>
```
- Ответ: `OK`
- Примечания: Значения Action: 1=подключить, 0=отключить

### Модуль Filemanager

**Загрузка файла на устройство**
- URL: `http://192.168.8.1/api/filemanager/upload`
- Метод: POST
- Запрос: ?
- Ответ: ?
- Примечания: Самое загадочное API. Кроме адреса, ничего не известно. Вероятно, работает аналогично столь же загадочному API api/sdcard/fileupload, за исключением того, что, вероятно, загружает файлы в основную директорию устройства.

### Модуль Global

**Получение информации о состоянии модулей**
- URL: `http://192.168.8.1/api/global/module-switch`
- Метод: GET
- Запрос: отсутствует
- Ответ:
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

### Модуль Monitoring

**Получение статуса**
- URL: `http://192.168.8.1/api/monitoring/status`
- Метод: GET
- Запрос: отсутствует
- Ответ:
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

**Получение статистики трафика**
- URL: `http://192.168.8.1/api/monitoring/traffic-statistics`
- Метод: GET
- Запрос: отсутствует
- Ответ:
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

### Модуль SMS

**Получение количества SMS**
- URL: `http://192.168.8.1/api/sms/sms-count`
- Метод: GET
- Запрос: отсутствует
- Ответ:
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

**Получение списка SMS**
- URL: `http://192.168.8.1/api/sms/sms-list`
- Метод: POST
- Запрос:
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
- Ответ:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <Count>5</Count>
    <Messages>
        <Message>
            <Smstat>3</Smstat>
            <Index>40000</Index>
            <Phone>+48500500500</Phone>
            <Content>Содержимое тестового сообщения</Content>
            <Date>2023-01-01 12:00:00</Date>
            <Sca></Sca>
            <SaveType>3</SaveType>
            <Priority>0</Priority>
            <SmsType>1</SmsType>
        </Message>
    </Messages>
</response>
```

**Отправка SMS**
- URL: `http://192.168.8.1/api/sms/send-sms`
- Метод: POST
- Запрос:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
    <Index>-1</Index>
    <Phones>
        <Phone>+48500500500</Phone>
    </Phones>
    <Sca></Sca>
    <Content>Содержимое тестового сообщения</Content>
    <Length>35</Length>
    <Reserved>1</Reserved>
    <Date>2023-01-01 12:00:00</Date>
</request>
```
- Ответ: `OK`

### Модуль WLAN

**Получение основных настроек WLAN**
- URL: `http://192.168.8.1/api/wlan/basic-settings`
- Метод: GET
- Запрос: отсутствует
- Ответ:
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

**Настройка основных параметров WLAN**
- URL: `http://192.168.8.1/api/wlan/basic-settings`
- Метод: POST
- Запрос:
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
- Ответ: `OK`

**Получение настроек безопасности**
- URL: `http://192.168.8.1/api/wlan/security-settings`
- Метод: GET
- Запрос: отсутствует
- Ответ:
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

## Использование API

API HiLink можно легко использовать в Linux. Как в виде команды, введенной в командной строке, так и в виде скрипта. В Windows это становится немного сложнее. Вы можете упростить задачу, загрузив исполняемые файлы curl (см. [страницу проекта cURL](http://curl.haxx.se/download.html)) и пакет инструментов командной строки Linux для Windows (например, [UnxUtils](http://unxutils.sourceforge.net/)), из которого стоит получить grep и cut.

Ниже приведены несколько примеров, протестированных на OpenWRT Barrier Breaker (r43618) с помощью команды curl (думаю, их можно адаптировать для команды wget без проблем), а также примеры пакетных файлов для Windows.

### Пример 1a - Перезагрузка модема - командная строка OpenWRT
```bash
curl -X POST http://192.168.8.1/api/device/control \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>"
```

### Пример 1b - Перезагрузка модема - пакетный файл Windows
```batch
@echo off
curl -X POST http://192.168.8.1/api/device/control ^
     -H "Content-Type: application/xml" ^
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Control>1</Control></request>"
```

### Пример 2 - Получение статуса соединения
```bash
curl -X GET http://192.168.8.1/api/monitoring/status
```

### Пример 3 - Отправка SMS
```bash
curl -X POST http://192.168.8.1/api/sms/send-sms \
     -H "Content-Type: application/xml" \
     -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?><request><Index>-1</Index><Phones><Phone>+48500500500</Phone></Phones><Sca></Sca><Content>Тестовое сообщение</Content><Length>20</Length><Reserved>1</Reserved><Date>$(date '+%Y-%m-%d %H:%M:%S')</Date></request>"
```

## Номера ошибок

Распространенные коды ошибок, возвращаемые API:

- **100001**: Нет поддержки
- **100002**: Недоступно
- **100003**: Не поддерживается
- **100004**: Система занята
- **100005**: Неизвестная ошибка
- **100006**: Неверный параметр
- **100007**: Ошибка записи
- **100008**: Неправильный пароль
- **100009**: Не выполнен вход
- **103002**: Неизвестная ошибка
- **103015**: Неизвестная ошибка
- **108001**: Неверное имя пользователя
- **108002**: Неверный пароль
- **108003**: Пользователь уже вошел в систему
- **108006**: Неверное имя пользователя или пароль
- **108007**: Слишком много пользователей вошли в систему
- **109001**: Низкий заряд резервной батареи
- **109002**: Выключение резервной батареи
- **109003**: RTC недоступен
- **109004**: Ошибка издания
- **109005**: Неправильные PIN-коды
- **109006**: Достигнут месячный лимит
- **109007**: Занято
- **109012**: Невозможно удалить профиль по умолчанию
- **110002**: Модем не подключен к ПК
- **111019**: Неизвестная ошибка
- **111020**: Устройство в спящем режиме и может быть разбужено SMS или звонком
- **111021**: Неизвестная ошибка
- **111022**: Устройство в режиме низкого энергопотребления
- **112001**: Неправильный PIN
- **112002**: Требуется PUK
- **112003**: PUK заблокирован
- **112004**: Аутентификация PUK не удалась
- **112005**: PUK заблокирован навсегда
- **112006**: Карта заблокирована
- **113018**: Недостаточно места
- **114001**: Неверный номер
- **114002**: Не удалось отправить SMS
- **114003**: Неверный номер телефона
- **114004**: Пустое содержимое SMS
- **114005**: База данных SMS полна
- **114006**: SMS уже отправлено
- **115001**: Неизвестная ошибка SMS
- **117001**: Неизвестная ошибка
- **117004**: Неправильный пароль WiFi
- **125001**: Неверный токен

## Прочее

### Модификация файлов WebUI

**ВНИМАНИЕ**: Процедура, описанная ниже, требует устройства с разблокированным доступом через ADB и/или telnet.
**ВНИМАНИЕ**: Процедура, описанная ниже, вероятно, работает только на модеме E3372 с разблокированным ADB (я не могу проверить на других устройствах).

Сначала из командной строки мы подключаемся к модему с помощью ADB и запускаем shell:

```bash
adb shell
```

Затем переходим в директорию, содержащую файлы WebUI:

```bash
cd /app/hilink/config
```

Редактируем конфигурационный файл (например, для разблокировки польского языка):

```bash
vi system.xml
```

Находим раздел с настройками языка и соответственно модифицируем.

### Автоматический ввод PIN

Для автоматического ввода PIN при запуске модема можно модифицировать скрипт autorun.sh:

```bash
adb shell
cd /system/bin
vi autorun.sh
```

Добавляем команды ввода PIN в скрипт.

### Список страниц конфигурации HiLink

Следующие страницы конфигурации доступны в устройствах HiLink:

- http://192.168.8.1/index.html
- http://192.168.8.1/home.html
- http://192.168.8.1/statistic.html
- http://192.168.8.1/sms.html
- http://192.168.8.1/phonebook.html
- http://192.168.8.1/ussd.html
- http://192.168.8.1/settings.html
- http://192.168.8.1/security.html
- http://192.168.8.1/help.html

### Список файлов JavaScript

Ключевые файлы JavaScript, используемые WebUI:

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

## Источники знаний

- Основным источником знаний является само устройство, а именно JavaScripts, содержащиеся на данных страницах конфигурации модема
- [Programming and Installing HUAWEI HiLink E3131 under Linux](https://chaddyhv.wordpress.com/2012/08/13/programming-and-installing-huawei-hilink-e3131-under-linux/) - страница, с которой я собрал довольно много информации и создал это руководство аналогичным образом
- [BlackyPanther/Huawei-HiLink](https://github.com/BlackyPanther/Huawei-HiLink) - PHP класс, использующий преимущества API HiLink
- [Forum 4PDA](http://4pda.ru/forum/index.php) - а именно темы, посвященные обсуждению данного устройства и его программного обеспечения