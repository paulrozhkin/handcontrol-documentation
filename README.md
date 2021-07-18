<a href="#">
    <img src="https://raw.githubusercontent.com/paulrozhkin/handcontrol-documentation/master/img/logo.jpg" title="Tamagotchi" alt="Tamagotchi" width="300">
</a>

# HandControl Documentation
Репозиторий содержит документация по проекту создания бионического протеза руки человека "HandControl" в рамках предмета ПОВС университета ИТМО, гранта УМНИК и магистерской диссертации. Дополнительную информацию по каждой подсистеме можно найти по ссылкам в разделе ***Linked Repositories***.

---

## Linked Repositories
Актульные репозитории:
- [Контроллер управления протезом на Raspberry Pi](https://github.com/paulrozhkin/arm_prosthesis_raspberry)
- [Контроллер управления линейными приводами на STM32F103C8T6](https://github.com/paulrozhkin/handcontrol-motor-controller)
- [Приложение для конфигурирования протеза для ПК на WPF](https://github.com/paulrozhkin/HandControlApplication)
- [Приложение для управления протезом для Android](https://github.com/ForsaiR/HandControlAndroidAplication)
- [Схемотехника драйвера моторов STM32F103C8T6 (Altium Designer)](https://github.com/paulrozhkin/DCDriverShematic)
- [Схемотехника контроллера управления Rasbperry Pi Zero W (Altium Designer)](https://github.com/paulrozhkin/raspberry-prosthesis-circuit)
- [Сервер для одновременной работы с несколькими протезами по MQTT на Kotlin](https://github.com/paulrozhkin/hand-control-mqtt)
- [MQTT proxy для контроллера управления протеза для STM32F767ZITX](https://github.com/paulrozhkin/handcontrol-mqtt-proxy)

Устаревшие:
- [Драйвер моторов на базе STM32F103C8T6 (old)](https://github.com/paulrozhkin/DriverMotorsCortexM3)
- [Просмотр показаний миоэлектричских датчиков](https://github.com/paulrozhkin/MyoSensor)
- [Контроллер управления на базе STM32F407VGTx](https://github.com/paulrozhkin/ArmProsthesisCortexM4)
- [Протез V1 (терминал для ПК)](https://github.com/paulrozhkin/RoboHandPythonOld)
- [Протез V2 (приложение для Android)](https://bitbucket.org/blackiiifox/handcontrolanroid/)
- [Протез V2 (контроллер управления)](https://bitbucket.org/blackiiifox/robohand/src/master/)
- [Схемотехника контроллера управления STM32F407 (Altium Designer)](https://github.com/paulrozhkin/ArmProsthesisShematic)
---

## Description
Каждый год в мире делают более 1 млн ампутаций. На США приходится более 185 тыс., на Россию — более 70 тыс. В современном мире оптимальным решением для таких людей будет бионический протез. Преимущество его перед остальными разновидностями протезов в том, что бионические протезы позволяют людям, оставшимся без ноги или руки, жить полноценной жизнью, а не просто иметь муляж конечности.

Данный проект представление аппратано-програмную систему бионического протеза руки человека.

Основными отличительными возможностями, характеризующими научную новизну аппаратно-программной системы, являются:
1)  Разработанная система хранения, передачи и исполнения жестов протеза, позволяющая абстрагироваться от реализуемой системы двигательных механизмов и устройств управления и осуществлять создания новых или изменение старых жестов протеза.
2)  Комбинированная система управления, основанная на взаимодействии миоэлектрических сенсоров, голосового управления и графического интерфейса, позволяющая существенно расширить набор жестов системы.

Аппаратно-программная система бионического протеза предназначена для людей с отсутствующей конечностью руки до локтевого сустава и может быть применена в области медицинской реабилитации верхних конечностей.

Модель протеза:

<a href="#">
    <img src="https://raw.githubusercontent.com/paulrozhkin/handcontrol-documentation/master/img/3d_model.png" title="3d model" alt="3d model" width="800">
</a>

Демонстрация работы:

https://user-images.githubusercontent.com/38138387/126069775-825ffeaa-66c6-4a8a-a21e-2c4df761032e.mp4

---

## Documentation
- [Диссертация с подробным описанием работы](https://raw.githubusercontent.com/paulrozhkin/handcontrol-documentation/master/resources/vak.pdf)
- [Техническое задание](https://docs.google.com/document/d/1KbQ16ehyuJt8YoWaM7xjhkE0ynpm1GKat7H1k4UXwzY/edit?usp=sharing)
- [MQTT API протеза](https://github.com/paulrozhkin/handcontrol-documentation/blob/master/api.md)
- [Bluetooth API протеза](https://github.com/paulrozhkin/handcontrol-documentation/blob/master/bluetooth_api.md)
- [Описание протокола взаимодействия драйвера моторов и управляющего контроллера](https://drive.google.com/file/d/1_azK8TsY6GFS11r03LjgoXlnlsHRx1bY/view?usp=sharing)
- [Структурная схема](https://drive.google.com/file/d/1gGquBem_VxTlNn12caVKcv3hVfcEDApx/view?usp=sharingg)
- [UseCase](https://drive.google.com/file/d/1P3Ixa92nB_NM5jHj8qfTBZ6zH1nHHI05/view?usp=sharing)

---

## License

[![License](http://img.shields.io/:license-mit-blue.svg?style=flat-square)](http://badges.mit-license.org)

- **[MIT license](http://opensource.org/licenses/mit-license.php)**
- Copyright 2020 © <a href="https://github.com/paulrozhkin" target="_blank">Paul Rozhkin</a>.
