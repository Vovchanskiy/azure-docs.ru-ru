---
title: 'Управление устройствами в центре IoT: задания устройств | Microsoft Docs'
description: В учебнике по использованию центра IoT Azure для управления устройствами описывается, как использовать задания устройств для выполнения таких операций, как удаленное обновление встроенного ПО.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2016
ms.author: juanpere

---
# Учебник по использованию заданий устройства для обновления встроенного ПО устройства (предварительная версия)
[!INCLUDE [iot-hub-device-management-job-selector](../../includes/iot-hub-device-management-jobs-selector.md)]

## Введение
Управление устройствами Azure IoT позволяет взаимодействовать с физическими устройствами, используя задания устройств. Определив "двойник" устройства (представление физического устройства в службе), вы сможете взаимодействовать с соответствующим физическим устройством, используя задания. Задания устройства позволяют координировать сложные процессы на нескольких устройствах. Этот процесс может включать несколько действий и длительные операции.

В настоящее время в управлении устройствами Azure IoT Hub используются шесть типов заданий устройств (при необходимости мы предоставим клиентам дополнительные задания):

* **Обновление встроенного ПО**: обновляет встроенное ПО (или образ операционной системы) на физическом устройстве.
* **Перезагрузка**: перезагружает физическое устройство.
* **Сброс к параметрам по умолчанию**: восстанавливает встроенное ПО (или образ операционной системы) физического устройства до архивного образа, сохраненного на устройстве производителем.
* **Обновление конфигурации**: настраивает для центра IoT агент клиента, запущенный на физическом устройстве.
* **Чтение свойства устройства**: возвращает актуальное значение свойства для физического устройства.
* **Запись свойства устройства:** изменяет свойство физического устройства.

Дополнительные сведения об использовании каждого из этих заданий см. в [документации по API][lnk-apidocs].

Запрос истории заданий позволяет определить состояние запущенных заданий. Некоторые примеры запросов см. в [нашей библиотеке выражений запросов][lnk-query-samples].

## Использование заданий устройств для обновления встроенного ПО: архитектура
На следующей схеме показано задание устройства для обновления встроенного ПО, взаимодействующее с одним физическим устройством.

![][img-architecture]

Ход выполнения задания устройства для обновления встроенного ПО:

1. Облачное решение IoT запускает задание устройства для обновления встроенного ПО и предоставляет URI расположения пакета встроенного ПО. Решение IoT должно разместить пакет встроенного ПО там, откуда устройство сможет его загрузить.
2. Получив URI, физическое устройство автоматически начинает загрузку из указанного расположения.
3. Код на устройстве получает запрос от центра IoT и загружает пакет встроенного ПО из расположения, указанного URI.
4. Получив сообщение о завершении загрузки, задание устройства дает устройству команду о применении загруженного образа встроенного ПО.
5. После применения образа встроенного ПО устройство перезагружается, восстанавливает подключение к центру IoT и сообщает центру IoT об установке нового встроенного ПО; на этом задание устройства завершается.

## Пример обновления встроенного ПО
Следующий пример дополняет учебник [Приступая к работе с функцией управления устройствами центра IoT Azure][lnk-get-started]. Он предназначен для работы с другими виртуальными устройствами и запускает задание для обновления встроенного ПО на всех таких устройствах.

### Предварительные требования
Перед запуском этого примера необходимо выполнить действия, описанные в статье [Приступая к работе с функцией управления устройствами центра IoT Azure][lnk-get-started]. Это означает, что виртуальные устройства должны быть запущены. Если эта процедура уже выполнялась, виртуальные устройства необходимо перезапустить.

### Запуск примера
Чтобы запустить пример, необходимо выполнить процесс **FirmwareUpdate.exe**. При этом на всех виртуальных устройствах запускается процесс обновления встроенного ПО. Чтобы запустить пример, выполните следующие действия.

1. В корневой папке, куда был клонирован репозиторий **azure-iot-sdks**, перейдите к папке **azure-iot-sdks\\csharp\\service\\samples\\bin**.
2. Запустите `FirmwareUpdate.exe <IoT Hub Connection String>`

В окне командной строки появятся семь заданий устройств, отслеживающих обновление встроенного ПО на шести виртуальных устройствах.

### Запуск задания устройства
Как правило, задания устройств запускаются с помощью ряда методов **Schedule&lt;JobName&gt;Async** в экземпляре **JobClient**. В примере обновления встроенного ПО мы вызываем метод **ScheduleFirmwareUpdateAsync**. Поскольку мы передаем массив с шестью кодами устройств, запускаются семь заданий устройств — по одному на каждое обновляемое устройство плюс родительское задание устройства, которое отслеживает остальные шесть заданий.

В приведенном ниже фрагменте кода из файла **Program.cs** проекта **FirmwareUpdate** запускается задание обновления встроенного ПО. Вызов принимает в качестве параметра массив строк со значениями **deviceId**, указывающими, какие устройства нужно обновить.

```
var jobResponse = await deviceJobClient.ScheduleFirmwareUpdateAsync(Guid.NewGuid().ToString(), deviceIds, packageURI, TimeSpan.FromMinutes(25));
```

### Запросы к журналу заданий
В этом примере периодически отображается список устройств, на которых активно выполняется задание устройства. Как только задание устройства завершается, соответствующее устройство исчезает из отображаемого списка. На следующем рисунке показан снимок экрана в процессе выполнения **FirmwareUpdate.exe**.

![][img-output1]

Представленный выше список формируется в результате запроса всех активных заданий, как видно из следующего фрагмента кода, взятого из файла **Program.cs** проекта **FirmwareUpdate**.

```
private static async Task OutputRunningJobs()
{
  JobClient deviceJobClient = JobClient.CreateFromConnectionString(connectionString);

  string json = JsonConvert.SerializeObject(
    new
    {
      filter = new
      {
        property = new
        {
          name = "Status",
          type = "default"
        },
        value = "Running",
        comparisonOperator = "eq",
        type = "comparison"
      }
    }
  );

  JobQueryResult result = await deviceJobClient.QueryJobHistoryJsonAsync(json);

  // List query result
  foreach (JobResponse job in result.Result)
  {
    Console.WriteLine("DeviceID: {0}\t\tJobId: {1}",
      job.DeviceId,
      job.JobId
      );
  }
}
```

По завершении родительского задания устройства пример кода выдает список всех заданий устройств. Это показано ниже. Родительское задание завершается только после того, как будут выполнены все соответствующие дочерние задания. На представленном ниже снимке экрана родительское задание является последним в списке — на это указывает то, что **ParentJobId** имеет значение **''**. Родительское задание также включает в себя поле **Reason**, содержащее строку, которая указывает на совокупный результат запроса. В данном случае она показывает, что обновлялись шесть устройств и все операции были выполнены успешно.

![][img-output2]

Приведенный выше список формируется путем запроса всех заданий и их сортировки по времени начала. Любое из представленных ниже свойств объекта **JobResponse** можно использовать для запросов к журналу заданий устройств.

![][img-properties]

Другие примеры запросов к журналу заданий устройств см. в [нашей библиотеке выражений запросов][lnk-query-samples].

### Сведения о реализации эмулятора устройств
В предыдущих разделах мы приводили сведения о реализации обновления встроенного ПО со стороны службы (как запустить задание устройства и запросить его состояние). Теперь рассмотрим соответствующую реализацию со стороны устройства.

Клиентская библиотека управления устройствами центра IoT Azure обрабатывает взаимодействие между устройством и службой, так что реализовать остается только соответствующую устройству логику. Она состоит из двух компонентов.

* Реализация соответствующего процесса обновления встроенного ПО устройства: сюда входит запись необходимой логики для загрузки пакета встроенного ПО и применения обновления в перечисленных ниже обратных вызовах. В примере эмуляции это реализуется в следующем [коде][lnk-github-firmware].
  
  ```
  object_firmwareupdate *obj = get_firmwareupdate_object(0);
  obj->firmwareupdate_packageuri_write_callback = start_firmware_download;
  // platform specific code
  obj->firmwareupdate_update_execute_callback = start_firmware_update;
  //platform specific code
  ```
* Информирование службы о процессе обновления встроенного ПО: сюда входит изменение данных в полях состояния обновления встроенного ПО и результата этого обновления. Для этого вызываются API **set\_firmwareupdate\_state** и **set\_firmwareupdate\_updateresult**. В примере эмуляции это реализуется в следующем [коде][lnk-github-firmware].

## Дальнейшие действия
Дополнительные сведения о возможностях управления устройствами центра IoT Azure можно просмотреть в руководствах:

* [Включение управляемых устройств за шлюзом IoT][lnk-dm-gateway]
* [Знакомство с клиентской библиотекой управления устройствами для центра Azure IoT][lnk-library-c]
* Клиентская библиотека управления устройствами центра IoT Azure содержит полноценный пример кода с использованием [устройства Intel Edison][lnk-edison]

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Разработка решения][lnk-design]
* [Руководство разработчика по центру Azure IoT (IoT — Интернет вещей)][lnk-devguide]
* [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]
* [Управление центрами IoT через портал Azure][lnk-portal]

<!-- Images and links -->

[img-architecture]: media/iot-hub-device-management-device-jobs/image1.png
[img-output1]: media/iot-hub-device-management-device-jobs/image2.png
[img-output2]: media/iot-hub-device-management-device-jobs/image3.png
[img-properties]: media/iot-hub-device-management-device-jobs/image4.png

[lnk-apidocs]: iot-hub-sdks-summary.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-github-firmware]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/c/iotdm_client/samples/iotdm_simple_sample/iotdm_simple_sample.c
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->