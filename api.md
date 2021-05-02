# API для управления протезом
Для управления протезом используется Bluetooth интерфейс с самописным протоколом и Ethernet интерфейс с протоколом MQTT. Payload для протоколов общий и представляет собой структуры данных, описанные и сериализованные с помощью [Protocol Buffers](https://developers.google.com/protocol-buffers). Сообщения protocol buffers находится в [`репозитории arm_prosthesis_raspberry, директория proto`](https://github.com/paulrozhkin/arm_prosthesis_raspberry/tree/main/proto). Дальнейшее описание будет представлять из себя описание API в виде таблицы и общий раздел Payload с описанием сообщений Protobuf.

## API
В тиблцев приведено описание API протеза со следующими полями:
* Name - название предоставляемой команды
* MQTT Topic - MQTT топик, который используется для команды
* MQTT Write - может ли кто-либо, кроме протеза (и эмулятора) отправлять сообщения в этот топик
* Payload - идентификатор полезной нагрузки (см. раздел Payload)
* Description - описание команды

|Name|MQTT Topic|MQTT Write|Payload|Description|
|---|---|---|---|---|
|GetOnline|controllers/online|-|Online/Offline|Каждый протез отправляет свой id в этот топик каждые 60 секунд для определения того, что он находится в системе и к нему можно получить доступ. Если протез не отправил данные, то можно считать, что он отключен.|
|GetOffline|controllers/offline|-|Online/Offline|При обрыве связи с протезом брокер публикует его id (механизм Last Will у MQTT).|
|Telemetry|{id}/data/telemetry|-|Telemetry|Телеметрия протеза. Содержит актульную информацию о системе. Обновляется при подписке на телеметрию через **StartTelemetry**.|
|GetSettings|{id}/data/settings|-|GetSettings|Топик хранит текущую конфигурацию протеза.|
|SetSettings|{id}/action/settings|+|SetSettings|Выполнить конфигурацию протеза. После успешного завершения данные в топике **GetSettings** будут обновлены, а в телеметрии будет слаться новое состояния.|
|GetGestures|{id}/data/gestures|-|GetGestures|Актуальные жесты, хранимые на протезе.|
|SaveGesture|{id}/action/gestures|+|SaveGesture|Выполнить сохранение/обновление жеста протеза. После успешного завершения данные в топике **GetGestures** будут обновлены, а в телеметрии будет слаться новое время синхронизации.|
|DeleteGesture|{id}/action/gestures/remove|+|DeleteGesture|Выполнить удаление жеста протеза. После успешного завершения данные в топике **GetGestures** будут обновлены, а в телеметрии будет слаться новое время синхронизации.|
|PerformGestureId|{id}/action/performGestureId|+|PerformGestureId|Выполнить жест по id. В телеметрии в поле Gesture будет установлено id жеста при успешном поиске по id.|
|PerformGestureRaw|{id}/action/performGestureRaw|+|PerformGestureRaw|Выполнить жест, который не сохранен в системе. В телеметрии в поле Gesture будет установлено id жеста, который был отправлен на исполнение.|
|SetPositions|{id}/action/positions|+|SetPositions|Установить положения пальцев протеза.|
## Payload

### Online/Offline
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|Id|Dynemic|string|Идентификатор протеза (MQTT Proxy) в системе в UTF-8. Не являтся Protobuf структурой, отправляетя в виде строки.|

### Telemetry
|Field|Description|
|---|---|
|emg_status|Состояние модуля Emg датчика|
|display_status|Состояние модуля дисплея|
|gyro_status|Состояние модуля гироскопа и акселерометра|
|driver_status|Состояние контроллера линейных приводов|
|last_time_sync|Время последней синхронизации жестов. В Unix времени|
|emg|Показания Emg датчика|
|executable_gesture|UUID исполняемого жеста.|
|power|Уровень заряда протеза в процентах (от 0 до 100%)|
|pointer_finger_position|Позиция указательного пальца|
|middle_finger_position|Позиция среднего пальца|
|ring_finger_position|Позиция безымянного пальца|
|little_finger_position|Позиция мезинца|
|thumb_finger_position|Позиция большого пальца|

### GetGestures
|Field|Description|
|---|---|
|last_time_sync|Время последней синхронизации жестов. В Unix времени|
|gestures|Список жестов, хранимых на протезе|

### SaveGesture
|Field|Description|
|---|---|
|time_sync|Текущее время. Будет использовано для идентификации последнего времеи синхронизации. В Unix времени|
|gesture|Жест, который будет сохранен, либо обновлен в системе, если id будет совпадать.|

### DeleteGesture
|Field|Description|
|---|---|
|time_sync|Текущее время. Будет использовано для идентификации последнего времеи синхронизации. В Unix времени|
|id|Id жеста, который будет удален из системы|

### SetSettings
|Field|Description|
|---|---|
|type_work|Текущий режим работы протеза|
|enable_emg|Программное отключение/подключение Emg датчика|
|enable_display|Программное отключение/подключение подключения дисплея|
|enable_gyro|Программное отключение/подключение гироскопа и акселерометра|
|enable_driver|Программное отключение/подключение драйвера линейных приводов|
|power_off|Выключить протез. При приходе true протез будет отключен.|

### GetSettings
|Field|Description|
|---|---|
|type_work|Текущий режим работы протеза|
|enable_emg|Программное отключение/подключение Emg датчика|
|enable_display|Программное отключение/подключение подключения дисплея|
|enable_gyro|Программное отключение/подключение гироскопа и акселерометра|
|enable_driver|Программное отключение/подключение драйвера линейных приводов|

### PerformGestureId
|Field|Description|
|---|---|
|id|UUID жеста, который должен быть исполнен.|

### PerformGestureRaw
|Field|Description|
|---|---|
|gesture|Жест, который будет исполняться на протезе.|

### SetPositions
|Field|Description|
|---|---|
|pointer_finger_position|Позиция указательного пальца|
|middle_finger_position|Позиция среднего пальца|
|ring_finger_position|Позиция безымянного пальца|
|little_finger_position|Позиция мезинца|
|thumb_finger_position|Позиция большого пальца|

### GetTelemetry
|Field|Description|
|---|---|
|telemetry|Телеметрия устройства|

### StartTelemetry
|Field|Description|
|---|---|
|interval_ms|Интервал отправки телеметрии устройством|

### UpdateLastTimeSync
|Field|Description|
|---|---|
|last_time_sync|Обновленное время синхронизации|

### GetMioPatterns
|Field|Description|
|---|---|
|patterns|Набор паттернов системы.|

### SetMioPatterns
|Field|Description|
|---|---|
|patterns|Обновления паттернов системы.|

## Payload typedef

### ModeType
|Field|Description|
|---|---|
|ModeType|MODE_MIO - Миоэлектрический режим работы протеза. В данном режиме выбор исполняемого жеста основывается только на миоэлектрических датчиках; MODE_COMMANDS - Режим прямого управления. В данном режиме выбор исполняемого жеста основывается на принимаемых командах с внешнего устройства; MODE_AUTO - Стандартный работы протеза. В данном режиме выбор исполняемого жеста основывается как на миоэлектрических датчиках, так и на принимаемых с внешнего устройства командах.|

### DriverStatusType
|Field|Description|
|---|---|
|DriverStatusType|DRIVER_STATUS_INITIALIZATION - Режим инициализации. В данном режиме происходит инициализация драйвера линейных приводов; DRIVER_STATUS_ERROR - Режим ошибки драйвера линейных приводов. Работа невозможна; DRIVER_STATUS_CONNECTION_ERROR - Ошибка соединения с драйвером линейных приводов; DRIVER_STATUS_DISABLED - Драйвер отключен программно; DRIVER_STATUS_SLEEP - Режим ожидания. Механические действия не исполняются; DRIVER_STATUS_SETTING_POSITION - Режим установки новых положений;|

### ModuleStatusType
|Field|Description|
|---|---|
|ModuleStatusType|MODULE_STATUS_INITIALIZATION - Режим инициализации. В данном режиме происходит инициализация модуля; MODULE_STATUS_WORK - Модуль находится в рабочем режиме; MODULE_STATUS_ERROR- Режим ошибки модуля. Работа невозможна; MODULE_STATUS_CONNECTION_ERROR - Ошибка соединения с модулем; MODULE_STATUS_DISABLED - Модуль отключен программно.|

### Gesture
|Field|Description|
|---|---|
|id|Уникальный идентификатор жеста.|
|name|Имя жеста|
|last_time_sync|Время создания или последнего изменения. В Unix времени|
|iterable|Состояние итерируемости жеста (бесконечное повторение жеста)|
|repetitions|Количество повторений жеста|
|actions|Список действий жеста|

### GestureAction
|Field|Description|
|---|---|
|pointer_finger_position|Позиция указательного пальца|
|middle_finger_position|Позиция среднего пальца|
|ring_finger_position|Позиция безымянного пальца|
|little_finger_position|Позиция мезинца|
|thumb_finger_position|Позиция большого пальца|
|delay|Задержка до начала выполнения следующего действия жеста|

### MioPattern
|Field|Description|
|---|---|
|pattern|Идентификатор миоэлектрического паттерна. Жестко задан в системе.|
|gesture_id|Идентификатор жеста, который должен быть исполнен при активизации паттерна|
