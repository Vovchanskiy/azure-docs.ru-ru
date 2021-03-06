---
title: Использование соединителя ресурсов Azure в приложениях логики | Microsoft Docs
description: Из этой статьи вы узнаете, как создать и настроить соединитель ресурсов Azure или приложение API и использовать его в приложении логики в службе приложений Azure.
services: logic-apps
documentationcenter: .net,nodejs,java
author: stepsic-microsoft-com
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/01/2016
ms.author: stepsic

---
# Приступая к работе с соединителем ресурсов Azure: добавление его в приложение логики
> [!NOTE]
> Эта версия статьи предназначена для приложений логики со схемой версии 2014-12-01-preview.
> 
> 

Используйте соединитель ресурсов Azure, чтобы легко управлять ресурсами Azure в приложении логики.

## Создание соединителя ресурсов Azure
Чтобы использовать приложение API соединителя ресурсов Azure, необходимо сначала создать его экземпляр. Это можно сделать как при создании приложения логики, так и путем выбора приложения API соединителя ARM в Azure Marketplace.

Чтобы настроить его, для субъекта-службы нужно задать разрешения на выполнение тех задач, которые вы планируете выполнять в Azure. Все вызовы будут выполняться от имени этого субъекта-службы. Благодаря этому соединитель будет делать только то, что вам нужно и ничего более.

Дэвид Эббо написал об этом отличную [статью в своем блоге](http://blog.davidebbo.com/2014/12/azure-service-principal.html). Прочтите ее и, воспользовавшись приведенными в ней инструкциями, получите **идентификатор клиента**, **код клиента** и **секрет**. Этих трех значений вместе с **идентификатором подписки** достаточно для настройки соединителя.

## Использование соединителя ресурсов Azure в конструкторе приложений логики
### Триггер
В соединителе поддерживаются два триггера.

| Имя | Описание |
| --- | --- |
| Происходит событие |Срабатывает, когда с ресурсом в подписке происходит какое-то событие. |
| Метрика достигает порогового значения |Срабатывает, когда метрика достигает определенного порогового значения. |

### Действие
Точно так же в подписке Azure можно предоставить большое количество действий.

Действия для **групп ресурсов**:

| Имя | Описание |
| --- | --- |
| Перечислить группы ресурсов |Вывод списка всех групп ресурсов в подписке. |
| Получить группу ресурсов |Получение группы ресурсов по ее идентификатору. |
| Создать группу ресурсов |Создание или изменение группы ресурсов. |
| Удалить группу ресурсов |Удаление группы ресурсов. |

Действия для **ресурсов**:

| Имя | Описание |
| --- | --- |
| Перечислить ресурсы |Вывод списка ресурсов в подписке по различным типам фильтров. |
| Получить ресурс |Получение одного ресурса по его идентификатору. |
| Создать и изменить ресурс |Создание нового или изменение существующего ресурса. Необходимо указать все свойства ресурса. |
| Действие с ресурсом |Выполнение других действий с ресурсом. Необходимо знать имя действия и полезные данные, которые оно принимает (если они есть). |
| Удалить ресурс |Удаление ресурса. |

Действия для **поставщиков ресурсов**:

| Имя | Описание |
| --- | --- |
| Перечислить поставщиков ресурсов |Вывод списка всех поставщиков ресурсов, доступных в подписке. |

Действия для **развертываний групп ресурсов**:

| Имя | Описание |
| --- | --- |
| Перечислить развертывания |Вывод списка всех развертываний в группе ресурсов. |
| Получить развертывание |Получение шаблона развертывания по его идентификатору. |
| Создать развертывание |Создание развертывания группы ресурсов с помощью шаблона. |

Действия для **событий** с ресурсами:

| Имя | Описание |
| --- | --- |
| Получить события |Получение событий в подписке или событий для определенного ресурса. |

Действия для **метрик** ресурсов:

| Действие | Описание |
| --- | --- |
| Получить метрики |Получение метрики для идентификатора ресурса. |

## Дополнительные возможности соединителя
После создания соединителя его можно добавить в бизнес-процесс с помощью приложения логики. См. статью [Что такое приложения логики?](app-service-logic-what-are-logic-apps.md)

> [!NOTE]
> Если вы хотите начать работу с Azure Logic Apps до создания учетной записи Azure, перейдите на веб-сайт [пробного использования приложений логики](https://tryappservice.azure.com/?appservice=logic). На этом сайте вы сможете быстро создать кратковременное приложение логики начального уровня в службе приложений. Никаких кредитных карт и обязательств.
> 
> 

Справку по интерфейсу REST API Swagger см. в статье [Справочные материалы по соединителям и приложениям API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0907_2016-->