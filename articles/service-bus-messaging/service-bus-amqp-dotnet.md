---
title: Служебная шина и .NET с протоколом AMQP 1.0 | Microsoft Docs
description: Использование служебной шины на платформе .NET с протоколом AMQP
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: sethm

---
# <a name="using-service-bus-from-.net-with-amqp-1.0"></a>Использование служебной шины на платформе .NET с протоколом AMQP 1.0
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## <a name="downloading-the-service-bus-sdk"></a>Загрузка пакета SDK служебной шины.
Поддержка AMQP 1.0 доступна в пакете SDK служебной шины версии 2.1 или более поздней. Чтобы гарантировать использование последней версии, скачайте части служебной шины с [NuGet][NuGet].

## <a name="configuring-.net-applications-to-use-amqp-1.0"></a>Настройка приложений .NET для использования протокола AMQP 1.0
По умолчанию клиентская библиотеке служебной шины .NET взаимодействует со службой служебной шины, используя специализированный протокол на базе SOAP. Чтобы использовать протокол AMQP 1.0 вместо протокола по умолчанию, необходимо явно настроить строку подключения служебной шины, как описано в следующем разделе. Помимо этих изменений, код приложения остается без изменений при использовании AMQP 1.0.

Текущий выпуск содержит некоторые функции интерфейса API, не поддерживаемые при использовании AMQP. Эти неподдерживаемые функции перечислены ниже в разделе [Неподдерживаемые функции, ограничения и различия в поведении](#unsupported-features-restrictions-and-behavioral-differences). Кроме того, при использовании AMQP отличается значение некоторых дополнительных параметров конфигурации.

### <a name="configuration-using-app.config"></a>Настройка с помощью файла App.config
Рекомендуется, чтобы приложения использовали для хранения настроек файл конфигурации App.config. Если вы используете приложения служебной шины, в файле App.config можно хранить значение параметра строки подключения **ConnectionString** служебной шины. Ниже приводится пример файла App.config:

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
        </appSettings>
    </configuration>

Значение параметра `Microsoft.ServiceBus.ConnectionString` — это строка подключения служебной шины, используемая для настройки подключения к шине. Используется следующий формат:

    Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Здесь `[namespace]` и `SharedAccessKey` получены с [портала Azure][]. Дополнительные сведения см. в статье [Начало работы с очередями служебной шины][Начало работы с очередями служебной шины].

Если вы используете AMQP, добавьте строку подключения с `;TransportType=Amqp`. Эта запись сообщает клиентской библиотеке о создании подключения к служебной шине с помощью протокола AMQP 1.0.

## <a name="message-serialization"></a>Сериализация сообщений
Если вы используете протокол по умолчанию, в клиентской библиотеке .NET по умолчанию используется тип [DataContractSerializer][DataContractSerializer] для сериализации экземпляра [BrokeredMessage][BrokeredMessage]. Это делает возможным транспорт между клиентской библиотекой и службой служебной шины. При использовании транспортного режима AMQP клиентская библиотека применяет систему типов AMQP для сериализации [сообщения в брокере][BrokeredMessage] в сообщение AMQP. Сериализация позволяет получать и распознавать сообщение принимающим приложением, выполняемым на разных платформах. Например, это может быть приложение Java, которое использует JMS API для доступа к служебной шине.

Создавая экземпляр [BrokeredMessage][BrokeredMessage], вы можете передать объект .NET конструктору в качестве параметра (как текст сообщения). Для объектов, которые могут быть сопоставлены с простыми типами AMQP, текст сообщения сериализуется в типы данных AMQP. Если объект не удается непосредственно сопоставить с простым типом AMQP (когда пользовательский тип определяется приложением), объект сериализуется с использованием [DataContractSerializer][DataContractSerializer], а сериализованные байты отправляются в сообщении данных AMQP.

Для упрощения взаимодействия с клиентами, отличными от .NET, используйте только те типы .NET, которые могут быть сериализованы непосредственно в типы AMQP (как текст сообщения). В таблице ниже приведены эти типы, а также соответствующие им типы AMQP.

| Тип объекта текста .NET | Сопоставленный тип AMQP | Тип раздела текста AMQP |
| --- | --- | --- |
| bool |Логическое |Значение AMQP |
| byte |ubyte |Значение AMQP |
| ushort |ushort |Значение AMQP |
| uint |uint |Значение AMQP |
| ulong |ulong |Значение AMQP |
| sbyte |byte |Значение AMQP |
| short |short |Значение AMQP |
| int |int |Значение AMQP |
| длинное целое число |длинное целое число |Значение AMQP |
| float; |float; |Значение AMQP |
| double |double |Значение AMQP |
| decimal |decimal128 |Значение AMQP |
| char; |char; |Значение AMQP |
| DateTime |Timestamp |Значение AMQP |
| Guid |uuid |Значение AMQP |
| byte[] |binary; |Значение AMQP |
| string |string |Значение AMQP |
| System.Collections.IList |list |Значение AMQP. В коллекции могут содержаться только элементы, которые определены в этой таблице. |
| System.Array |array |Значение AMQP. В коллекции могут содержаться только элементы, которые определены в этой таблице. |
| System.Collections.IDictionary |map |Значение AMQP. В коллекции могут содержаться только элементы, которые определены в этой таблице. Примечание: поддерживаются только строковые ключи. |
| URI |Описанный тип string (см. таблицу ниже) |Значение AMQP |
| DateTimeOffset |Описанный тип long (см. таблицу ниже) |Значение AMQP |
| TimeSpan |Описанный тип long (см. таблицу ниже) |Значение AMQP |
| Поток |binary; |Данные AMQP (может быть несколько). Разделы данных содержат необработанные байты из объекта Stream. |
| Другой объект |binary; |Данные AMQP (может быть несколько). Содержит сериализованные двоичные данные объекта, который использует DataContractSerializer или предоставленный приложением сериализатор. |

| Тип .NET | Сопоставленный описанный тип AMQP | Примечания |
| --- | --- | --- |
| URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| Datetimeoffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` |TimeSpan.Ticks |

## <a name="unsupported-features,-restrictions,-and-behavioral-differences"></a>Неподдерживаемые функции, ограничения и различия в поведении
Следующие функции API служебной шины .NET в настоящее время не поддерживаются при использовании AMQP:

* Транзакции
* Отправка через место назначения передачи

Существуют также некоторые различия в поведении API .NET служебной шины при использовании AMQP в сравнении с протоколом по умолчанию:

* Свойство [OperationTimeout][OperationTimeout] игнорируется.
* `MessageReceiver.Receive(TimeSpan.Zero)` реализуется в виде `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

## <a name="controlling-amqp-protocol-settings"></a>Параметры управления протоколом AMQP
API-интерфейсы .NET предоставляют несколько параметров для управления поведением протокола AMQP.

* **MessageReceiver.PrefetchCount**: определяет начальные разрешения, которые применяются к ссылке. Значение по умолчанию — 0.
* **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: определяет максимальный размер кадра AMQP, доступный во время согласования при открытии подключения. Значение по умолчанию: 65 536 байт.
* **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: если передачу данных можно разбить на пакеты, это значение определяет максимальную задержку отправки стратегий обработки. По умолчанию наследуется отправителями и получателями. Отдельные отправители или получатели могут переопределить значение по умолчанию, составляющее 20 миллисекунд.
* **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: определяет, устанавливаются ли AMQP-подключения с использованием протокола SSL. Значение по умолчанию — **true**.

## <a name="next-steps"></a>Дальнейшие действия
Хотите узнать больше? Перейдите по следующим ссылкам:

* [Протокол AMQP служебной шины — обзор]
* [Поддержка AMQP 1.0 для секционированных очередей и разделов служебной шины]
* [Протокол AMQP служебной шины для Windows Server]

[Начало работы с очередями служебной шины]: service-bus-dotnet-get-started-with-queues.md
[DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
[Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
[OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[портал Azure]: https://portal.azure.com
[Протокол AMQP служебной шины — обзор]: service-bus-amqp-overview.md
[Поддержка AMQP 1.0 для секционированных очередей и разделов служебной шины]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[Протокол AMQP служебной шины для Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx


<!--HONumber=Oct16_HO2-->


