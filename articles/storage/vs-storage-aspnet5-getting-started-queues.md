---
title: Начало работы с хранилищем очередей и подключенными службами Visual Studio (ASP.NET 5) | Microsoft Docs
description: Начало работы с использованием хранилища очередей Azure в проекте ASP.NET 5 в Visual Studio
services: storage
documentationcenter: ''
author: TomArcher
manager: douge
editor: ''

ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: tarcher

---
# Начало работы с хранилищем очередей и подключенными службами Visual Studio (ASP.NET 5)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## Обзор
В этой статье описывается, как приступить к использованию хранилища очередей Azure в Visual Studio после создания учетной записи хранения Azure или указания ссылки на нее в проекте ASP.NET 5 с помощью диалогового окна **Добавление подключенных служб** в Visual Studio. Операция **Добавить подключенные службы** устанавливает соответствующие пакеты NuGet для доступа к службе хранилища Azure в вашем проекте и добавляет строку подключения для учетной записи хранения в файлы конфигурации проекта.

Хранилище очередей Azure — это служба хранения большого количества сообщений, к которым можно получить доступ практически из любой точки мира с помощью вызовов с проверкой подлинности по протоколу HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать миллионы сообщений, ограничиваемых общей емкостью учетной записи хранения.

Чтобы начать работу, сначала необходимо создать очередь Azure в учетной записи хранения. Мы покажем, как создать очередь в коде. Кроме того, вы узнаете, как выполнять базовые операции с очередями, например добавлять, изменять, считывать и удалять сообщения в очередях. Примеры написаны на C#, и в них используется библиотека клиента службы хранилища Azure для .NET. Дополнительные сведения см. на сайте [ASP.NET](http://www.asp.net).

**ПРИМЕЧАНИЕ.** Некоторые интерфейсы API, которые выполняют вызовы в службу хранилища Azure в ASP.NET 5, являются асинхронными. Дополнительные сведения см. в статье [Асинхронное программирование с использованием ключевых слов Async и Await](http://msdn.microsoft.com/library/hh191443.aspx). В следующем примере кода предполагается, что используются асинхронные методы программирования.

* Дополнительные сведения о выполнении программных операций с очередями см. в статье [Приступая к работе с хранилищем очередей Azure с помощью .NET](storage-dotnet-how-to-use-queues.md).
* Общие сведения о службе хранилища Azure см. в [документации по службе хранилища](https://azure.microsoft.com/documentation/services/storage/).
* Общие сведения об облачных службах Azure см. в [документации по облачным службам](https://azure.microsoft.com/documentation/services/cloud-services/).
* Дополнительную информацию о программировании для ASP.NET см. в разделе [ASP.NET](http://www.asp.net).

## Доступ к очередям в коде
Для доступа к очередям в проектах ASP.NET 5 необходимо включить следующие элементы во все файлы исходного кода C#, которые обращаются к хранилищу очередей Azure.

1. Убедитесь, что объявления пространств имен в верхней части файла C# содержат указанные ниже выражения **using**.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Получите объект **CloudStorageAccount**, представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Используйте объект **CloudQueueClient** для ссылки на объекты очереди в вашей учетной записи хранения.
   
        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Получите объект **CloudQueue** для ссылки на определенную очередь.
   
        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**ПРИМЕЧАНИЕ.** Вставьте весь код, представленный выше, перед кодом в следующих примерах.

### Создание очереди в коде
Чтобы создать очередь Azure в коде, просто добавьте вызов **CreateIfNotExistsAsync**.

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

## Добавление сообщения в очередь
Чтобы вставить сообщение в существующую очередь, создайте новый объект **CloudQueueMessage**, а затем вызовите метод **AddMessageAsync**.

Для создания объекта **CloudQueueMessage** можно использовать либо строку (в формате UTF-8), либо массив типа byte.

Ниже приведен пример, который вставляет сообщение "Hello, World".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

## Чтение сообщения в очереди
Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод **PeekMessageAsync**.

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


## Чтение и удаление сообщения в очереди
Ваш код может удалить сообщение из очереди в два этапа.

1. Вызовите **GetMessageAsync**, чтобы получить следующее сообщение в очереди. Сообщение, возвращаемое методом **GetMessageAsync**, становится невидимым для любого другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд.
2. Чтобы завершить удаление сообщения из очереди, вызовите **DeleteMessageAsync**.

Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Следующий код вызывает метод **DeleteMessageAsync** сразу после обработки сообщения.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## Дополнительные варианты удаления сообщений из очереди
Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод **GetMessages** используется для получения 20 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла **foreach**. Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание, что пятиминутный период начинается для всех сообщений одновременно, поэтому по прошествии 5 минут с момента вызова **GetMessages** все сообщения, которые не были удалены, снова становятся видимыми.

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## Получение длины очереди
Вы можете узнать приблизительное количество сообщений в очереди. Метод **FetchAttributes** отправляет в службу очередей запрос на извлечение атрибутов очереди, включая количество сообщений. Свойство **ApproximateMethodCount** возвращает последнее значение, полученное с использованием метода **FetchAttributes**, без обращения к службе очередей.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Использование схемы Async-Await со стандартными API очереди
В этом примере показано использование алгоритма Async-Await со стандартными API очередей. Пример вызывает асинхронную версию каждого заданного метода. Это можно увидеть после исправления каждого метода для асинхронного использования. При использовании асинхронного метода схема Async-Await приостанавливает локальное выполнение процесса до завершения вызова. Благодаря этому текущий поток может выполнять другие задачи, что позволяет избежать возникновения узких мест и повысить общую скорость реагирования приложения. Дополнительные сведения об использовании алгоритма Async-Await в .NET см. в статье [Асинхронное программирование с использованием ключевых слов Async и Await (C# и Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Create a message to add to the queue.
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message.
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## Удаление очереди
Чтобы удалить очередь и все сообщения в ней, вызовите метод **Delete** для объекта очереди.

    // Delete the queue.
    messageQueue.Delete();


## Дальнейшие действия
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

<!---HONumber=AcomDC_0727_2016-->