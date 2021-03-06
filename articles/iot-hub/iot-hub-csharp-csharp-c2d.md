---
title: Отправка сообщений из облака на устройства с помощью центра IoT | Microsoft Docs
description: Следуйте инструкциям этого учебника, чтобы узнать, как отправлять сообщения с облака на устройства в центре IoT Azure с помощью C#.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: elioda

---
# Учебник: как отправлять сообщения из облака на устройства с помощью центра IoT и .Net
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## Введение
Центр Azure IoT — полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью приложения. В учебнике [Приступая к работе с центром IoT] показано, как создать центр IoT, подготовить в нем удостоверение устройства и написать код виртуального устройства, которое отправляет сообщения с устройства в облако.

Это руководство является логическим продолжением статьи [Приступая к работе с центром Azure IoT с использованием Java]. В нем показано следующее:

* Отправка сообщения, передаваемого из облака на устройство, из облачной серверной части приложения на одно устройство через центр IoT.
* Получение на устройстве сообщений, передаваемых из облака на устройство.
* Запрос из облачной серверной части приложения подтверждения доставки (*отзыва*) для сообщений, отправленных на устройство из центра IoT.

Дополнительные сведения о сообщениях, отправляемых с облака на устройство, можно найти в [Руководстве разработчика по центру IoT][IoT Hub Developer Guide - C2D].

К завершению этого учебника вы запустите два консольных приложения Windows.

* **SimulatedDevice**, измененную версию приложения, созданного в учебнике [Приступая к работе с центром IoT]. Это приложение подключается к центру IoT и получает сообщения с облака на устройство.
* **SendCloudToDevice**, которое отправляет сообщение из облака на имитацию устройства с помощью центра IoT, а затем получает подтверждение о его доставке.

> [!NOTE]
> Для центра IoT существуют пакеты SDK для многих платформ устройств и языков (включая C, Java и Javascript). Эти пакеты работают на основе пакетов SDK для устройств Azure IoT. Пошаговые инструкции по связыванию устройства с кодом из этого учебника, а также по подключению к центру Azure IoT см. в [центре разработчика для Azure IoT].
> 
> 

Для работы с этим руководством требуется:

* Microsoft Visual Studio 2015;
* Активная учетная запись Azure. (Если ее нет, можно создать бесплатную пробную версию учетной записи всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][lnk-free-trial].)

## Получение сообщений на виртуальном устройстве
В этом разделе вы измените приложение имитации устройства, созданное в разделе [Приступая к работе с центром IoT], для получения сообщений из облака на устройство от центра IoT.

1. В Visual Studio в проекте **SimulatedDevice** добавьте в класс **Program** следующий метод:
   
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
   
                await deviceClient.CompleteAsync(receivedMessage);
            }
        }
   
    Метод `ReceiveAsync` асинхронно возвращает полученное сообщение, когда устройство его получило. Он возвращает значение *NULL* после истечения заданного времени ожидания (в этом примере используется значение по умолчанию, равное одной минуте). В этом случае необходимо, чтобы код продолжил ожидать новые сообщения. По этой причине присутствует строка `if (receivedMessage == null) continue`.
   
    Вызов `CompleteAsync()` уведомляет центр IoT об успешной обработке сообщения. Можно спокойно удалить сообщение из очереди устройства. Если что-то помешает приложению устройства завершить обработку сообщения, центр IoT доставит его снова. Поэтому важно, чтобы логика обработки сообщений в приложении устройства была *идемпотентной*. Это обеспечит одинаковый результат при многократном получении одного и того же сообщения. Приложение может также временно отказаться от сообщения. Тогда центр IoT будет хранить сообщение в очереди для последующей обработки. Или приложение может отклонить сообщение, и тогда оно будет окончательно удалено из очереди. Дополнительную информацию о жизненном цикле сообщений, передаваемых из облака на устройство, см. в [руководстве разработчика для центра IoT][IoT Hub Developer Guide - C2D].
   
   > [!NOTE]
   > При использовании HTTP/1 вместо AMQP в качестве транспорта немедленно возвращается метод `ReceiveAsync`. Схема сообщений, передаваемых из облака на устройство с помощью HTTP/1, может использоваться для периодически подключаемых устройствах, которые редко проверяют наличие новых сообщений (реже, чем раз в 25 минут). Передача большего количества получений HTTP/1 приводит к регулированию запросов в центре IoT. Дополнительную информацию о различиях между поддержкой AMQP и HTTP/1, а также регулировании центра IoT см. в [руководстве разработчика для центра IoT][IoT Hub Developer Guide - C2D].
   > 
   > 
2. Добавьте в метод **Main** непосредственно перед строкой `Console.ReadLine()` следующий метод.
   
        ReceiveC2dAsync();

> [!NOTE]
> Для упрощения в этом учебнике не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя].
> 
> 

## Отправка сообщения из облака на устройство
В этом разделе вам предстоит написать консольное приложение для Windows, которое отправляет сообщения, передаваемые из облака на устройство, в имитацию приложения устройства.

1. В текущем решении Visual Studio создайте новый проект классического приложения Visual C# с помощью шаблона проекта **Консольное приложение**. Присвойте проекту имя **SendCloudToDevice**.
   
    ![Новый проект в Visual Studio][20]
2. В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения...**.
   
    Откроется окно **Управление пакетами NuGet**.
3. Выполните поиск `Microsoft Azure Devices` и щелкните **Установить**, после чего примите условия использования.
   
    После этого будут выполнены скачивание, установка и добавление ссылки на [пакет NuGet пакета SDK служб в Azure IoT].
4. Добавьте следующий оператор `using` в начало файла **Program.cs**.
   
        using Microsoft.Azure.Devices;
5. Добавьте следующие поля в класс **Program**. Замените значение заполнителя строкой подключения центра IoT из раздела [Приступая к работе с центром IoT].
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Добавьте следующий метод в класс **Program**.
   
        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }
   
    Этот метод отправляет на устройство с идентификатором `myFirstDevice` новое сообщение, передаваемое из облака на устройство. Измените этот параметр соответствующим образом, если вы использовали значение, отличное от указанного в разделе [Приступая к работе с центром IoT].
7. Наконец, добавьте следующие строки в метод **Main**:
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. В Visual Studio щелкните правой кнопкой мыши свое решение и выберите пункт **Назначить запускаемые проекты**. Выберите **Несколько запускаемых проектов**, а затем выберите действие **Запуск** для **ProcessDeviceToCloudMessages**, **SimulatedDevice** и **SendCloudToDevice**.
9. Нажмите клавишу **F5**. Должны запуститься все три приложения. Выберите окна **SendCloudToDevice** и нажмите клавишу **ВВОД**. Имитация приложения должна получить сообщение.
   
   ![Приложение получает сообщение][21]

## Получение подтверждений доставки
Для каждого сообщения, передаваемого из облака на устройство, в центре IoT можно запросить подтверждения доставки (или истечения срока действия). Это позволяет серверной части облака легко передавать данные в логику повторных попыток или компенсаций. Дополнительную информацию о подтверждениях при передаче из облака на устройство см. в [руководстве разработчика для центра IoT][IoT Hub Developer Guide - C2D].

В этом разделе вы измените приложение **SendCloudToDevice**, чтобы оно запрашивало подтверждение и получало его из центра IoT.

1. В Visual Studio в проекте **SendCloudToDevice** добавьте в класс **Program** следующий метод:
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();
   
            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();
   
                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }
   
    Обратите внимание, что шаблон получения здесь аналогичен шаблону, использовавшемуся для получения сообщений из облака на устройство из приложения устройства.
2. Добавьте следующий метод в метод **Main** непосредственно после строки `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`.
   
        ReceiveFeedbackAsync();
3. Чтобы запросить подтверждение доставки сообщения из облака на устройство, необходимо указать свойство в методе **SendCloudToDeviceMessageAsync**. Добавьте следующую строку сразу после строки `var commandMessage = new Message(...);`:
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. Запустите приложения, нажав клавишу **F5**. Должны запуститься все три приложения. Выберите окна **SendCloudToDevice** и нажмите клавишу **ВВОД**. Имитация приложения должна получить сообщение, а через несколько секунд ваше приложение **SendCloudToDevice** должно получить сообщение о подтверждении.
   
   ![Приложение получает сообщение][22]

> [!NOTE]
> Для упрощения в этом учебнике не реализуются какие-либо политики повтора. В рабочем коде следует реализовать политики повтора (например, экспоненциальную задержку), как указано в статье MSDN [Обработка временного сбоя].
> 
> 

## Дальнейшие действия
В этом учебнике вы научились отправлять и получать сообщения с облака на устройство.

Примеры комплексных решений, в которых используется центр IoT, см. в [документации по Azure IoT Suite].

Дополнительные сведения о разработке решений с помощью центра IoT см. в [руководстве разработчика по центру IoT].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[пакет NuGet пакета SDK служб в Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Обработка временного сбоя]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[руководстве разработчика по центру IoT]: iot-hub-devguide.md
[Приступая к работе с центром Azure IoT с использованием Java]: iot-hub-csharp-csharp-getstarted.md
[Приступая к работе с центром IoT]: iot-hub-csharp-csharp-getstarted.md
[центре разработчика для Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[документации по Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0727_2016-->