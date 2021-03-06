---
title: Руководство разработчика. Обмен сообщениями | Microsoft Docs
description: Руководство разработчика Центра Интернета вещей Azure. Передача сообщений между устройством и облаком
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett

---
# <a name="send-and-receive-messages-with-iot-hub"></a>Отправка и получение сообщений в Центре Интернета вещей
## <a name="overview"></a>Обзор
Центр Интернета вещей предоставляет следующие примитивы обмена сообщениями для взаимодействия с устройством:

* [С устройства в облако][lnk-d2c]: с устройства в серверную часть приложения.
* [Из облака на устройство][lnk-c2d]: из серверной части приложения (*службы* или *облака*).

Основные свойства функции обмена сообщениями в центре IoT — надежность и устойчивость сообщений. Эти свойства сохраняются, если на стороне устройства прерывается подключение и если наступает момент пиковой загрузки на стороне облака при обработке событий. Центр IoT гарантирует *как минимум однократную* доставку при двухстороннем обмене сообщениями между устройством и облаком.

Центр Интернета вещей поддерживает [несколько протоколов, обращенных к устройству][lnk-protocols] (например, MQTT, AMQP и HTTP). Для поддержки эффективного взаимодействия по протоколам Центр Интернета вещей задает [общий формат сообщений][lnk-message-format], поддерживаемый всеми обращенными к устройству протоколами.

Центр Интернета вещей предоставляет [конечную точку, совместимую с концентраторами событий][lnk-compatible-endpoint], для чтения сообщений, которые отправлены с устройства в облако и получены от концентратора, в приложениях серверной части.

### <a name="when-to-use"></a>Сценарии использования
Обмен сообщениями — это основная возможность Центра Интернета вещей. Используйте ее при отправке сообщений с устройства в серверную часть или из серверной части на устройство.

Сравнение услуг, предоставляемых Центром Интернета вещей и концентраторами событий, см. в статье [Сравнение центра IoT и концентраторов событий][lnk-compare].

## <a name="device-to-cloud-messages"></a>Отправка сообщений с устройства в облако
Отправка сообщений с устройства в облако осуществляется через обращенную к устройству конечную точку (**/devices/{deviceId}/messages/events**). Сообщения, отправляемые с устройства в облако, поступают в серверную службу через конечную точку, обращенную к службе (**/messages/events**), которая совместима с [концентраторами событий][lnk-event-hubs]. Таким образом, чтобы получать сообщения, отправляемые с устройства в облако, можно использовать стандартную [интеграцию концентраторов событий и пакетов SDK][lnk-compatible-endpoint].

Реализация отправки сообщений с устройства в облако в Центре Интернета вещей похожа на реализацию в [концентраторах событий][lnk-event-hubs]. При этом сообщения Центра Интернета вещей, отправляемые с устройства в облако, больше похожи на *события* концентраторов событий, чем на *сообщения* [служебной шины][lnk-servicebus].

Далее перечислены следствия этой реализации.

* Подобно событиям концентраторов событий, сообщения, отправляемые с устройства в облако, устойчивы и хранятся в Центре Интернета вещей до семи дней (см. раздел [Параметры конфигурации сообщений, отправляемых с устройства в облако][lnk-d2c-configuration]).
* Сообщения, отправляемые с устройства в облако, секционируются в фиксированном наборе секций, заданном во время создания (см. раздел [Параметры конфигурации сообщений, отправляемых с устройства в облако][lnk-d2c-configuration]).
* Подобно тому, как это происходит с концентраторами событий, клиенты, читающие сообщения, отправляемые с устройства в облако, должны работать с секциями и назначением контрольных точек. См. раздел [о потреблении событий в статье о концентраторах событий][lnk-event-hubs-consuming-events].
* Подобно событиям концентраторов событий, размер сообщений, поступающих с устройства в облако, не может превышать 256 КБ, и их можно объединять в пакеты, чтобы оптимизировать отправку. Размер пакетов не может превышать 256 КБ, а сами пакеты не могут содержать более 500 сообщений.

При этом сообщения центра IoT, отправляемые с устройства в облако, и сообщения, отправляемые концентраторами событий, существенно различаются.

* Как объяснено в разделе [Управление доступом к Центру Интернета вещей][lnk-devguide-security], Центр Интернета вещей обеспечивает аутентификацию и контроль доступа для каждого устройства.
* Центр Интернета вещей позволяет одновременно подключать миллионы устройств (см. статью [Квоты и регулирование][lnk-quotas]), а концентраторы событий позволяют выполнять максимум 5000 подключений по протоколу AMQP в одном пространстве имен.
* Центр IoT не позволяет выполнять произвольное секционирование с помощью **PartitionKey**. Сообщения, отправляемые с устройства в облако, секционируются по исходному идентификатору **deviceId**.
* Масштабирование центра IoT немного отличается от масштабирования концентраторов событий. Дополнительную информацию см. в статье [Масштабирование центра IoT][lnk-guidance-scale].

> [!NOTE]
> Невозможно заменить Центр Интернета вещей на концентраторы событий во всех сценариях. Например, иногда при вычислениях для обработки событий может понадобиться перераспределить события на основе другого свойства или поля, чтобы проанализировать потоки данных. В этом сценарии с помощью концентратора событий можно отделить друг от друга два раздела конвейера обработки потока. Дополнительные сведения см. в разделе *Разделы* статьи [Общие сведения о концентраторах событий Azure][lnk-eventhub-partitions].
> 
> 

Сведения об использовании обмена сообщениями с устройства в облако см. в статье [IoT Hub APIs and SDKs][lnk-sdks] (Интерфейсы API и пакеты SDK Центра Интернета вещей).

> [!NOTE]
> При использовании протокола HTTP для отправки сообщений с устройства в облако имена и значения свойств могут содержать только буквенно-цифровые знаки ASCII и знаки ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

### <a name="non-telemetry-traffic"></a>Трафик, передающийся без использования телеметрии
Часто устройства отправляют не только точки данных телеметрии, но и сообщения и запросы, выполнение и обработку которых осуществляет та часть приложения, которая отвечает за бизнес-логику. Например, устройство отправляет критические оповещения, которые должны активировать определенное действие в серверной части, или отвечает на команды, отправленные из серверной части.

Дополнительные сведения о том, как лучше всего обработать такие сообщения, см. в статье [Tutorial: How to process IoT Hub device-to-cloud messages][lnk-d2c-tutorial] (Руководство. Обработка сообщений, отправляемых с устройства в облако, с помощью Центра Интернета вещей).

### <a name="device-to-cloud-configuration-options"></a>Параметры конфигурации сообщений, отправляемых с устройства в облако
Центр IoT позволяет управлять сообщениями, отправляемыми с устройства в облако, с помощью приведенных ниже свойств.

* **Количество секций**. Это свойство можно задать во время создания, чтобы определить количество разделов для приема событий сообщений, отправляемых с устройства в облако.
* **Время хранения**. Это свойство определяет время хранения сообщений, отправляемых с устройства в облако. Значение по умолчанию — один день, но это значение можно увеличить до семи дней.

Кроме того, как и концентраторы событий, центр IoT предоставляет возможность управлять группами потребителей в конечной точке, которая получает сообщения, отправляемые с устройства в облако.

Вы можете изменить все эти свойства программно (с помощью [интерфейсов API поставщика ресурсов в Центре Интернета вещей Azure][lnk-resource-provider-apis]) или на [портале Azure][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Свойства для защиты от спуфинга
Чтобы избежать спуфинга устройств при работе с сообщениями, отправляемыми с устройства в облако, центр IoT отмечает все сообщения такими свойствами:

* **ConnectionDeviceId;**
* **ConnectionDeviceGenerationId;**
* **ConnectionAuthMethod.**

Первые два свойства содержат параметры **deviceId** и **generationId** исходного устройства (согласно разделу [Свойства удостоверений устройств][lnk-device-properties]).

Свойство **ConnectionAuthMethod** содержит сериализованный объект JSON, имеющий такие свойства:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>Получение сообщений из облака на устройство
Сообщения из облака на устройство можно отправлять через конечную точку, обращенную к службе (**/messages/devicebound**). Устройства получают их через конечную точку конкретного устройства (**/devices/{deviceId}/messages/devicebound**).

Каждое сообщение, отправляемое из облака на устройство, адресовано отдельному устройству, то есть для свойства **to** задается значение **/devices/{deviceId}/messages/devicebound**.

> [!IMPORTANT]
> Одна очередь устройства содержит не более 50 сообщений, отправляемых из облака на устройство. Попытка отправить на устройство большее количество сообщений приведет к ошибке.
> 
> [!NOTE]
> При отправке сообщений из облака на устройство имена и значения свойств могут содержать только буквенно-цифровые знаки ASCII и знаки ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

### <a name="message-lifecycle"></a>Жизненный цикл сообщения
Чтобы гарантировать как минимум однократную доставку, Центр Интернета вещей помещает в очереди устройств сообщения, отправляемые из облака на устройство. Сами устройства должны явно подтвердить *завершение* доставки, чтобы центр IoT мог удалить сообщения из очереди. Это гарантирует устойчивость к сбоям подключения и ошибкам устройств.

На следующей схеме показан жизненный цикл сообщений, отправляемых с облака в устройство.

![Жизненный цикл сообщений, отправляемых из облака на устройство][img-lifecycle]

Когда служба отправляет сообщение, считается, что оно *поставлено в очередь*. Когда устройству нужно *получить* сообщение, Центр Интернета вещей *блокирует* сообщение (устанавливает состояние **Невидимо**), чтобы другие потоки на том же устройстве начали получать другие сообщения. Когда поток устройства завершает обработку сообщения, он уведомляет центр IoT путем *завершения* сообщения.

Устройство также может:

* *отклонить* сообщение, в результате чего центр IoT переводит его в состояние **Deadlettered** (Не доставлено); Примечание. Устройства, подключающиеся по протоколу MQTT, не могут отклонять сообщения, отправляемые из облака на устройство.
* *прервать* сообщение, в результате чего центр IoT помещает его в очередь с состоянием **Enqueued**(Поставлено в очередь).

При обработке сообщения потоком может произойти ошибка без уведомления центра IoT. В этом случае состояние сообщения автоматически меняется с **Невидимо** на **Enqueued** (Поставлено в очередь) после *истечения срока видимости (или блокировки)*. Значение по умолчанию — одна минута.

Состояние сообщения может изменяться с **Enqueued** (Поставлено в очередь) на **Невидимо** и наоборот столько раз, сколько указано в свойстве **Максимальное число доставок** Центра Интернета вещей. После выполнения такого количества изменений Центр Интернета вещей устанавливает для сообщения состояние **Deadlettered** (Не доставлено). Таким же образом Центр Интернета вещей устанавливает для сообщения состояние **Deadlettered** (Не доставлено) после истечения срока действия сообщения (см. раздел [Срок жизни][lnk-ttl]).

Руководство по сообщениям, отправляемым из облака на устройство, см. в статье [Tutorial: How to send cloud-to-device messages with IoT Hub][lnk-c2d-tutorial] (Руководство. Обработка сообщений, отправляемых из облака на устройство, с помощью Центра Интернета вещей). Справочную информацию о том, как разные интерфейсы API и пакеты SDK предоставляют функцию отправки сообщений из облака на устройство, см. в статье [Интерфейсы API и пакеты SDK Центра Интернета вещей][lnk-sdks].

> [!NOTE]
> Обычно, если потеря сообщения, отправляемого с облака в устройство, никак не влияет на логику приложения, эти сообщения завершаются. Например, когда содержимое сообщения успешно сохранено в локальном хранилище, или когда операция успешно выполнена. Сообщение может также содержать временные сведения, потеря которых не влияет на функциональность приложения. Иногда при работе с долгосрочной задачей сообщение, отправляемое с облака в устройство, можно завершить после того, как описание задачи сохранится в локальном хранилище. После этого на различных этапах выполнения задачи в серверную часть приложения отправляется одно или несколько уведомлений.
> 
> 

### <a name="message-expiration-(time-to-live)"></a>Срок действия сообщения (срок жизни)
Каждое сообщение из облака на устройство имеет срок действия. Его может задавать служба (в свойстве **ExpiryTimeUtc**) или Центр Интернета вещей, в котором задан *срок жизни* по умолчанию, указанный в соответствующем свойстве Центра Интернета вещей. См. раздел [Параметры конфигурации сообщений, отправляемых из облака на устройство][lnk-c2d-configuration].

> [!NOTE]
> Распространенный способ воспользоваться сроком действия, чтобы сообщения не отправлялись на отключенные устройства — задать короткий срок жизни. Этот подход позволяет добиться того же результата, что и поддержание состояния подключения устройства, но при этом обладает большей эффективностью. При запросе подтверждения сообщений Центр Интернета вещей сообщает о том, какие устройства могут получать сообщения, а какие находятся в автономном режиме или в состоянии сбоя.
> 
> 

### <a name="message-feedback"></a>Отзыв на сообщение
При отправке сообщений с облака в устройство служба может запросить отзыв на каждое сообщение, уведомляющий о его конечном состоянии.

* Если для свойства **Ack** задать значение **positive**, то Центр Интернета вещей создаст отзыв на сообщение, только если сообщение, отправляемое из облака на устройство, имеет состояние **Завершено**.
* Если для свойства **Ack** задать значение **negative**, то Центр Интернета вещей создаст отзыв на сообщение, только если сообщение, отправляемое из облака на устройство, имеет состояние **Deadletterd** (Не доставлено).
* Если для свойства **Ack** задать значение **full**, то Центр Интернета вещей создаст отзыв на сообщение в любом случае.

> [!NOTE]
> Если свойство **Ack** имеет значение **full** и отзыв не получен, то это означает, что срок действия отзыва истек. Служба не знает, что случилось с исходным сообщением. На практике служба должна убедиться, что может обработать отзыв до истечения срока его действия. Максимальный срок действия составляет два дня, и этого времени достаточно для повторного запуска службы при сбое.
> 
> 

Как описано в разделе [Endpoints][lnk-endpoints] (Конечные точки), Центр Интернета вещей предоставляет отзывы в виде сообщений через конечную точку, обращенную к службе (**/messages/servicebound/feedback**). Семантика получения отзыва идентична семантике, используемой для сообщений, отправляемых из облака в устройство, с тем же [жизненным циклом сообщения][lnk-lifecycle]. По возможности отзывы на сообщения группируются в одно сообщение, имеющее приведенный ниже формат:

| Свойство | Описание |
| --- | --- |
| EnqueuedTime |Метка времени, указывающая, когда было создано сообщение. |
| UserId |`{iot hub name}` |
| ContentType |`application/vnd.microsoft.iothub.feedback.json` |

Основная часть — это сериализованный массив записей JSON, каждая из которых имеет следующие свойства:

| Свойство | Описание |
| --- | --- |
| EnqueuedTimeUtc |Метка времени, указывающая, когда отобразился результат сообщения. Например, устройство завершило его или истек срок его действия. |
| OriginalMessageId |**MessageId** сообщения, которое отправляется из облака на устройство и к которому относится эта информация. |
| StatusCode |Требуется целое число. Используется в отзывах, созданных центром IoT. <br/> 0 — успех <br/> 1 — срок действия сообщения истек <br/> 2 — превышено максимальное число доставок <br/> 3 — сообщение отклонено |
| Описание |Строковые значения **StatusCode**. |
| deviceId |**DeviceId** целевого устройства, которому отправляется сообщение из облака и к которому относится этот отзыв. |
| DeviceGenerationId |**DeviceGenerationId** целевого устройства, которому отправляется сообщение из облака и к которому относится этот отзыв. |

> [!IMPORTANT]
> Служба должна указать идентификатор **MessageId** для сообщения, отправляемого из облака на устройство, чтобы соотнести свой отзыв с исходным сообщением.
> 
> 

Ниже приведен пример текста сообщения отзыва.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>Параметры конфигурации сообщений, отправляемых из облака на устройство
Каждый центр IoT предоставляет следующие параметры конфигурации для отправки сообщений из облака на устройство.

| Свойство | Описание | Диапазон и значение по умолчанию |
| --- | --- | --- |
| defaultTtlAsIso8601 |Заданный по умолчанию срок жизни для сообщений, отправляемых из облака на устройство. |Интервал ISO_8601 — до 2 устройств (минимум 1 минута). Значение по умолчанию — 1 час. |
| maxDeliveryCount |Лимит доставки для очереди доставки сообщений, отправляемых из облака на устройство, для каждого отдельного устройства. |От 1 до 100. Значение по умолчанию — 10. |
| feedback.ttlAsIso8601 |Хранение отзывов, направленных службе. |Интервал ISO_8601 — до 2 устройств (минимум 1 минута). Значение по умолчанию — 1 час. |
| feedback.maxDeliveryCount |Лимит доставок для очереди отзывов. |От 1 до 100. Значение по умолчанию — 100. |

Дополнительные сведения см. в разделе [Create IoT hubs][lnk-portal] (Создание Центров Интернета вещей).

## <a name="read-device-to-cloud-messages"></a>Чтение сообщений, отправленных с устройства в облако
Центр Интернета вещей предоставляет серверным службам конечную точку для чтения сообщений, отправленных с устройства в облако, которые получены от концентратора. Конечная точка совместима с концентраторами событий, поэтому можно использовать любой из механизмов для чтения сообщений, который поддерживает служба концентраторов событий.

При использовании [пакета SDK служебной шины Azure для .NET][lnk-servicebus-sdk] или [узла обработчика событий концентратора событий][lnk-eventprocessorhost] вы можете использовать любые строки подключения к Центру Интернета вещей с нужными разрешениями. А затем использовать **messages/events** в качестве имени концентратора событий.

При использовании пакетов SDK (или интеграции продуктов), которые не поддерживают Центр Интернета вещей, следует получить конечную точку, совместимую с концентраторами событий, и имя концентратора событий в разделе параметров Центра Интернета вещей на [портале Azure][lnk-management-portal]:

1. В колонке центра IoT щелкните **Сообщения**.
2. В разделе **Параметры раздела "Из устройства в облако"** отобразятся следующие значения: **Конечная точка, совместимая с концентратором событий**, **Имя, совместимое с концентратором событий** и **Разделы**.
   
    ![Параметры отправки сообщений с устройства в облако][img-eventhubcompatible]

> [!NOTE]
> Если для пакета SDK требуется указать **имя узла** или **пространство имен**, то удалите схему из **конечной точки, совместимой с концентратором событий**. Например, если конечная точка, совместимая с концентратором событий, — **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, то **именем узла** будет **iothub-ns-myiothub-1234.servicebus.windows.net**, а **пространством имен** — **iothub-ns-myiothub-1234**.
> 
> 

Затем можно использовать любую политику безопасности общего доступа с разрешениями **ServiceConnect** , которые позволяют подключаться к указанному концентратору событий.

Если нужно создать строку подключения к концентратору событий с помощью указанных выше сведений, можно использовать следующий образец:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Ниже приведен список пакетов SDK и интеграций, которые можно применять к совместимым с концентратором событий конечным точкам, которые предоставляет центр IoT:

* [клиент концентраторов событий Java;](https://github.com/hdinsight/eventhubs-client)
* [Воронка Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Вы можете просмотреть [источник воронки](https://github.com/apache/storm/tree/master/external/storm-eventhubs) на портале GitHub.
* [интеграция Apache Spark.](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference"></a>Справочные материалы
### <a name="message-format"></a>Формат сообщений
Сообщения центра IoT включают в себя следующие компоненты.

* Набор *свойств системы*. Свойства, которые интерпретирует или задает Центр Интернета вещей. Этот набор определяется предварительно.
* Набор *свойств приложения*. Словарь свойств строки, которые приложение может задать и использовать без необходимости десериализации текста сообщения. Центр IoT никогда не изменяет эти свойства.
* Непрозрачная двоичная основная часть.

Дополнительные сведения о разных способах кодировки сообщений в разных протоколах см. в статье [Интерфейсы API и пакеты SDK Центра Интернета вещей][lnk-sdks].

В следующей таблице указаны системные свойства в сообщениях Центра Интернета вещей.

| Свойство | Описание |
| --- | --- |
| MessageId |Задаваемый пользователем идентификатор сообщения, используемый для шаблонов типа запрос-ответ. Формат: строка с учетом регистра (длиной до 128 знаков), состоящая из букв и цифр в 7-битовом формате ASCII + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Порядковый номер |Число (уникальное для каждой очереди устройства), которое центр IoT назначает каждому сообщению, отправленному из облака на устройство. |
| Кому |Место назначения, которое указывается в сообщениях, отправляемых [из облака на устройство][lnk-c2d]. |
| ExpiryTimeUtc |Дата и время истечения срока действия сообщения. |
| EnqueuedTime |Дата и время получения сообщения центром IoT. |
| CorrelationId |Строковое свойство в сообщении ответа, которое обычно содержит идентификатор сообщения запроса в шаблонах "запрос-ответ". |
| UserId |Идентификатор, используемый для указания источника сообщений. Если сообщения создает центр IoT, для этого параметра задается значение `{iot hub name}`. |
| Ack |Генератор отзывов на сообщения. Это свойство используется в сообщениях, отправляемых из облака в устройство, чтобы запросить у центра IoT отправку отзыва после того, как сообщение будет использовано устройством. Возможные значения: **none** (по умолчанию) — отзывы не создаются, **positive** — отзыв будет получен, когда исходное сообщение будет передано, **negative** — отзыв будет получен, когда истечет срок действия исходного сообщения (или будет превышен лимит доставок) и при этом устройство его не примет, **full** — активированы значения positive и negative. Дополнительные сведения см. в разделе [Отзыв на сообщение][lnk-feedback]. |
| ConnectionDeviceId |Центр IoT устанавливает этот идентификатор в сообщениях, отправляемых с устройства в облако. Содержит идентификатор **deviceId** устройства, отправившего сообщение. |
| ConnectionDeviceGenerationId |Центр IoT устанавливает этот идентификатор в сообщениях, отправляемых с устройства в облако. Он содержит идентификатор **generationId** (см. раздел [Свойства удостоверений устройств][lnk-device-properties]) устройства, отправившего сообщение. |
| ConnectionAuthMethod |Центр IoT устанавливает этот метод проверки подлинности в сообщениях, отправляемых с устройства в облако. Это свойство содержит сведения о методе проверки подлинности, используемом для аутентификации устройства, отправляющего сообщение. Дополнительные сведения см. в разделе [Свойства для защиты от спуфинга][lnk-antispoofing]. |

### <a name="communication-protocols"></a>Протоколы связи
Центр Интернета вещей поддерживает протоколы MQTT, [AMQP][lnk-amqp], AMQP через WebSocket и HTTP/1 для обмена данными на стороне устройства. Ниже в таблице приведены общие рекомендации по выбору протокола.

| Протокол | Когда следует выбрать этот протокол |
| --- | --- |
| MQTT |Используйте на всех устройствах, которые не используют WebSockets. |
| AMQPS |Используйте на собственных или облачных шлюзах, чтобы воспользоваться преимуществами мультиплексирования подключений на устройствах. <br/>  Используйте, если необходимо подключаться к порту 443. |
| HTTPS |Используйте для устройств, которые не поддерживают другие протоколы. |

При выборе протокола для связи на стороне устройства учитывайте следующие факторы:

* **Шаблон сообщения из облака на устройство**. Протокол HTTP/1 не позволяет отправлять данные по инициативе сервере. Таким образом, при использовании протокола HTTP/1 устройства опрашивают центр IoT на предмет наличия сообщений из облака на устройство. Этот подход неэффективен для устройства и Центра Интернета вещей. Согласно текущим рекомендациям для протокола HTTP/1 каждое устройство должно отправлять запросы не реже, чем раз в 25 минут. С другой стороны, протоколы MQTT и AMQP поддерживают отправку сообщений по инициативе сервера при получении сообщений из облака на устройство. Они позволяют незамедлительно передавать сообщения из центра IoT на устройство. Если задержка доставки является проблемой, то лучше использовать протокол AMQP или MQTT. Для редко подключаемых устройств протокол HTTP/1 также работает.
* **Шлюзы поля**. Протоколы HTTP-1 и MQTT не позволяют подключать по TLS-соединению одновременно несколько устройств (на каждом из которых используются собственные учетные данные). Таким образом, эти протоколы не вполне подходят для [ситуаций с использованием полевого шлюза][lnk-azure-gateway-guidance], так как требуют отдельного TLS-подключения между полевым шлюзом и Центром Интернета вещей для каждого устройства, подключаемого к полевому шлюзу.
* **Устройства с небольшим количеством ресурсов**. Библиотеки MQTT и HTTP/1 уступают по объему библиотекам AMQP, а значит, только их можно использовать для устройств с ограниченными ресурсами (например, если размер ОЗУ меньше 1 МБ).
* **Обход сети**. Стандарт MQTT прослушивает порт 8883, что может вызвать проблемы в сетях, закрытых для других протоколов. В подобных случаях используются протоколы HTTP и AMQP (через WebSocket).
* **Объем полезных данных**. AMQP и MQTT — это двоичные протоколы, позволяющие передавать более компактные полезные данные, чем HTTP/1.

> [!NOTE]
> При использовании протокола HTTP/1 каждое устройство должно проверять наличие сообщений, передаваемых из облака на устройство, не реже, чем раз в 25 минут. Тем не менее, во время разработки частота такой проверки может быть выше.
> 
> 

### <a name="port-numbers"></a>Номера портов
Устройства могут взаимодействовать с центром IoT в Azure с использованием различных протоколов. Как правило, выбор протокола определяется конкретными требованиями решения. В следующей таблице перечислены исходящие порты, которые должны быть открыты, чтобы устройство могло воспользоваться определенным протоколом.

| Протокол | Порты |
| --- | --- |
| MQTT |8883 |
| AMQP |5671 |
| AMQP через WebSocket |443 |
| HTTPS |443 |
| LWM2M (Управление устройствами) |5684 |

После создания центра IoT в регионе Azure IP-адрес центра остается неизменным в течение всего времени существования центра. Однако для обеспечения качества обслуживания при перемещении корпорацией Майкрософт центра IoT в другую единицу масштабирования центру назначается новый IP-адрес.

### <a name="notes-on-mqtt-support"></a>Примечания о поддержке протокола MQTT
В центре IoT протокол MQTT 3.1.1 реализован со следующими ограничениями и особенностями:

* **QoS 2 не поддерживается**. Если клиент устройства публикует сообщение с **QoS 2**, то центр IoT закрывает сетевое подключение. Если клиент устройства подписывается на раздел со **вторым уровнем качества обслуживания**, то Центр Интернета вещей присваивает пакету **SUBACK** уровень качества обслуживания не выше первого.
* **Сообщения retain не сохраняются**. Если клиент устройства публикует сообщение с флагом RETAIN, имеющим значение 1, то центр IoT добавляет в сообщение свойство приложения **x-opt-retain** . В этом случае центр IoT не сохранит сообщение, а передаст его во внутреннее приложение.

Дополнительные сведения см. в разделе [Поддержка MQTT в центре Интернета вещей][lnk-devguide-mqtt].

И наконец, обратите внимание на [шлюз протокола Интернета вещей Azure][lnk-azure-protocol-gateway], с помощью которого можно развернуть высокопроизводительный настраиваемый шлюз протокола, который взаимодействует с Центром Интернета вещей. Шлюз протокола Azure IoT позволяет настроить протокол устройства для уже существующих развертываний MQTT или других настраиваемых протоколов. Однако при этом подходе необходимо самостоятельно размещать настраиваемый шлюз протокола и управлять им.

### <a name="additional-reference-material"></a>Дополнительные справочные материалы
Другие справочные статьи в руководстве для разработчиков:

* Статья [Конечные точки Центра Интернета вещей][lnk-endpoints] содержит информацию о конечных точках, которые каждый Центр Интернета вещей предоставляет для операций управления и среды выполнения.
* Статья [Throttling and quotas][lnk-quotas] (Регулирование и квоты) содержит информацию о квотах, применимых к службе Центра Интернета вещей, и ожидаемом поведении регулирования при использовании службы.
* В статье [Пакеты SDK для устройств и служб Центра Интернета вещей][lnk-sdks] указаны различные языковые пакеты SDK, которые можно использовать при разработке приложений устройств и служб, взаимодействующих с Центром Интернета вещей.
* В статье [Язык запросов для двойников, методов и заданий][lnk-query] описывается язык запросов, который можно использовать для получения сведений о двойниках, методах и заданиях устройств из Центра Интернета вещей.
* Статья [Поддержка MQTT в Центре Интернета вещей][lnk-devguide-mqtt] содержит дополнительные сведения о поддержке протокола MQTT в Центре Интернета вещей.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы узнали, как отправлять и получать сообщения с помощью Центра Интернета вещей, вас могут заинтересовать следующие статьи руководства для разработчиков:

* [Upload files from a device][lnk-devguide-upload] (Отправка файлов с устройства)
* [Manage device identities in IoT Hub][lnk-devguide-identities] (Управление сущностями устройства в Центре Интернета вещей)
* [Управление доступом к Центру Интернета вещей][lnk-devguide-security]
* [Use device twins to synchronize state and configurations][lnk-devguide-device-twins] (Синхронизация состояния и конфигураций с помощью двойников устройств)
* [Вызов прямого метода на устройстве][lnk-devguide-directmethods]
* [Планирование заданий на нескольких устройствах][lnk-devguide-jobs]

Если вы хотели бы применить на практике некоторые основные понятия, описанные в этой статье, можно просмотреть следующие руководства по Центру Интернета вещей:

* [Get started with Azure IoT Hub][lnk-getstarted-tutorial] (Начало работы с Центром Интернета вещей Azure)
* [How to send cloud-to-device messages with IoT Hub][lnk-c2d-tutorial] (Отправка сообщений из облака на устройства с помощью Центра Интернета вещей)
* [How to process IoT Hub device-to-cloud messages][lnk-d2c-tutorial] (Обработка сообщений, отправляемых с устройства Центра Интернета вещей в облако)

[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: https://www.amqp.org/
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md


<!--HONumber=Oct16_HO2-->


