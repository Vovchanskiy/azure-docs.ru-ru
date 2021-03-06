---
title: Добавление соединителя Twilio в приложения логики | Microsoft Docs
description: Обзор соединителя Twilio с параметрами API REST
services: ''
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia

---
# Начало работы с соединителем Twilio
Подключитесь к Twilio для отправки и получения глобальных SMS-, MMS- и IP-сообщений.

> [!NOTE]
> Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview.
> 
> 

С помощью Twilio вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Twilio;
* использовать действия для получения сообщений, вывода списка сообщений и выполнения многих других задач. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, новое полученное сообщение Twilio можно использовать в рабочем процессе служебной шины.

Сведения о добавлении операции в приложения логики см. в статье [Создание приложения логики](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Триггеры и действия
Соединитель Twilio позволяет выполнять следующие действия. Триггеры отсутствуют.

| триггеры; | Действия |
| --- | --- |
| None |<ul><li>Получение сообщения</li><li>Вывод списка сообщений</li><li>Отправка сообщения</li></ul> |

Все соединители поддерживают данные в форматах JSON и XML.

## Создание подключения к Twilio
При добавлении этого соединителя в приложения логики введите указанные ниже значения Twilio:

| Свойство | Обязательно | Описание |
| --- | --- | --- |
| Идентификатор учетной записи |Да |Введите идентификатор своей учетной записи Twilio |
| Маркер доступа |Да |Введите свой маркер доступа Twilio |

> [!INCLUDE [Шаги по созданию подключения к Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Если у вас нет маркера доступа, прочитайте в статье про [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) о том, как его создать.

> [!TIP]
> Это подключение к Twilio можно использовать в других приложениях логики.
> 
> 

## Справочник по REST API Swagger
#### Эта документация предназначена для версии 1.0
### Получение сообщения
Возвращает одно сообщение, определяемое предоставленным идентификатором сообщения. ```GET: /Messages/{MessageId}.json```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| MessageId |string |Да |path |Нет |Идентификатор сообщения |

### Ответ
| Имя | Описание |
| --- | --- |
| 200 |Операция выполнена успешно |
| 400 |Ошибка запроса |
| 404 |Сообщение не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

### Вывод списка сообщений
Возвращает список сообщений, связанных с вашей учетной записью. ```GET: /Messages.json```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| Кому |string |Нет |запрос |Нет |На номер телефона |
| Из |string |Нет |запрос |Нет |С номера телефона |
| DateSent |string |Нет |запрос |Нет |Отображение только сообщений, отправленных в указанную дату (в формате GMT), заданную в виде ГГГГ-ММ-ДД. Пример: DateSent = 2009-07-06. Можно также указать неравенство, например DateSent < = ГГГГ-ММ-ДД для сообщений, отправленных в полночь или до полуночи указанной даты, и DateSent > = ГГГГ-ММ-ДД для сообщений, отправленных в полночь или после полуночи указанной даты. |
| PageSize |целое число |Нет |запрос |50 |Количество ресурсов, возвращаемых на каждой странице списка. Значение по умолчанию — 50. |
| Страница |целое число |Нет |запрос |0 |Номер страницы. Значение по умолчанию — 0. |

### Ответ
| Имя | Описание |
| --- | --- |
| 200 |Операция выполнена успешно |
| 400 |Ошибка запроса |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

### Отправка сообщения
Отправляет новое сообщение на номер мобильного телефона. ```POST: /Messages.json```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| sendMessageRequest | |Да |текст |Нет |Отправляемое сообщение |

### Ответ
| Имя | Описание |
| --- | --- |
| 200 |Операция выполнена успешно |
| 400 |Ошибка запроса |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка |
| по умолчанию |Операция завершилась ошибкой. |

## Определения объектов
#### SendMessageRequest: модель запроса для операции отправки сообщения
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| from |string |Да |
| значение |string |Да |
| текст |string |Да |
| media\_url |array |Нет |
| status\_callback |string |Нет |
| messaging\_service\_sid |string |Нет |
| application\_sid |string |Нет |
| max\_price |string |Нет |

#### Message: модель для сообщения
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| текст |string |Нет |
| from |string |Нет |
| значение |string |Нет |
| status |string |Нет |
| sid |string |Нет |
| account\_sid |string |Нет |
| api\_version |string |Нет |
| num\_segments |string |Нет |
| num\_media |string |Нет |
| date\_created |string |Нет |
| date\_sent |string |Нет |
| date\_updated |string |Нет |
| direction |string |Нет |
| error\_code |string |Нет |
| error\_message |string |Нет |
| price |string |Нет |
| price\_unit |string |Нет |
| uri |string |Нет |
| subresource\_uris |array |Нет |
| messaging\_service\_sid |string |Нет |

#### MessageList: модель ответа для операции вывода списка сообщений
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| messages |array |Нет |
| page |целое число |Нет |
| page\_size |целое число |Нет |
| num\_pages |целое число |Нет |
| uri |string |Нет |
| first\_page\_uri |string |Нет |
| next\_page\_uri |string |Нет |
| total |целое число |Нет |
| previous\_page\_uri |string |Нет |

#### IncomingPhoneNumberList: модель ответа для операции вывода списка сообщений
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| incoming\_phone\_numbers |array |Нет |
| page |целое число |Нет |
| page\_size |целое число |Нет |
| num\_pages |целое число |Нет |
| uri |string |Нет |
| first\_page\_uri |string |Нет |
| next\_page\_uri |string |Нет |

#### AddIncomingPhoneNumberRequest: модель запроса для операции добавления входящего номера
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| PhoneNumber |string |Да |
| AreaCode |string |Нет |
| FriendlyName |string |Нет |

#### IncomingPhoneNumber: входящий телефонный номер
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| phone\_number |string |Нет |
| friendly\_name |string |Нет |
| sid |string |Нет |
| account\_sid |string |Нет |
| date\_created |string |Нет |
| date\_updated |string |Нет |
| capabilities |не определено |Нет |
| status\_callback |string |Нет |
| status\_callback\_method |string |Нет |
| api\_version |string |Нет |

#### Capabilities: возможности номеров телефонов
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| mms |Логическое |Нет |
| sms |Логическое |Нет |
| voice |Логическое |Нет |

#### AvailablePhoneNumbers: доступные номера телефонов
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| phone\_number |string |Нет |
| friendly\_name |string |Нет |
| lata |string |Нет |
| latitude |string |Нет |
| longitude |string |Нет |
| postal\_code |string |Нет |
| rate\_center |string |Нет |
| region |string |Нет |
| MMS |Логическое |Нет |
| SMS |Логическое |Нет |
| voice |Логическое |Нет |

#### UsageRecords: класс записей об использовании
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| category |string |Нет |
| usage |string |Нет |
| usage\_unit |string |Нет |
| description |string |Нет |
| price |number |Нет |
| price\_unit |string |Нет |
| count |string |Нет |
| count\_unit |string |Нет |
| start\_date |string |Нет |
| end\_date |string |Нет |

## Дальнейшие действия
[Создайте приложение логики](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0921_2016-->