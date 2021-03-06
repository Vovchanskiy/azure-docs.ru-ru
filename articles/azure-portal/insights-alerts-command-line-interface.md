---
title: Использование кроссплатформенного интерфейса командной строки (CLI) для создания оповещений для служб Azure | Microsoft Docs
description: Используйте интерфейс командной строки для создания оповещений Azure, которые могут активировать уведомления или автоматизированные операции при выполнении заданных условий.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb

---
# Использование кроссплатформенного интерфейса командной строки (CLI) для создания оповещений для служб Azure
> [!div class="op_single_selector"]
> * [Портал](../monitoring-and-diagnostics/insights-alerts-portal.md)
> * [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
> * [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](insights-alerts-command-line-interface.md)
> 
> 

## Обзор
В этой статье показано, как настроить оповещения Azure с помощью интерфейса командной строки (CLI).

Вы можете получать оповещения на основе отслеживания метрик или событий в службах Azure.

* **Значения метрик**. Оповещение активируется, когда значение указанной метрики выходит за рамки заданного порогового значения. То есть сначала оно активируется, когда условие выполняется, а затем — когда условие перестает выполняться.
* **События журнала действий**. Оповещение может активироваться при *каждом* событии или только тогда, когда выполняется определенное число событий.

Для оповещения можно настроить действие, выполняемое при активации оповещения:

* отправка уведомлений по электронной почте администратору службы и соадминистраторам;
* отправка уведомления на указанные дополнительные электронные адреса;
* вызов webhook;
* запуск выполнения Runbook Azure (в настоящее время только на портале Azure).

Для настройки правил генерации оповещений и получении сведений о них можно использовать:

* [Портал Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [интерфейс командной строки (CLI)](insights-alerts-command-line-interface.md);
* [REST API Azure Insights](https://msdn.microsoft.com/library/azure/dn931945.aspx).

Справку по командам всегда можно получить, введя команду с параметром -help. Например:

    azure insights alerts -help
    azure insights alerts actions email create -help


## Создание правил генерации оповещений с помощью интерфейса командной строки
1. Выполнение необходимые условия и войдите в Azure. Ознакомьтесь с [примерами использования интерфейса командной строки в Azure Insights](../monitoring-and-diagnostics/insights-cli-samples.md). Если коротко, установите интерфейс командной строки и выполните следующие команды. Вы выполните вход, увидите, какая подписка используется, и подготовитесь к выполнению команд Insights.

    ```console
    azure login
    azure account show
    azure config mode arm 

    ```

1. Чтобы получить список существующих правил для группы ресурсов, используйте следующую команду: **azure insights alerts rule list** *[параметры] &lt;группа\_ресурсов&gt;*.
   
   ```console
   azure insights alerts rule list myresourcegroupname
   
   ```
2. Чтобы создать правило, необходимо сначала получить определенные важные сведения.
   
   * **Идентификатор ресурса**, для которого необходимо задать оповещение.
   * Доступные **определения метрик** для этого ресурса.
     
     Получить идентификатор ресурса можно на портале Azure. Если ресурс уже создан, выберите его на портале. В следующей колонке в разделе *Параметры* выберите *Свойства*. В следующей колонке отображается поле *Идентификатор ресурса*. Кроме того, для получения идентификатора ресурса можно использовать [Azure Resource Explorer](https://resources.azure.com/).
     
     Ниже приведен пример идентификатора ресурса для веб-приложения.
     
     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```
     
     Чтобы получить список доступных метрик и их единиц измерения для предыдущего примера ресурсов, введите следующую команду в интерфейсе командной строки.
     
     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M 
     ```
     
     *PT1M* представляет собой детализацию доступного измерения (интервалы в 1 минуту). Выбор различных уровней детализации позволяет использовать разные параметры метрик.
3. Чтобы создать правило генерации оповещений на основе метрики, используйте команду следующего вида.
   
    **azure insights alerts rule metric set** *[параметры] &lt;имя\_правила&gt; &lt;расположение&gt; &lt;группа\_ресурсов&gt; &lt;размер\_окна&gt; &lt;оператор&gt; &lt;пороговое\_значение&gt; &lt;ИД\_целевого\_ресурса&gt; &lt;имя\_метрики&gt; &lt;оператор\_агрегата\_времени&gt;*
   
    Следующий пример настраивает оповещение о ресурсе веб-сайта. Оповещение активируется, когда любой трафик непрерывно поступает в течении 5 минут, и затем снова активируется, если в течение 5 минут трафик не поступает.
   
    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
   
    ```
4. Чтобы при срабатывании оповещения создавался webhook или отправлялось электронное сообщение, необходимо сначала создать электронный адрес и (или) веб-перехватчики webhook. Сразу после этого следует создать правило. С помощью интерфейса командной строки невозможно связать webhook или электронные адреса с уже созданными правилами.
   
    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com
   
    azure insights alerts actions webhook create https://www.contoso.com
   
    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```
5. Чтобы создать оповещение, которое срабатывает при возникновении определенных условий в журнале действий, используйте следующую команду.
   
    **insights alerts rule log set** *[gfhfvtnhs] &lt;имя\_правила&gt; &lt;расположение&gt; &lt;группа\_ресурсов&gt; &lt;имя\_операции&gt;*
   
    Например:
   
    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```
   
    Имя\_операции соответствует типу события в записи, сохраненной в журнале действий. Примеры: *Microsoft.Compute/virtualMachines/delete*, *microsoft.insights/diagnosticSettings/write*.
   
    Можно использовать команду PowerShell [Get AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) для получения списка возможных имен операций. Кроме того, можно использовать портал Azure, чтобы запросить журнал действий и найти определенные прошедшие операции, для которых требуется создать оповещение. Операции отображаются в графическом представлении журнала с понятными именами. Найдите в тексте JSON нужную запись и получите значение имя\_операции.
6. Можно проверить, правильно ли созданы оповещения, просмотрев отдельное правило.
   
    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Для удаления правил используйте следующую команду.
   
    **insights alerts rule delete** [параметры] &lt;группа\_ресурсов&gt; &lt;имя\_правила&gt;
   
    Приведенные ниже команды удаляют правила, созданные ранее в этой статье.
   
    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## Дальнейшие действия
* [Ознакомьтесь с общими сведениями о мониторинге Azure](../monitoring-and-diagnostics/monitoring-overview.md), включая типы информации, которую можно собирать и отслеживать.
* Узнайте больше о [настройке веб-перехватчиков webhook в оповещениях](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Узнайте больше о [модулях Runbook службы автоматизации Azure](../automation/automation-starting-a-runbook.md).
* Ознакомьтесь с [обзором сбора журналов диагностики](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), чтобы собирать подробные метрики о службе с высокой частотой.
* Прочитайте [обзор сбора метрики](insights-how-to-customize-monitoring.md) и узнайте, как можно обеспечить, чтобы служба была доступна и отвечала на запросы.

<!---HONumber=AcomDC_0928_2016-->