---
title: Использование разделов служебной шины в PHP | Microsoft Docs
description: Узнайте, как использовать разделы служебной шины с PHP в Azure.
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
ms.date: 05/10/2016
ms.author: sethm

---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Как использовать разделы и подписки служебной шины
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Из этой статьи вы узнаете, как использовать разделы и подписки служебной шины. Примеры написаны на PHP и используют [пакет Azure SDK для PHP](../php-download-sdk.md). В этой статье описаны такие сценарии, как **создание разделов и подписок**, **создание фильтров подписок**, **отправка сообщений в раздел**, **получение сообщений из подписки** и **удаление разделов и подписок**.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Создание приложения PHP
Единственным требованием для создания приложения PHP, которое получает доступ к службе BLOB-объектов Azure, является наличие ссылки на классы в [пакете SDK для Azure для PHP](../php-download-sdk.md) непосредственно из кода. Для создания приложения можно использовать любые средства разработки или Блокнот.

> [!NOTE]
> В установленном пакете PHP должно быть установлено и включено [расширение OpenSSL](http://php.net/openssl).
> 
> 

В этой статье рассказывается, как использовать компоненты службы, которые могут быть вызваны локально в приложении PHP или в коде, работающем в веб-роли, рабочей роли или на веб-сайте Azure.

## <a name="get-the-azure-client-libraries"></a>Получение клиентских библиотек Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Настройка приложения для использования служебной шины
Чтобы использовать интерфейсы API служебной шины, требуется следующее.

1. Ссылка на файл автозагрузчика с использованием оператора [require_once][require-once].
2. Ссылка на любые классы, которые могут использоваться.

В следующем примере показано, как включить файл автозагрузчика и сослаться на класс **ServiceBusService**.

> [!NOTE]
> В этом примере (и других примерах в этой статье) предполагается, что установлены клиентские библиотеки PHP для Azure с помощью Composer. При установке библиотек вручную или в качестве пакета PEAR необходимо использовать ссылку на файл автозагрузчика **WindowsAzure.php**.
> 
> 

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

В приведенных ниже примерах всегда будет отображаться оператор `require_once`, однако ссылки будут приводиться только на классы, которые необходимы для выполнения этого примера.

## <a name="set-up-a-service-bus-connection"></a>Настройка подключения к Service Bus
Для создания клиента служебной шины Azure необходимо сначала сформировать правильную строку подключения в следующем формате:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

где `Endpoint` обычно имеет формат `https://[yourNamespace].servicebus.windows.net`.

Для создания клиента службы Azure необходимо использовать класс **ServicesBuilder**. Вы можете:

* Передать строку подключения напрямую или
* использовать **CloudConfigurationManager (CCM)** для проверки нескольких внешних источников на наличие строки подключения:
  * По умолчанию предоставляется поддержка одного внешнего источника — переменных среды.
  * Можно добавить новые источники, расширив класс **ConnectionStringSource** .

В приведенных здесь примерах строка подключения передается напрямую.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Создание раздела
Операции управления для разделов служебной шины можно выполнять с помощью класса **ServiceBusRestProxy**. Объект **ServiceBusRestProxy** создается с помощью фабричного метода **ServicesBuilder::createServiceBusService** с соответствующей строкой подключения, которая включает в себя разрешения маркеров для управления им.

В приведенном ниже примере показано, как создать экземпляр **ServiceBusRestProxy** и вызвать метод **ServiceBusRestProxy->createTopic** для создания раздела с именем `mytopic` в пространстве имен `MySBNamespace`.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {       
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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
> Можно использовать метод `listTopics` для объектов `ServiceBusRestProxy`, чтобы проверить, существует ли уже раздел с указанным именем в пространстве имен службы.
> 
> 

## <a name="create-a-subscription"></a>Создание подписки
Подписки на разделы также создаются с помощью метода **ServiceBusRestProxy->createSubscription**. Подписки имеют имена и могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

### <a name="create-a-subscription-with-the-default-(matchall)-filter"></a>Создание подписки с фильтром по умолчанию (MatchAll)
Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, то все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем mysubscription и используется фильтр по умолчанию **MatchAll**.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Создание подписок с фильтрами
Кроме того, можно настраивать фильтры, позволяющие определять, какие посылаемые в раздел сообщения должны появляться в рамках конкретной подписки. Самый гибкий тип фильтра, который поддерживается подписками, — это **SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительную информацию о фильтрах SqlFilter см. в описании свойства [SqlFilter.SqlExpression Property][sqlfilter].

> [!NOTE]
> Каждое правило в подписке обрабатывает входящие сообщения независимо, добавляя к подписке свои сообщения о результате. Кроме того, в состав каждой новой подписки по умолчанию входит объект **Правило** с фильтром, который добавляет все сообщения из раздела в подписку. Чтобы получать только сообщения, соответствующие условиям фильтра, необходимо удалить это правило по умолчанию. Правило по умолчанию можно удалить с помощью метода `ServiceBusRestProxy->deleteRule`.
> 
> 

В приведенном ниже примере создается подписка с именем **HighMessages** с фильтром **SqlFilter**, который выбирает только те сообщения, у которых значение настраиваемого свойства **MessageNumber** больше 3 (см. раздел [Отправка сообщений в раздел](#send-messages-to-a-topic) для получения сведений о добавлении настраиваемых свойств в сообщения).

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Обратите внимание, что этот код требует использования дополнительного пространства имен: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Аналогично в следующем примере создается подписка с именем **LowMessages** и фильтром **SqlFilter**, который выбирает только сообщения со значением свойства **MessageNumber**, которое меньше или равно 3.

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Теперь, если сообщение отправляется в раздел `mytopic`, оно всегда будет доставляться получателям, подписанным на подписку раздела `mysubscription`, и выборочно доставляться получателям, подписанным на подписки разделов `HighMessages` и `LowMessages` (в зависимости от содержания сообщения).

## <a name="send-messages-to-a-topic"></a>Отправка сообщений в раздел
Чтобы отправить сообщение в раздел служебной шины, приложение вызывает метод **ServiceBusRestProxy->sendTopicMessage**. В следующем примере кода показано, как отправить сообщение в раздел `mytopic`, созданный ранее в пространстве имен службы `MySBNamespace`.

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
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Сообщения, отправляемые в разделы служебной шины и получаемые из них, — это экземпляры класса **BrokeredMessage**. У объектов **BrokeredMessage** есть набор стандартных свойств и методов (например, **getLabel**, **getTimeToLive**, **setLabel** и **setTimeToLive**), а также свойства, которые можно использовать для хранения настраиваемых свойств приложения. В следующем примере показано, как отправить 5 тестовых сообщений в созданный ранее раздел `mytopic`. Метод **setProperty** используется для добавления настраиваемого свойства (`MessageNumber`) в каждое сообщение. Обратите внимание, что значение свойства `MessageNumber` меняется в каждом сообщении (с помощью этого можно определить, какие подписки его получают, как показано в разделе [Создание подписки](#create-a-subscription)).

```
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Разделы служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Максимальный размер раздела ограничен 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину][Квоты на служебную шину].

## <a name="receive-messages-from-a-subscription"></a>Получение сообщений из подписки
Оптимальным способом получения сообщений из подписки является метод **ServiceBusRestProxy->receiveSubscriptionMessage**. Полученные сообщения могут работать в двух режимах: **ReceiveAndDelete** (по умолчанию) и **PeekLock**.

В режиме **ReceiveAndDelete** получение осуществляется в рамках одиночной операции. То есть когда служебная шина получает запрос на чтение для сообщения в подписке, сообщение помечается как использованное и возвращается в приложение. Режим **ReceiveAndDelete** представляет собой самую простую модель, которая лучше всего работает в ситуациях, когда приложение может не обрабатывать сообщение при сбое. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В режиме **PeekLock** процесс получения становится двухэтапной операцией, что позволяет поддерживать приложения, не устойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), служебная шина завершает второй этап процесса получения, передавая полученное сообщение в метод **ServiceBusRestProxy->deleteMessage**. Когда служебная шина обнаруживает вызов **deleteMessage** сообщение помечается как используемое и удаляется из очереди.

В следующем примере показывается, как получать и обрабатывать сообщения с помощью режима **PeekLock** (не является режимом по умолчанию). 

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to:-handle-application-crashes-and-unreadable-messages"></a>Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений
служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать для полученного сообщения метод **unlockMessage** (вместо метода **deleteMessage**). После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), служебная шина разблокирует сообщение автоматически и сделает его доступным для приема.

Если сбой приложения происходит после обработки сообщения, но перед отправкой запроса **deleteMessage** это сообщение будет повторно доставлено в приложение после его перезапуска. Часто такой подход называют **Обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью метода **getMessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="delete-topics-and-subscriptions"></a>Удаление разделов и подписок
Чтобы удалить раздел или подписку, используйте методы **ServiceBusRestProxy->deleteTopic** или **ServiceBusRestProxy->deleteSubscripton** соответственно. Обратите внимание, что при удалении раздела также удаляются все подписки, зарегистрированные в этом разделе.

Следующий пример показывает, как удалить раздел `mytopic` и зарегистрированные в нем подписки.

```
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {       
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

С помощью метода **deleteSubscription** можно удалить подписку независимо.

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Дальнейшие действия
Вы ознакомились с основами использования очередей служебной шины. Дополнительные сведения см. в статье [Очереди, разделы и подписки служебной шины][].

[Очереди, разделы и подписки]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[Квоты на служебную шину]: service-bus-quotas.md



<!--HONumber=Oct16_HO2-->


