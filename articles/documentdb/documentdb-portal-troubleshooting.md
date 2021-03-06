---
title: Устранение неполадок DocumentDB на портале | Microsoft Docs
description: Узнайте, как устранять неполадки в работе DocumentDB на портале Azure.
services: documentdb
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: monicar

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: mimig

---
# Советы по устранению неполадок в работе DocumentDB на портале Azure
В этой статье описывается устранение неполадок в работе DocumentDB на портале Azure.

## Отсутствуют ресурсы
**Симптом**: в колонках портала нет баз данных или коллекций.

**Решение**: необходимо понизить уровень использования приложений, чтобы соблюдалась квота максимальной пропускной способности для коллекции.

**Объяснение**: портал — это такое же приложение, как и любое другое; он совершает вызовы к базе данных DocumentDB и коллекции. Если в данный момент ваши запросы регулируются по той причине, что вызовы совершаются из другого приложения, то портал также может регулироваться, из-за чего ресурсы не отображаются на портале. Чтобы устранить эту проблему, найдите причину повышенного использования пропускной способности, а затем обновите колонку портала. Сведения об измерении и понижении уровня использования пропускной способности см. в разделе [Пропускная способность](documentdb-performance-tips.md#throughput) статьи [Советы по улучшению производительности DocumentDB](documentdb-performance-tips.md).

## Не загружаются страницы или колонки
**Симптом**: на портале не отображаются страницы и колонки.

**Решение**: необходимо понизить уровень использования приложений, чтобы соблюдалась квота максимальной пропускной способности для коллекции.

**Объяснение**: портал — это такое же приложение, как и любое другое; он совершает вызовы к базе данных DocumentDB и коллекции. Если в данный момент ваши запросы регулируются по той причине, что вызовы совершаются из другого приложения, то портал также может регулироваться, из-за чего ресурсы не отображаются на портале. Чтобы устранить эту проблему, найдите причину повышенного использования пропускной способности, а затем обновите колонку портала. Сведения об измерении и понижении уровня использования пропускной способности см. в разделе [Пропускная способность](documentdb-performance-tips.md#throughput) статьи [Советы по улучшению производительности DocumentDB](documentdb-performance-tips.md).

## Кнопка "Добавить коллекцию" неактивна
**Симптом**: в колонке базы данных неактивна кнопка **Добавить коллекцию**.

**Объяснение**: если ваша подписка Azure связана с преимуществами или кредитами, такими как бесплатные кредиты, предлагаемые в подписке MSDN, и вы использовали все свои кредиты за текущий месяц, то создание новых коллекций в DocumentDB будет недоступно.

**Решение**: удалите из учетной записи предельную сумму расходов.

1. На навигационной панели портала Azure щелкните **Подписки**, выберите подписку, связанную с базой данных DocumentDB, а затем в колонке **Подписка** щелкните **Управление**. ![DocumentDB предлагает несколько четко определенных моделей согласованности (нестрогой) на ваш выбор.](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)
2. В новом окне браузера вы увидите, что у вас больше не осталось кредитов. Нажмите кнопку **Снять предельную сумму расходов**, чтобы удалить ограничение расходов только для текущий расчетного периода или на неопределенный срок. Затем выполните шаги мастера, чтобы добавить данные своей кредитной карты или подтвердить их. ![DocumentDB предлагает несколько четко определенных моделей согласованности (нестрогой) на ваш выбор.](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

## Обозреватель запросов завершается с ошибками
См. раздел "Устранение неполадок" в [статье об обозревателе запросов](documentdb-query-collections-query-explorer.md#troubleshoot).

## В элементах мониторинга нет доступных данных
См. раздел "Устранение неполадок" в [статье о мониторинге](documentdb-monitor-accounts.md#troubleshooting).

## В проводнике документов не отображаются документы
См. статью [Просмотр, изменение, создание и отправка документов JSON с помощью проводника документов DocumentDB](documentdb-view-json-document-explorer.md#troubleshoot).

## Дальнейшие действия
Если у вас по-прежнему возникают проблемы при использовании портала, отправьте сообщение электронной почты по адресу [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) для получения помощи или зарегистрируйте запрос в службу поддержки. Для этого на портале щелкните **Обзор** > **Справка и поддержка** и выберите **Создать запрос в службу поддержки**.

<!---HONumber=AcomDC_0831_2016-->