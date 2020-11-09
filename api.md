# API для управления протезом
Для управления протезом используется Bluetooth интерфейс с самописным протоколом и Ethernet интерфейс с протоколом MQTT. Payload для протоколов общий и представляе собой данные в бинарном формате. Дальнейшее описание будет представлять из себя описание API в виде таблицы и общий раздел Payload для представление бинарного формата.

## API
В тиблцев приведено описание API протеза со следующими полями:
* Name - название предоставляемой команды
* MQTT Topic - MQTT топик, который используется для команды
* MQTT Write - может ли кто-либо, кроме протеза (и эмулятора) отправлять сообщения в этот топик
* Payload - идентификатор полезной нагрузки (см. раздел Payload)
* Description - описание команды

|Name|MQTT Topic|MQTT Write|Payload|Description|
|---|---|---|---|---|
|SetOnline|controllers/online|-|Online/Offline|Каждый протез отправляет свой id в этот топик каждые 60 секунд для определения того, что он находится в системе и к нему можно получить доступ. Если протез не отправил данные, то можно считать, что он отключен.|
|Offline|controllers/offline|-|Online/Offline|При обрыве связи с MQTT Proxy брокер публикует id протеза с которым был обрыв связи (механизм Last Will у MQTT).|
|Telemetry|{id}/data/telemetry|-|Telemetry|Телеметрия протеза. Содержит актульную информацию о системе. Обновляется минимум раз в секунду.|
|GetSettings|{id}/data/settings|-|GetSettings|Получить конфигурацию протеза.|
|SetSettings|{id}/action/settings|+|SetSettings|Выполнить конфигурацию протеза. После успешного завершения данные в топике **GetSettings** будут обновлены, а в телеметрии будет слаться новое состояния.|
|GetGestures|{id}/data/gestures|-|GetGestures|Актуальные жесты, хранимые на протезе.|
|SaveGesture|{id}/action/gestures|+|SaveGesture|Выполнить сохранение/обновление жеста протеза. После успешного завершения данные в топике **GetGestures** будут обновлены, а в телеметрии будет слаться новое время синхронизации.|
|DeleteGesture|{id}/action/gestures/remove|+|DeleteGesture|Выполнить удаление жеста протеза. После успешного завершения данные в топике **GetGestures** будут обновлены, а в телеметрии будет слаться новое время синхронизации.|
|PerformGestureId|{id}/action/performGestureId|+|PerformGestureId|Выполнить жест по id. В телеметрии в поле Gesture будет установлено id жеста при успешном поиске по id.|
|PerformGestureRaw|{id}/action/performGestureRaw|+|PerformGestureRaw|Выполнить жест, который не сохранен в системе. В телеметрии в поле Gesture будет установлено значение [0x01].|
|SetPositions|{id}/action/positions|+|SetPositions|Установить положения пальцев протеза.|
## Payload

### Online/Offline
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|Id|Dynemic|string|Идентификатор протеза в системе в UTF-8|

### Telemetry
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|TelemetryFrequency|2|uint16_t|Частота отправки телеметрии (от 1Гц до 450Гц)|
|EmgStatus|1|ModuleTypeWorkEnum|Состояние модуля Emg датчика|
|DisplayStatus|1|ModuleTypeWorkEnum|Состояние модуля дисплея|
|GyroStatus|1|ModuleTypeWorkEnum|Состояние модуля гироскопа и акселерометра|
|DriverStatus|1|DriverTypeWorkEnum|Режим работы контроллера линейных приводов|
|LastTimeSync|8|long|Время последней синхронизации жестов. В Unix времени|
|EMG|2|uint16_t|Показания Emg датчика|
|Gesture|16|UUID|UUID исполняемого жеста. Если значение 0x00, то исполняемого жеста нет. Если значение 0x01, то исполняется жест, который не сохранен на протезе.|
|Power|1|uint8_t|Уровень заряда протеза в процентах (от 0 до 100%)|
|PointerFingerPosition|1|uint8_t|Позиция указательного пальца|
|MiddleFingerPosition|1|uint8_t|Позиция среднего пальца|
|RingFinderPosition|1|uint8_t|Позиция безымянного пальца|
|LittleFingerPosition|1|uint8_t|Позиция мезинца|
|ThumbFingerPosition|1|uint8_t|Позиция большого пальца|

### GetGestures
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|LastTimeSync|8|long|Время последней синхронизации жестов. В Unix времени|
|Count|2|uint16_t|Количество жестов|
|ListGestures|Dynemic|List<Gesture>|Список жестов, хранимых на протезе|

### SaveGesture
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|TimeSync|8|long|Текущее время. Будет использовано для идентификации последнего времеи синхронизации. В Unix времени|
|IsDelete|1|bool|Флаг необходимости удаления жеста. Должен быть false.|
|Gesture|Dynemic|Gesture|Жест, который будет сохранен, либо обновлен в системе, если id будет совпадать.|

### DeleteGesture
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|TimeSync|8|long|Текущее время. Будет использовано для идентификации последнего времеи синхронизации. В Unix времени|
|IsDelete|1|bool|Флаг необходимости удаления жеста. Должен быть true.|
|id|16|UUID|Id жеста, который будет удален из системы|

### SetSettings
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|TypeWork|1|TypeWorkEnum|Текущий режим работы протеза|
|TelemetryFrequency|2|uint16_t|Частота отправки телеметрии (от 1Гц до 450Гц)|
|EnableEmg|1|bool|Программное отключение/подключение Emg датчика|
|EnableDisplay|1|bool|Программное отключение/подключение подключения дисплея|
|EnableGyro|1|bool|Программное отключение/подключение гироскопа и акселерометра|
|EnableDriver|1|bool|Программное отключение/подключение драйвера линейных приводов|
|Enable|1|bool|Программное отключение/подключение гироскопа и акселерометра|

### GetSettings
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|TypeWork|1|TypeWorkEnum|Текущий режим работы протеза|
|EnableEmg|1|bool|Программное отключение/подключение Emg датчика|
|EnableDisplay|1|bool|Программное отключение/подключение подключения дисплея|
|EnableGyro|1|bool|Программное отключение/подключение гироскопа и акселерометра|
|EnableDriver|1|bool|Программное отключение/подключение драйвера линейных приводов|
|Enable|1|bool|Программное отключение/подключение гироскопа и акселерометра|

### PerformGestureId
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|Gesture|16|UUID|UUID жеста, который должен быть исполнен.|

### PerformGestureRaw
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|Gesture|Dynemic|GestureRaw|Жест, который будет исполняться на протезе.|

## Payload typedef
### StringFormat
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|Size|4|uint32_t|Количество байт в строке|
|String|Size|UTF-8|Строка UTF-8|

### TypeWorkEnum
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|Size|1|uint8_t|[0x00] - MIO - Миоэлектрический режим работы протеза. В данном режиме выбор исполняемого жеста основывается только на миоэлектрических датчиках; [0x01] - COMMANDS - Режим прямого управления. В данном режиме выбор исполняемого жеста основывается на принимаемых командах с внешнего устройства; [0x02] - AUTO - Стандартный работы протеза. В данном режиме выбор исполняемого жеста основывается как на миоэлектрических датчиках, так и на принимаемых с внешнего устройства командах.|

### DriverTypeWorkEnum
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|Size|1|uint8_t|[0x00] - InitializationMode - Режим инициализации. В данном режиме происходит инициализация драйвера линейных приводов; [0x02] - ErrorMode - Режим ошибки драйвера линейных приводов. Работа невозможна; [0x03] - ConnectionError - Ошибка соединения с драйвером линейных приводов; [0x05] - ModuleDisabled - Драйвер отключен программно; [0xF1] - SleepMode - Режим ожидания. Механические действия не исполняются; [0xF2] - SettingPositionMode - Режим установки новых положений;|

### ModuleTypeWorkEnum
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|Size|1|uint8_t|[0x00] - InitializationMode - Режим инициализации. В данном режиме происходит инициализация модуля; [0x01] - Work - Модуль находится в рабочем режиме; [0x02] - ErrorMode - Режим ошибки модуля. Работа невозможна; [0x03] - ConnectionError - Ошибка соединения с модулем; [0x04] - ModuleDisabled - Модуль отключен программно.|

### Gesture
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|ID|16|UUID|Уникальный идентификатор жеста.|
|String|Dynemic|StringFormat|Имя жеста|
|LastTimeSync|8|long|Время создания или последнего изменения. В Unix времени|
|IterableGesture|1|bool|Состояние итерируемости жеста (бесконечное повторение жеста)|
|NumberOfGestureRepetitions|1|uint8_t|Количество повторений жеста|
|NumberOfMotions|1|uint8_t|Кол-во действий в жесте|
|ListActions|NumberOfMotions * 7|List<GestureAction>|Список действий жеста|

### GestureRaw
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|IterableGesture|1|bool|Состояние итерируемости жеста (бесконечное повторение жеста)|
|NumberOfGestureRepetitions|1|uint8_t|Количество повторений жеста|
|NumberOfMotions|1|uint8_t|Кол-во действий в жесте|
|ListActions|NumberOfMotions * 7|List<GestureAction>|Список действий жеста|

### GestureAction
|Field|Size (bytes)|Data Type|Description|
|---|---|---|---|
|PointerFingerPosition|1|uint8_t|Позиция указательного пальца|
|MiddleFingerPosition|1|uint8_t|Позиция среднего пальца|
|RingFinderPosition|1|uint8_t|Позиция безымянного пальца|
|LittleFingerPosition|1|uint8_t|Позиция мезинца|
|ThumbFingerPosition|1|uint8_t|Позиция большого пальца|
|Delay|2|uint16_t|Задержка до начала выполнения следующего действия жеста|
