---
title: Использования реального устройства с пакетом SDK для шлюза | Microsoft Docs
description: Пошаговое руководство по работе с пакетом SDK для шлюза центра Azure IoT с использованием устройства Texas Instruments SensorTag для отправки данных в центр IoT через шлюз, работающий на вычислительном модуле Intel Edison
services: iot-hub
documentationcenter: ''
author: chipalost
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2016
ms.author: andbuc

---
# <a name="iot-gateway-sdk-(beta)-–-send-device-to-cloud-messages-with-a-real-device-using-linux"></a>Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через реальное устройство с ОС Linux
В этом пошаговом руководстве по использованию [примера устройства Bluetooth с низким энергопотреблением][lnk-ble-samplecode] показано, как использовать [пакет SDK для шлюза Интернета вещей Microsoft Azure][lnk-sdk] для пересылки данных телеметрии, передаваемых с устройства в облако, с физических устройств в Центр Интернета вещей и как маршрутизировать команды из Центра Интернета вещей на физические устройства.

В этом руководстве рассматриваются следующие темы.

* **Архитектура**: важные данные об архитектуре в примере устройства Bluetooth с низким энергопотреблением.
* **Сборка и запуск**: шаги, необходимые для сборки и запуска примера.

## <a name="architecture"></a>Архитектура
В пошаговом руководстве показано, как выполнять сборку и запускать шлюз IoT на вычислительном модуле Intel Edison, работающем под управлением ОС Linux. Шлюз собран с использованием пакета SDK для шлюза IoT. Для сбора данных температуры в этом примере используется устройство Bluetooth с низким энергопотреблением (BLE) Texas Instruments SensorTag.

При запуске шлюз выполняет следующие действия:

* Подключается к устройству SensorTag с помощью протокола низкого энергопотребления Bluetooth (BLE).
* Подключается к центру IoT с помощью протокола AMQP.
* Пересылает данные телеметрии с устройства SensorTag в центр IoT.
* Маршрутизирует команды из центра IoT на устройство SensorTag.

Шлюз содержит следующие модули:

* *Модуль BLE* , который взаимодействует с устройством BLE для получения от него данных температуры и отправки на него команд.
* *Модуль BLE "Из облака на устройство"*, который преобразовывает сообщения JSON, поступающие из облака, в инструкции BLE для *модуля BLE*.
* *Модуль ведения журнала* , который регистрирует в журнале все сообщения шлюза.
* *Модуль сопоставления удостоверений* , который преобразовывает MAC-адреса устройств BLE и удостоверения устройств центра Azure IoT.
* *Модуль Центра Интернета вещей* , который передает в Центр Интернета вещей данные телеметрии и получает из него команды устройств.
* *Модуль принтера BLE* интерпретирует данные телеметрии из устройства BLE и печатает форматированные данные в консоль, чтобы включить устранение неполадок и отладку.

### <a name="how-data-flows-through-the-gateway"></a>Как происходит поток данных через шлюз
На следующей блок-схеме показано, как работает конвейер отправки данных телеметрии.

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

Элемент телеметрии на пути из устройства BLE в Центр Интернета вещей выполняет следующие шаги:

1. Устройство BLE создает пример данных температуры и отправляет его через Bluetooth на модуль BLE в шлюзе.
2. Модуль BLE получает этот пример и публикует его в брокер вместе с MAC-адресом устройства.
3. Модуль сопоставления удостоверений извлекает это сообщение и использует внутреннюю таблицу для преобразования MAC-адреса устройства в удостоверение устройства Центра Интернета вещей (идентификатор и ключ устройства). Затем он публикует новое сообщение, содержащее пример данных температуры, MAC-адрес, идентификатор и ключ устройства.
4. Модуль Центра Интернета вещей получает это новое сообщение (созданное модулем сопоставления удостоверений) и публикует его в Центре Интернета вещей.
5. Модуль ведения журнала регистрирует в журнале все сообщения из брокера в файл на диске.

На следующей блок-схеме показано, как работает конвейер обработки данных команд устройств.

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. Модуль Центра Интернета вещей периодически опрашивает Центр Интернета вещей на наличие новых командных сообщений.
2. Когда модуль Центра Интернета вещей получает новое командное сообщение, он публикует его в брокер.
3. Модуль сопоставления удостоверений извлекает это командное сообщение и использует внутреннюю таблицу для преобразования идентификатора устройства Центра Интернета вещей в MAC-адрес устройства. Затем он публикует новое сообщение, содержащее MAC-адрес целевого устройства в схеме сопоставления свойств сообщения.
4. Модуль BLE "Из облака на устройство" извлекает это сообщение и преобразовывает его в правильную инструкцию BLE для модуля BLE. Затем он публикует новое сообщение.
5. Модуль BLE извлекает это сообщение и выполняет инструкцию ввода-вывода, взаимодействуя с устройством BLE.
6. Модуль ведения журнала регистрирует в журнале все сообщения из брокера в файл на диске.

## <a name="prepare-your-hardware"></a>Подготовка оборудования
В этом учебнике предполагается, что вы используете устройство [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) , подключенное к плате Intel Edison.

### <a name="set-up-the-edison-board"></a>Настройка платы Edison
Перед началом выполнения описанных ниже действий убедитесь, что устройство Edison может подключаться к беспроводной сети. Чтобы настроить устройство Edison, его необходимо подключить к главному компьютеру. Intel предоставляет руководства по началу работы для следующих операционных систем:

* [Get Started with the Intel Edison Development Board on Windows 64-bit][lnk-setup-win64] (Начало работы с макетной платой Intel Edison на 64-разрядной версии Windows).
* [Get Started with the Intel Edison Development Board on Windows 32-bit][lnk-setup-win32] (Начало работы с макетной платой Intel Edison на 32-разрядной версии Windows).
* [Get Started with the Intel Edison Development Board on Mac OS X][lnk-setup-osx] (Начало работы с макетной платой Intel Edison на Mac OS X).
* [Getting Started with the Intel® Edison Board on Linux][lnk-setup-linux] (Начало работы с платой Intel® Edison на ОС Linux).

Чтобы настроить устройство Edison и ознакомиться с ним, выполните все шаги, описанные в этих статьях по началу работы, кроме последнего шага ("Выбор IDE"), который не требуется для данного учебника. В процессе настройки устройства Edison должны быть выполнены следующие действия:

* На устройство Edison установлена последняя версия встроенного ПО.
* Установлено последовательное соединение между главным компьютером и устройством Edison.
* Выполнен сценарий **configure_edison**, чтобы задать пароль и включить Wi-Fi на устройстве Edison.

### <a name="enable-connectivity-to-the-sensortag-device-from-your-edison-board"></a>Подключение к устройству SensorTag с платы Edison
Перед запуском примера необходимо убедиться, что плата Edison имеет возможность подключаться к устройству SensorTag.

Сначала необходимо убедиться, что плата Edison может подключиться к устройству SensorTag.

1. Разблокируйте Bluetooth на устройстве Edison и убедитесь, что номер версии — **5.37**.
   
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```
2. Выполните команду **bluetoothctl** . Теперь вы находитесь в интерактивной оболочке Bluetooth. 
3. Введите команду **power on** , чтобы включить питание контроллера Bluetooth. Должен отобразиться примерно такой результат:
   
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```
4. Оставаясь в интерактивной оболочке Bluetooth, введите команду **scan on** для поиска устройств Bluetooth. Должен отобразиться примерно такой результат:
   
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```
5. Сделайте устройство SensorTag доступным для обнаружения, нажав маленькую кнопку (должен загореться зеленый светодиодный индикатор). Плата Edison должна обнаружить устройство SensorTag:
   
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
   
    В этом примере можно увидеть, что устройство SensorTag имеет MAC-адрес **A0:E6:F8:B5:F6:00**.
6. Отключите поиск, введя команду **scan off** .
   
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```
7. Подключитесь к устройству SensorTag с помощью его MAC-адреса, введя команду **connect <MAC address>**. Обратите внимание, что приведенный ниже пример выходных данных является сокращенным:
   
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
   
    Примечание. Список характеристик GATT устройства можно получить повторно, используя команду **list-attributes**.
8. Теперь можно отключиться от устройства с помощью команды **disconnect** и выйти из оболочки Bluetooth с помощью команды **quit**:
   
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Теперь все готово для запуска примера шлюза BLE на устройстве Edison.

## <a name="run-the-ble-gateway-sample"></a>Запуск примера шлюза BLE
Чтобы запустить пример шлюза BLE на устройстве Edison, необходимо выполнить три задачи:

* Настроить два примера устройств в центре IoT.
* Выполнить сборку пакета SDK для шлюза на устройстве Edison.
* Настроить и запустить пример шлюза BLE на устройстве Edison.

На момент написания данной статьи пакет SDK для шлюза поддерживал только шлюзы, использующие модули BLE на ОС Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Настройка двух примеров устройств в центре IoT
* [Создайте Центр Интернета вещей][lnk-create-hub] в подписке Azure (для выполнения указаний данного пошагового руководства необходимо имя центра). Если у вас еще нет подписки Azure, получите [бесплатную учетную запись][lnk-free-trial].
* Добавьте в Центр Интернета вещей одно устройство с именем **SensorTag_01**, а затем запишите его идентификатор и ключ устройства. Чтобы добавить устройство в Центр Интернета вещей, созданный на предыдущем шаге, и получить его ключ, можно использовать такие средства, как [обозреватель устройств или iothub-explorer][lnk-explorer-tools]. Сопоставление этого устройства с устройством SensorTag будет выполнено при настройке шлюза.

### <a name="build-the-gateway-sdk-on-your-edison-device"></a>Выполнение сборки пакета SDK для шлюза на устройстве Edison
Версия **git** на устройстве Edsion не поддерживает подмодули. Существует два варианта скачивания на устройство Edison полного исходного кода для пакета SDK для шлюза.

* Вариант 1. Вы можете клонировать репозиторий с [пакетом SDK для шлюза Интернета вещей Microsoft Azure][lnk-sdk] на устройстве Edison, а затем вручную клонировать репозиторий для каждого подмодуля.
* Вариант 2. Вы можете клонировать репозиторий с [пакетом SDK для шлюза Интернета вещей Microsoft Azure][lnk-sdk] на настольное устройство, на котором **git** поддерживает подмодули, а затем скопировать полный репозиторий с подмодулями на устройство Edison.

Если вы выбрали второй вариант, используйте следующие команды **git** , чтобы клонировать пакет SDK для шлюза и все его подмодули:

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

Весь локальный репозиторий следует запаковать в один файл архива, прежде чем копировать его на устройство Edison. Скопировать файл архива на устройство Edison можно с помощью служебной программы, например **pscp** (включена в **Putty**). Например:

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Когда репозиторий пакета SDK для шлюза полностью скопирован на устройство Edison, можно выполнить его сборку, используя следующую команду из папки, в которой содержится пакет SDK:

```
./tools/build.sh
```

### <a name="configure-and-run-the-ble-sample-on-your-edison-device"></a>Настройка и запуск примера BLE на устройстве Edison
Для начальной загрузки и запуска примера необходимо настроить каждый модуль, который участвует в шлюзе. Необходимая конфигурация предоставляется в файле JSON, и требуется настроить все пять участвующих модулей. В репозитории есть пример JSON-файла с именем **gateway_sample.json**, на основе которого вы можете создать собственный файл конфигурации. Этот файл находится в папке **samples/ble_gateway_hl/src** в локальной копии репозитория с пакетом SDK для шлюза.

В следующих разделах описывается, как изменить этот файл конфигурации для примера BLE. Кроме того, предполагается, что репозиторий с пакетом SDK для шлюза находится в папке **/home/root/azure-iot-gateway-sdk/** на устройстве Edison. Если репозиторий находится в другом месте, необходимо соответствующим образом изменить пути.

#### <a name="logger-configuration"></a>Конфигурация средства ведения журнала
Убедившись, что репозиторий шлюза находится в папке **/home/root/azure-iot-gateway-sdk/**, настройте модуль ведения журнала следующим образом:

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### <a name="ble-module-configuration"></a>Конфигурация модуля BLE
В примере конфигурации для устройства BLE предполагается, что используется устройство Texas Instruments SensorTag. Любое стандартное устройство BLE, которое может функционировать как периферийное устройство GATT, должно работать, но для этого необходимо обновить идентификаторы характеристик и данные GATT (для инструкций записи). Добавьте MAC-адрес устройства SensorTag: 

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>Модуль Центра Интернета вещей
Добавьте имя центра IoT. Значением суффикса обычно является **azure-devices.net**:

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothub/libiothub_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport": "HTTP"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Конфигурация модуля сопоставления удостоверений
Добавьте MAC-адрес устройства SensorTag, а также идентификатор и ключ устройства **SensorTag_01**, которое вы добавили в Центр Интернета вещей:

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Конфигурация модуля принтера BLE
```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

#### <a name="routing-configuration"></a>Конфигурация маршрутизации
Следующие параметры конфигурации гарантируют выполнение таких действий:

* Модуль **ведения журнала** получает и регистрирует все сообщения.
* Модуль **SensorTag** отправляет сообщения в модуль **mapping** и модуль **принтера BLE**.
* Модуль **mapping** отправляет в модуль **IoTHub** сообщения, которые затем будут отправляться в ваш Центр Интернета вещей.
* Модуль **IoTHub** отправляет сообщения обратно в модуль **mapping**.
* Модуль **mapping** отправляет сообщения обратно в модуль **SensorTag**.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "SensorTag" }
  ]
```

Для запуска примера выполните двоичный файл **ble_gateway_hl**, передав путь к файлу конфигурации JSON. Если использовался файл **gateway_sample.json**, выполняемая команда будет выглядеть так:

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Перед запуском примера может потребоваться нажать маленькую кнопку на устройстве SensorTag, чтобы сделать его доступным для обнаружения.

При запуске примера вы можете использовать [обозреватель устройств или iothub-explorer][lnk-explorer-tools] для мониторинга сообщений, которые шлюз перенаправляет с устройства SensorTag.

## <a name="send-cloud-to-device-messages"></a>Отправка сообщений из облака на устройство
Модуль BLE также поддерживает отправку инструкции из центра Azure IoT на устройство. Для отправки сообщений JSON, которые модуль шлюза BLE передает на устройство BLE, можно использовать [обозреватель устройств Центра Интернета вещей Azure](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) или [обозреватель Центра Интернета вещей](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer). Например, при использовании устройства Texas Instruments SensorTag можно отправлять из центра IoT на устройство следующие сообщения JSON.

* Сбросить все светодиодные индикаторы и звуковые сигналы (отключить их):
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```
* Настроить операции ввода-вывода как "удаленные":
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* Включить красный светодиодный индикатор:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* Включить зеленый светодиодный индикатор:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```
* Включить звуковой сигнал:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

По умолчанию устройство, использующее протокол HTTP для подключения к центру IoT, будет каждые 25 минут проверять наличие новой команды. Таким образом, если вы отправляете несколько отдельных команд, необходимо подождать 25 минут, чтобы устройство получило каждую команду.

> [!NOTE]
> Шлюз также проверяет наличие новых команд при каждом запуске, поэтому для принудительной обработки команды можно остановить и снова запустить шлюз.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Если вы хотите подробнее изучить возможности пакета SDK для шлюза и поэкспериментировать с примерами кода, см. следующие руководства и ресурсы для разработчиков.

* [Azure IoT Gateway SDK][lnk-sdk] (Пакет SDK для шлюза Интернета вещей Azure)

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Developer guide][lnk-devguide] (Руководство разработчика)

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 


<!--HONumber=Oct16_HO2-->


