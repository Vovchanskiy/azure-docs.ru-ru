---
title: Подписка Microsoft Azure, границы, квоты и ограничения служб
description: В этой статье приводится перечень наиболее распространенных ограничений, относящихся к подписке Azure и различным службам, квот и границ. Сюда входит информация о том, как увеличить лимиты и максимальные значения.
services: ''
documentationcenter: ''
author: rothja
manager: jeffreyg
editor: ''
tags: billing

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: btardif

---
# <a name="azure-subscription-and-service-limits,-quotas,-and-constraints"></a>Подписка Azure, границы, квоты и ограничения службы
## <a name="overview"></a>Обзор
Настоящий документ описывает некоторые из наиболее известных ограничений Microsoft Azure. На текущий момент он охватывает не все службы Azure. Со временам эти ограничения будут расширены.

Дополнительную информацию о ценах Azure см. на странице [Цены Azure](https://azure.microsoft.com/pricing/). Там вы сможете оценить затраты с помощью [калькулятора цен](https://azure.microsoft.com/pricing/calculator/) или просмотрев страницу сведений о ценах для службы (например, для [виртуальных машин Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)).

> [!NOTE]
> Если требуется сделать ограничение выше значения **ограничения по умолчанию**, то можно [бесплатно отправить запрос в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Ограничения не могут быть увеличены выше значения **Максимальное ограничение** в таблицах ниже. Если столбца **Максимальное ограничение** нет, то указанный ресурс не имеет настраиваемого ограничения.
> 
> 

## <a name="limits-and-the-azure-resource-manager"></a>Ограничения и диспетчер ресурсов Azure
Теперь несколько ресурсов можно объединить в одной группе ресурсов Azure. При использовании групп ресурсов ограничения, которые были глобальными, становятся управляемыми на региональном уровне благодаря диспетчеру ресурсов Azure. Дополнительные сведения о группах ресурсов Azure см. в статье [Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md).

В следующие ограничения добавлена новая таблица, в которой отражены все различия ограничений при использовании диспетчера ресурсов Azure. Например, имеется таблица **Ограничения подписки** и таблица **Ограничения подписки — Azure Resource Manager**. Если ограничение применяется в обоих случаях, оно показано только в первой таблице. Если не указано иное, ограничения глобальны во всех областях.

> [!NOTE]
> Важно подчеркнуть, что квоты для ресурсов в группах ресурсов Azure доступны для каждого региона по подписке, а не для каждой подписки, как квоты управления службами. В качестве примера используем квоты ядер ЦП. Если вам необходимо запросить увеличение квоты с поддержкой ядер, следует решить, сколько ядер будут использоваться и в каких регионах, а затем отправить запрос на квоты ядер группы ресурсов Azure core с нужными регионами и количеством. Поэтому, если необходимо использовать 30 ядер в Западной Европе для запуска приложения, следует запросить 30 ядер в Западной Европе. Но других регионах квота ядер не увеличится не будет — только в Западной Европе будет 30 ядер.
> <!-- -->
> В результате может быть полезно проанализировать, какие квоты группы ресурсов Azure потребуется для вашей рабочей нагрузки в каждом регионе, и запросить эти ресурсы в регионах, где выполняется развертывание. Подробные сведения об определении текущих квот для конкретных регионов см. в разделе [Устранение неполадок развертывания](resource-manager-common-deployment-errors.md).
> 
> 

## <a name="service-specific-limits"></a>Ограничения определенных служб
* [Active Directory](#active-directory-limits)
* [Управление API](#api-management-limits)
* [Служба приложений](#app-service-limits)
* [Application Insights](#application-insights-limits)
* [Автоматизация](#automation-limits)
* [кэш Azure Redis](#azure-redis-cache-limits)
* [Azure RemoteApp](#azure-remoteapp-limits)
* [Архивация](#backup-limits)
* [Пакетная служба](#batch-limits)
* [Службы BizTalk](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Облачные службы](#cloud-services-limits)
* [Фабрика данных](#data-factory-limits)
* [Аналитика озера данных](#data-lake-analytics-limits)
* [DNS](#dns-limits)
* [DocumentDB](#documentdb-limits)
* [Концентраторы событий](#event-hubs-limits)
* [Центр IoT](#iot-hub-limits)
* [хранилище ключей;](#key-vault-limits)
* [Службы мультимедиа](#media-services-limits)
* [Mobile Engagement;](#mobile-engagement-limits)
* [Мобильные службы](#mobile-services-limits)
* [Мониторинг](#monitoring-limits)
* [Multi-Factor Authentication](#multi-factor-authentication)
* [Сеть](#networking-limits)
* [Служба концентратора уведомлений](#notification-hub-service-limits)
* [Operational Insights;](#operational-insights-limits)
* [Группа ресурсов](#resource-group-limits)
* [Планировщик](#scheduler-limits)
* [Поиск](#search-limits)
* [Служебная шина](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [База данных SQL](#sql-database-limits)
* [Хранилище](#storage-limits)
* [StorSimple System](#storsimple-system-limits)
* [Анализ потока](#stream-analytics-limits)
* [Подписка](#subscription-limits)
* [Диспетчер трафика](#traffic-manager-limits)
* [Виртуальные машины](#virtual-machines-limits)
* [Наборы для масштабирования виртуальных машин](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Ограничения подписки
#### <a name="subscription-limits"></a>Ограничения подписки
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Ограничения подписки — Azure Resource Manager
При использовании диспетчера ресурсов Azure и групп ресурсов Azure применяются следующие ограничения. Ограничения, которые не были изменены после выпуска диспетчера ресурсов Azure, ниже не перечислены. Их можно просмотреть в предыдущей таблице.

Сведения о работе с ограничениями на запросы Resource Manager см. в разделе [Throttling Resource Manager requests](resource-manager-request-limits.md) (Регулирование запросов Resource Manager).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Ограничения группы ресурсов
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Ограничения виртуальных машин
#### <a name="virtual-machine-limits"></a>Ограничения виртуальной машины
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Ограничения виртуальных машин — диспетчер ресурсов Azure
При использовании диспетчера ресурсов Azure и групп ресурсов Azure применяются следующие ограничения. Ограничения, которые не были изменены после выпуска диспетчера ресурсов Azure, ниже не перечислены. Их можно просмотреть в предыдущей таблице.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Ограничения для масштабируемых наборов виртуальных машин
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>Ограничения сети
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Ограничения сети
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="traffic-manager-limits"></a>Ограничения диспетчера трафика
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Ограничения DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Ограничения хранилища
Дополнительные сведения об ограничениях учетных записей хранения см. в статье [Целевые показатели по производительности и масштабируемости для хранилища Azure](storage/storage-scalability-targets.md).

#### <a name="storage-service-limits"></a>Ограничения службы хранения
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### <a name="virtual-machine-disk-limits"></a>Ограничения для дисков виртуальной машины
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Дополнительные сведения см. в статье [Размеры виртуальных машин](virtual-machines/virtual-machines-linux-sizes.md).

**Учетные записи хранения Standard**

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Учетные записи хранения Premium**

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>Ограничения поставщика ресурсов хранилища
[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="cloud-services-limits"></a>Ограничения облачных служб
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Ограничения службы приложений
Следующие ограничения службы приложения включают ограничения для веб-приложений, мобильных приложений, приложений API и приложений логики.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Ограничения планировщика
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Ограничения пакета
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Ограничения служб BizTalk
В следующей таблице показаны ограничения для служб BizTalk Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="documentdb-limits"></a>Ограничения DocumentDB
[!INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

Квоты, перечисленные со звездочкой (*), [могут быть изменены при обращении в службу поддержки Azure](documentdb/documentdb-increase-limits.md).

### <a name="mobile-engagement-limits"></a>Ограничения привлечения пользователей мобильных устройств
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Ограничения поиска
Ценовые категории определяют емкость и ограничения службы поиска. Существуют следующие категории:

* *Бесплатный* : мультитенантная служба, используемая совместно с другими подписчиками Azure и предназначенная для оценки и разработки небольших проектов.
* *Базовый* предоставляет выделенные вычислительные ресурсы для небольших рабочих нагрузок в рабочей среде, поддерживая до трех реплик для обработки запросов с высоким уровнем доступности.
* *Стандартный (S1, S2, S3, S3 с высокой плотностью)* предназначена для больших рабочих нагрузок в рабочей среде. Существует несколько уровней в рамках уровня "Стандартный", поэтому для конкретных сценариев можно выбирать конфигурацию ресурсов.

**Ограничения на одну подписку**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Ограничения на одну службу поиска**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Подробные сведения о других ограничениях, включая размер документов, количество запросов в секунду, ключи, запросы и ответы, см. в статье [Ограничения поиска Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Ограничения служб мультимедиа
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Ограничения CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Ограничения мобильных служб
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>Мониторинг ограничений
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Ограничения служб концентратора уведомлений
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Ограничения концентраторов событий
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Ограничения служебной шины
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Ограничения центра IoT
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Ограничения фабрики данных
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Ограничения аналитики озера данных
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="stream-analytics-limits"></a>Ограничения Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Ограничения Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-remoteapp-limits"></a>Ограничения Azure RemoteApp
[!INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>Ограничения системы StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="operational-insights-limits"></a>Ограничения Operational Insights
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Ограничения резервного копирования
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Ограничения Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Ограничения Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Ограничения управления API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Ограничения кэша Redis для Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Ограничения хранилища ключей
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Ограничения автоматизации
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Ограничения базы данных SQL
Ограничения базы данных SQL описаны в разделе [Ограничения ресурсов базы данных SQL](sql-database/sql-database-resource-limits.md).

## <a name="see-also"></a>Дополнительные материалы
[Основные сведения о лимитах Azure и их увеличении](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Размеры виртуальных машин и облачных служб для Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Размеры для облачных служб](cloud-services/cloud-services-sizes-specs.md)

<!--HONumber=Oct16_HO2-->


