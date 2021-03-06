---
title: Как использовать очереди служебной шины в Node.js | Microsoft Docs
description: Узнайте, как использовать очереди служебной шины в Azure в приложении Node.js.
services: service-bus
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 10/03/2016
ms.author: sethm

---
# <a name="how-to-use-service-bus-queues"></a>Как использовать очереди служебной шины
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

В этой статье показано, как использовать очереди служебной шины в Node.js. Примеры написаны на JavaScript и используют модуль Node.js для Azure. Здесь описаны такие сценарии, как **создание очередей**, **отправка и получение сообщений**, а также **удаление очередей**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия](#next-steps).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-node.js-application"></a>Создание приложения Node.js
Создайте пустое приложение Node.js. Указания по созданию приложения Node.js можно найти в статьях [Начало работы с веб-приложениями Node.js в службе приложений Azure][] или [Построение и развертывание приложения Node.js в облачной службе Azure][](с помощью Windows PowerShell.md).

## <a name="configure-your-application-to-use-service-bus"></a>Настройка приложения для использования служебной шины
Чтобы использовать служебную шину Azure, необходимо скачать и запустить пакет Azure для Node.js. Пакет содержит набор библиотек, взаимодействующих со службами REST Service Bus.

### <a name="use-node-package-manager-(npm)-to-obtain-the-package"></a>Использование диспетчера пакета Node (NPM) для получения пакета
1. Используя командное окно **Windows PowerShell для Node.js**, перейдите в папку **C:\\node\\sbqueues\\WebRole1**, в которой создан пример приложения.
2. В командном окне введите **npm install azure**, что должно привести к результату, аналогичному следующему:
   
    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. Выполнив команду **ls** вручную, можно убедиться, что папка **node\_modules** создана. В этой папке находится пакет **azure**, содержащий библиотеки, необходимые для доступа к очередям служебной шины.

### <a name="import-the-module"></a>Импорт модуля
С помощью Блокнота или другого текстового редактора добавьте в начало файла **server.js** приложения следующее:

```
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Настройка подключения к служебной шине Azure
Модуль Azure считывает переменные среды AZURE\_SERVICEBUS\_NAMESPACE и AZURE\_SERVICEBUS\_ACCESS\_KEY для получения сведений, необходимых для подключения к служебной шине. Если эти переменные среды не заданы, при вызове **createServiceBusService** необходимо указать сведения об учетной записи.

Пример настройки переменных среды в файле конфигурации для облачной службы Azure см. в статье [Облачная служба Node.js с хранилищем][Облачная служба Node.js с хранилищем].

Пример настройки переменных среды для веб-сайта Azure на [классическом портале Azure][классическом портале Azure] см. в статье [Веб-приложение Node.js с хранилищем][Веб-приложение Node.js с хранилищем].

## <a name="create-a-queue"></a>Создание очереди
Объект **ServiceBusService** позволяет работать с очередями служебной шины. Следующий код создает объект **ServiceBusService**. Добавьте его в начало файла **server.js** после оператора импорта модуля Аzure.

```
var serviceBusService = azure.createServiceBusService();
```

Вызов **createQueueIfNotExists** для объекта **ServiceBusService** возвращает указанную очередь (при ее наличии) или создает новую очередь с указанным именем. В следующем коде используется метод **createQueueIfNotExists**, чтобы создать очередь `myqueue` или подключиться к ней.

```
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

**createServiceBusService** также поддерживает дополнительные параметры, позволяющие переопределить настройки очереди по умолчанию, такие как срок жизни сообщения или максимальный размер очереди. В следующем примере показано, как установить максимальный размер очереди 5 ГБ и срок жизни 1 минуту.

```
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Фильтры
Используя **ServiceBusService**, к выполняемым операциям можно применить дополнительные операции фильтрации. К операциям фильтрации могут относиться ведение журнала, автоматический повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

```
function handle (requestOptions, next)
```

Выполнив предварительную обработку параметров запроса, метод должен вызвать `next`, передавая обратный вызов со следующей сигнатурой:

```
function (returnObject, finalCallback, next)
```

В этой функции обратного вызова и после обработки **returnObject** (ответ на запрос к серверу) функция обратного вызова должна либо вызвать `next` (при наличии), чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать `finalCallback` для завершения обращения к службе.

В пакет SDK Azure для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter**. Следующий код создает объект **ServiceBusService**, использующий **ExponentialRetryPolicyFilter**:

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Отправка сообщений в очередь
Чтобы отправить сообщение в очередь служебной шины, в приложении вызывается метод **sendQueueMessage** для объекта **ServiceBusService**. Сообщения, отправляемые в очереди служебной шины и получаемые из них, — это объекты **BrokeredMessage**, которые имеют набор стандартных свойств (таких как **Label** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из произвольных данных приложения. Приложение может задать текст сообщения, передав строку как сообщение. Все необходимые стандартные свойства будут заполнены значениями по умолчанию.

В следующем примере показано, как отправить тестовое сообщение в очередь с именем `myqueue` с помощью метода **sendQueueMessage**.

```
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину][Квоты на служебную шину].

## <a name="receive-messages-from-a-queue"></a>Получение сообщений из очереди
Сообщения извлекаются из очереди с помощью метода **receiveQueueMessage** для объекта **ServiceBusService**. По умолчанию прочитанные сообщения удаляются из очереди. Но можно прочитать (извлечь) сообщение и заблокировать его без удаления из очереди, задав для необязательного параметра **isPeekLock** значение **true**.

Поведение по умолчанию — чтение и удаление сообщения как часть операции получения — это простейшая модель, оптимальная для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

Если для параметра **isPeekLock** установлено значение **true**, получение становится двухэтапной операцией, что позволяет поддерживать приложения, которые не могут пропускать сообщения. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Обработав сообщение (или сохранив его в надежном месте для последующей обработки), приложение вызывает метод **deleteMessage** и указывает сообщение, которое будет удалено как параметр, таким образом завершая второй этап процесса получения. Метод **deleteMessage** помечает сообщение как использованное и удаляет его из очереди.

В следующем примере показано, как получать и обрабатывать сообщения с помощью метода **receiveQueueMessage**. Сначала сообщение в примере получается и удаляется, затем оно получается с помощью параметра **isPeekLock**, для которого задано значение **true**, а затем удаляется с помощью метода **deleteMessage**.

```
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Как обрабатывать сбои приложения и нечитаемые сообщения
служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если по какой-либо причине приложению-получателю не удается обработать сообщение, оно вызывает метод **unlockMessage** в объекте **ServiceBusService**. После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus разблокирует сообщение автоматически и сделает его доступным для приема.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода **deleteMessage**, сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию об очередях см. в следующих ресурсах.

* [Очереди, разделы и подписки][Очереди, разделы и подписки]
* Репозиторий [пакетов SDK Azure для Node][] на сайте GitHub
* [Центр разработчиков Node.js.](/develop/nodejs/)

[Пакет SDK Azure для Node]: https://github.com/Azure/azure-sdk-for-node
[классическом портале Azure]: http://manage.windowsazure.com

[Облачная служба Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Очереди, разделы и подписки]: service-bus-queues-topics-subscriptions.md
[Создание и развертывание приложения Node.js на веб-сайте Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Облачная служба Node.js с хранилищем]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[Веб-приложение Node.js с хранилищем]: ../storage/storage-nodejs-how-to-use-table-storage.md
[Квоты на служебную шину]: service-bus-quotas.md




<!--HONumber=Oct16_HO2-->


