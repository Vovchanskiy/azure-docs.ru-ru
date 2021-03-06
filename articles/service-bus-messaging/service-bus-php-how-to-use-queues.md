---
title: Как использовать очереди служебной шины с помощью PHP | Microsoft Docs
description: Узнайте, как использовать очереди служебной шины в Azure. Примеры кода написаны на PHP.
services: service-bus
documentationcenter: php
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/04/2016
ms.author: sethm

---
# <a name="how-to-use-service-bus-queues"></a>Как использовать очереди служебной шины
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

В этом руководстве показано, как использовать очереди служебной шины. Примеры написаны на PHP и используют [пакет Azure SDK для PHP](../php-download-sdk.md). Здесь описаны такие сценарии, как **создание очередей**, **отправка и получение сообщений**, а также **удаление очередей**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Создание приложения PHP
Для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, достаточно сослаться на классы в [пакете SDK Azure для PHP](../php-download-sdk.md) непосредственно из кода. Для создания приложения можно использовать любые средства разработки или Блокнот.

> [!NOTE]
> В установленном пакете PHP должно быть установлено и включено [расширение OpenSSL](http://php.net/openssl).
> 
> 

В этом руководстве будут использоваться компоненты службы, которые могут быть вызваны локально или в коде из приложения PHP, работающем в веб-роли Azure, рабочей роли или на веб-сайте Azure.

## <a name="get-the-azure-client-libraries"></a>Получение клиентских библиотек Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Настройка приложения для использования служебной шины
Чтобы использовать интерфейсы API очередей служебной шины, необходимо следующее:

1. Ссылка на файл автозагрузчика с использованием оператора [require_once][require_once].
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServicesBuilder** .

> [!NOTE]
> В этом примере (и других примерах в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure с помощью Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо использовать ссылку на файл автозагрузчика **WindowsAzure.php**.
> 
> 

```
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

В приведенных ниже примерах всегда будет отображаться оператор `require_once`, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

## <a name="set-up-a-service-bus-connection"></a>Настройка подключения к Service Bus
Для создания клиента служебной шины необходимо сначала сформировать правильную строку подключения в следующем формате:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

где **конечная точка** обычно имеет формат `[yourNamespace].servicebus.windows.net`.

Для создания клиента службы Azure необходимо использовать класс **ServicesBuilder**. Вы можете:

* Передать строку подключения напрямую или
* использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения:
  * По умолчанию предоставляется поддержка одного внешнего источника — переменных среды.
  * Для добавления новых источников можно унаследоваться от класса **ConnectionStringSource**.

В приведенных здесь примерах строка подключения передается напрямую.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="how-to-create-a-queue"></a>Практическое руководство. Создание очереди
Операции управления для очередей служебной шины можно выполнять с помощью класса **ServiceBusRestProxy**. Объект **ServiceBusRestProxy** создается с помощью фабричного метода **ServicesBuilder::createServiceBusService** с соответствующей строкой подключения, которая включает в себя разрешения маркеров для управления им.

В приведенном ниже примере показано, как создать экземпляр **ServiceBusRestProxy** и вызвать метод **ServiceBusRestProxy->createQueue** для создания очереди с именем `myqueue` в пространстве имен службы `MySBNamespace`.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Можно использовать метод `listQueues` для объектов `ServiceBusRestProxy`, чтобы проверить, существует ли уже очередь с указанным именем в пространстве имен.
> 
> 

## <a name="how-to-send-messages-to-a-queue"></a>Практическое руководство. Отправка сообщений в очередь
Чтобы отправить сообщение в очередь служебной шины, в приложении вызывается метод **ServiceBusRestProxy->sendQueueMessage**. В следующем примере кода показано, как отправить сообщение в очередь `myqueue`, созданную ранее в пространстве имен службы `MySBNamespace`.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Сообщения, отправляемые в очереди служебной шины и получаемые из них, представляют собой экземпляры класса **BrokeredMessage**. Объекты **BrokeredMessage** имеют набор стандартных методов (например, **getLabel**, **getTimeToLive**, **setLabel** и **setTimeToLive**) и свойств, используемых для хранения настраиваемых свойств приложения, а также текст из произвольных данных приложения.

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Максимальный размер очереди ограничен 5 ГБ.

## <a name="how-to-receive-messages-from-a-queue"></a>Как получать сообщения из очереди
Основным способом получения сообщений из очереди является метод **ServiceBusRestProxy->receiveQueueMessage**. Сообщения могут приниматься в двух режимах: **ReceiveAndDelete** (по умолчанию) и **PeekLock**.

В режиме **ReceiveAndDelete** получение является одиночной операцией, т. е. когда служебная шина получает запрос на чтение для сообщения в очереди, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает в ситуациях, когда приложение может не обрабатывать сообщение при сбое. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы другие получатели не могли его принять, а затем возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), служебная шина завершает второй этап процесса получения, передавая полученное сообщение в метод **ServiceBusRestProxy->deleteMessage**. Когда служебная шина обнаруживает вызов **deleteMessage** сообщение помечается как используемое и удаляется из очереди.

В следующем примере показывается, как можно получать и обрабатывать сообщения с помощью режима **PeekLock** (не является режимом по умолчанию).

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/windowsazure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений
служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто такой подход называют **Обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, рекомендуется добавить в приложение дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="next-steps"></a>Дальнейшие действия
Вы ознакомились с основами использования очередей служебной шины. Дополнительные сведения см. в статье [Очереди, разделы и подписки служебной шины][].

Дополнительные сведения также доступны в [Центре разработчика PHP](/develop/php/).

[Очереди, разделы и подписки]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once





<!---HONumber=Oct16_HO2-->


