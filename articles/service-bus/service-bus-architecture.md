---
title: Архитектура служебной шины | Microsoft Docs
description: Здесь описывается архитектура обработки сообщений и запросов на ретрансляцию, которая используется служебной шиной Azure.
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2016
ms.author: sethm

---
# Архитектура служебной шины
В этой статье описывается архитектура обработки сообщений и запросов на ретрансляцию, которая используется служебной шиной Azure.

## Единицы масштабирования служебной шины
Содержимое служебной шины упорядочено по *единицам масштабирования*. Единица масштабирования — это единица развертывания, которая содержит все компоненты, необходимые для запуска службы. Для каждой области развертывается одна или несколько единиц масштабирования служебной шины.

С единицей масштабирования сопоставляется пространство имен служебной шины. Единица масштабирования обрабатывает все типы сущностей служебной шины: ретрансляторы и элементы обмена сообщениями через брокер (очереди, разделы, подписки). Единица масштабирования служебной шины состоит из следующих компонентов.

* **Набор узлов шлюза.** Узлы шлюза проверяют подлинность входящих запросов и обрабатывают запросы на ретрансляцию. У каждого узла шлюза есть общедоступный IP-адрес.
* **Набор узлов брокера сообщений.** Узлы брокера сообщений обрабатывают запросы к сущностям обмена сообщениями.
* **Единое хранилище шлюза.** Хранилище шлюза содержит данные по каждой сущности, определенной в соответствующей единице масштабирования. Хранилище шлюза реализуется на основе базы данных SQL Azure.
* **Несколько хранилищ сообщений.** Хранилища сообщений содержат сообщения из всех очередей, разделов и подписок, определенных в соответствующей единице масштабирования. Здесь же хранятся все данные подписки. Если не включены [секционированные сущности обмена сообщениями](../service-bus-messaging/service-bus-partitioning.md), очередь или раздел сопоставляется с одним хранилищем сообщений. Подписки хранятся в том же хранилище сообщений, что и соответствующий родительский раздел. Хранилища сообщений реализуются поверх баз данных SQL Azure (за исключением [обмена сообщениями через служебную шину категории "Премиум"](../service-bus-messaging/service-bus-premium-messaging.md)).

## Контейнеры
Каждой сущности обмена сообщениями назначается определенный контейнер. Контейнер — это логическая конструкция, которая использует одно единственное хранилище сообщений для хранения всех соответствующих данных в этом контейнере. Каждый контейнер назначается узлу брокера сообщений. Обычно контейнеров больше, чем узлов брокера сообщений. Таким образом, каждый узел брокера сообщений загружает несколько контейнеров. Контейнеры назначаются узлу брокера сообщений таким образом, чтобы все узлы брокера сообщений были загружены в равной мере. Если шаблон нагрузки меняется (например, один из контейнеров загружается слишком сильно) или узел брокера сообщений становится временно недоступным, контейнеры перераспределяются между узлами брокера сообщений.

## Обработка входящих запросов обмена сообщениями
Когда клиент отправляет запрос на служебную шину, подсистема балансировки нагрузки Azure передает его в один из узлов шлюза. Узел шлюза авторизует запрос. Если запрос относится к сущности обмена сообщениями (очереди, разделу или подписке), узел шлюза выполняет поиск этой сущности в хранилище шлюза и определяет, в каком хранилище сообщений она находится. Затем он определяет, какой узел брокера сообщений обслуживает данный контейнер в настоящий момент, и отправляет запрос на этот узел брокера сообщений. Узел брокера сообщений обрабатывает запрос и обновляет состояние сущности в хранилище контейнера. Узел брокера сообщений отправляет ответ обратно на узел шлюза, который пересылает соответствующий ответ клиенту, отправившему исходный запрос.

![Обработка входящих запросов обмена сообщениями](./media/service-bus-architecture/IC690644.png)

## Обработка входящих запросов на ретрансляцию
Когда клиент отправляет запрос на служебную шину, подсистема балансировки нагрузки Azure передает его в один из узлов шлюза. Если запрос связан с прослушиванием, узел шлюза создает новую ретрансляцию. Если запрос связан с подключением к определенной ретрансляции, узел шлюза переадресовывает его на узел шлюза, которому принадлежит ретрансляция. Узел шлюза, которому принадлежит ретрансляция, отправляет запрос о подключении клиенту прослушивания, чтобы прослушиватель создал временный канал к узлу шлюза, получившему запрос на подключение.

Как только подключение ретрансляции будет установлено, клиенты смогут обмениваться сообщениями через узел шлюза, используемый для связи.

![Обработка входящих запросов на ретрансляцию](./media/service-bus-architecture/IC690645.png)

## Дальнейшие действия
Теперь, когда вы ознакомились с архитектурой служебной шины, изучите следующие статьи.

* [Основные сведения об обмене сообщениями через служебную шину](../service-bus-messaging/service-bus-messaging-overview.md)
* [Базовая информация о Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Решение для обмена сообщениями в очереди при помощи очередей служебной шины](../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)

<!---HONumber=AcomDC_0928_2016-->