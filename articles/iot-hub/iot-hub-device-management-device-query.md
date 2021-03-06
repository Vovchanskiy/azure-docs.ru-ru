---
title: 'Управление устройствами в центре IoT: запросы для поиска '
;двойников";: ''
'|': ''
microsoft: ''
azure": ''
description: 'В учебнике по использованию Azure IoT Hub для управления устройствами описывается, как использовать запросы для поиска '
устройств.": ''
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
# Учебник. Поиск "двойников" устройств с помощью запросов на C# (предварительная версия)
[!INCLUDE [iot-hub-device-management-query-selector](../../includes/iot-hub-device-management-query-selector.md)]

Управление устройствами Azure IoT позволяет находить "двойники" устройств (представление физического устройства в службе) с помощью запросов. Запросы можно составлять на основе свойств устройства, свойств службы или тегов в "двойнике" устройства. В запросе можно использовать теги и свойства:

* Для запроса "двойников" устройств с помощью тегов необходимо передать массив строк, после чего запрос возвращает набор устройств, помеченных всеми указанными строками.
* Для запроса "двойников" устройств с помощью свойств службы или устройства используется выражение запроса JSON.

Дополнительные сведения о "двойниках" устройств и запросах см. в статье [Общие сведения об управлении устройствами с помощью центра IoT Azure (предварительная версия)][lnk-dm-overview].

## Пример выполнения запроса устройств
Следующий пример дополняет учебник [Приступая к работе с функцией управления устройствами центра IoT Azure][lnk-get-started]. Он предназначен для работы с другими виртуальными устройствами и рассчитан на поиск определенных устройств.

### Предварительные требования
Перед запуском этого примера необходимо выполнить действия, описанные в статье [Приступая к работе с функцией управления устройствами центра IoT Azure][lnk-get-started]. Это означает, что виртуальные устройства должны быть запущены. Если эта процедура уже выполнялась, виртуальные устройства необходимо перезапустить.

### Запуск примера
Чтобы запустить пример, необходимо выполнить процесс **Query.exe**. При этом выполняются сразу несколько разных запросов. Чтобы запустить пример, выполните следующие действия.

1. После запуска виртуальных устройств дайте им поработать не меньше минуты. Это нужно для того, чтобы свойства устройства успели синхронизироваться между "двойником" и физическим устройством. Дополнительные сведения о синхронизации см. в статье [Учебник по использованию "двойников" устройств (предварительная версия)][lnk-twin-tutorial].
2. В корневой папке, куда был клонирован репозиторий **azure-iot-sdks**, перейдите к папке **azure-iot-sdks\\csharp\\service\\samples\\bin**.
3. Запустите `Query.exe <IoT Hub Connection String>`

В окне командной строки появится результат выполнения запросов к объектам устройств с использованием тегов, свойств службы и свойств устройства.

## Структура запроса (JSON)
Запросы с использованием свойств устройства и свойств службы выполняются с помощью строки JSON, представляющей сам запрос. Строка JSON состоит из четырех частей. Ниже описывается каждая из них, а также объект C#, который можно сериализовать в правильную строку JSON.

* **Project**: выражение, указывающее, какие поля из объекта устройства необходимо включить в результирующий набор запроса (эквивалент метода SELECT в SQL).
  
  ```
      var query = JsonConvert.SerializeObject(
          new
          {
              project = new
              {
                  all = false,
                  properties = new[]
                  {
                      new
                      {
                      name = "CustomerId",
                      type = "service"
                      },
                      new
                      {
                      name = "Weight",
                      type = "service"
                      }
                  }
              }
          }
      );
    ```
* **Filter**: выражение, ограничивающее объекты устройств, добавляемые в результирующий набор запроса (эквивалент метода WHERE в SQL).
  
  ```
  var query = JsonConvert.SerializeObject(
      new
      {
        filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = "123456",
          comparisonOperator = "eq",
          type = "comparison"
        }
      }
  );
  ```
* **Aggregate**: выражение, определяющее способ группирования данных в результирующем наборе запроса (эквивалент метода GROUPBY в SQL).
  
  ```
  var query = JsonConvert.SerializeObject(
      new
      {
      filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = (string)null,
          comparisonOperator = "ne",
          type = "comparison"
        },
        aggregate = new
        {
          keys = new[]
          {
            new
            {
              name = "CustomerId",
              type = "service"
            }
          },
          properties = new[]
          {
            new
            {
              @operator = "avg",
              property = new
              {
                name = "Weight",
                type = "service"
              },
              columnName = "TotalWeight"
            }
          }
        }
      }
  );
  ```
* **Sort**: выражение, определяющее, какое свойство нужно использовать для сортировки данных в результирующем наборе запроса (эквивалент метода ORDER BY в SQL). Если для сортировки задано значение NULL, по умолчанию используется свойство **deviceID**.
  
  ```
  var query = JsonConvert.SerializeObject(
    new
    {
      sort = new[]
      {
        new
        {
          property = new
          {
            name = "QoS",
            type = "service"
          },
          order = "asc"
        }
      }
    }
  );
  ```

### Ограничения
В общедоступной предварительной версии реализации запросов действуют некоторые ограничения.

* Выражение запроса не проверяется.
* Запросы чувствительны к регистру.
* Если выражения используются в запросах на основе свойств службы или устройства, выдаются только первые 100 устройств. Пример реализации постраничного представления см. в [нашей библиотеке запросов][lnk-query-samples].

Дополнительные сведения о синтаксисе и доступных полях JSON см. [здесь][lnk-query-expression-guide]. Кроме того, примеры запросов доступны в [нашей библиотеке выражений запросов][lnk-query-samples].

### Запросы по свойствам устройств и служб
Составленное выражение запроса JSON можно использовать для запроса "двойников" устройств. Вызовите **QueryDeviceJsonAsync** и проверьте поле **AggregateResult** в случае статистических запросов или **Result** в случае всех прочих запросов. Поле **Result** содержит список объектов устройств, представляющих соответствующих запросу "двойников" устройств. Поле **AggregateResult** содержит массив словарей, каждый из которых содержит результирующую строку.

Эти запросы используются в файле **Program.cs** проекта **Query**.

```
var foundDevices = (await registryManager.QueryDevicesJsonAsync(query)).Result;

var results = (await registryManager.QueryDevicesJsonAsync(query)).AggregateResult;
```

### Запрос на основе тегов
Запросы на основе тегов позволяют находить объекты устройств, не используя выражение запросов JSON. Если передаваемых тегов несколько, возвращаются только объекты устройств, помеченные всеми указанными тегами. Второй параметр — **maxCount**, максимальное число возвращаемых устройств. Максимальное значение параметра **maxCount** — 1000.

```
var foundDevices = await registryManager.QueryDevicesAsync(new[] { "bacon" }, 100);
```

### Реализация устройств
Запрос активируется клиентской библиотекой управления устройствами центра IoT Azure. Устройства можно запрашивать, если их свойства синхронизированы (как описано в статье [Учебник по использованию "двойников" устройств (предварительная версия)][lnk-twin-tutorial]). Свойства устройства доступны только после того, как физическое устройство подключится к центру IoT и предоставит начальные значения.

## Дальнейшие действия
Дополнительные сведения о возможностях управления устройствами центра IoT Azure можно просмотреть в руководствах:

* [How to use device jobs to update device firmware (Использование заданий устройства для обновления встроенного ПО устройства)][lnk-tutorial-jobs]
* [Включение управляемых устройств за шлюзом IoT][lnk-dm-gateway]
* [Знакомство с клиентской библиотекой управления устройствами для центра Azure IoT][lnk-library-c]

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Разработка решения][lnk-design]
* [Руководство разработчика по центру Azure IoT (IoT — Интернет вещей)][lnk-devguide]
* [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]
* [Управление центрами IoT через портал Azure][lnk-portal]

<!-- images and links -->
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-jobs-tutorial]: iot-hub-device-management-device-jobs.md
[lnk-query-spec]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-query-expression-guide]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md

[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->