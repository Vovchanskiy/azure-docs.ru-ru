---
title: Словарь терминов Azure | Microsoft Docs
description: Словарь терминов, связанных с облаком на платформе Azure. Краткий словарь Azure, содержащий определения основных терминов по облакам в Azure.
keywords: словарь Azure, облачная терминология, словарь терминов Azure, определения терминов, облачные термины
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''

ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: monicar

---
# Словарь терминов Microsoft Azure: словарь терминов, связанных с облаком на платформе Azure
Словарь терминов Microsoft Azure — это краткий словарь терминов, связанных с облаком на платформе Azure.

## Поиск определения служб и других терминов, связанных с облаком
* Определения служб Azure и их аналогов в AWS см. в разделе [Microsoft Azure и веб-службы Amazon](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).
* Общие термины облачной индустрии см. в статье [Терминология облачных вычислений](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

Вместе с двумя указанными выше ссылками словарь терминов Azure дает полную классификацию терминов, применяемых в Azure и облачной индустрии.

## Список терминов Azure
### <a name="account"></a>учетная запись
Рабочая, учебная или личная учетная запись Майкрософт, которая используется для доступа к подписке Azure и управления этой подпиской. См. также статью [Связь между подписками Azure и Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).

### <a name="availability-set"></a>группа доступности
Коллекция виртуальных машин, которые находятся под общим управлением для обеспечения избыточности и надежности приложений. Во время планового или внепланового обслуживания группы доступности обеспечивают доступность как минимум одной виртуальной машины. См. также статьи, посвященные [управлению доступностью виртуальных машин Windows](virtual-machines/virtual-machines-windows-manage-availability.md) и [управлению доступностью виртуальных машин Linux](virtual-machines/virtual-machines-linux-manage-availability.md).

### <a name="classic-model"></a>классическая модель развертывания Azure
Одна из двух [моделей развертывания](resource-manager-deployment-model.md), которые используются для развертывания ресурсов в Azure (новой моделью является модель Azure Resource Manager). Одни ресурсы Azure можно развертывать только по одной из этих моделей, другие — по любой из них. Модели, которые можно использовать для развертывания определенных ресурсов Azure, указаны в соответствующих инструкциях.

### <a name="cli"></a>интерфейс командной строки Azure (CLI)
[Интерфейс командной строки](xplat-cli-install.md), который можно использовать для управления службами Azure на компьютерах Windows, OSX и Linux.

### <a name="powershell"></a>Azure PowerShell
[Интерфейс командной строки](powershell-install-configure.md) для управления службами Azure через командную строку на компьютерах под управлением Windows. Некоторыми службами или компонентами служб можно управлять только с помощью PowerShell или интерфейса командной строки. Модели, которые можно использовать для развертывания определенных ресурсов Azure, указаны в соответствующих инструкциях. См. также статью [Как установить и настроить Azure PowerShell](powershell-install-configure.md).

### <a name="arm-model"></a>модель развертывания с помощью Azure Resource Manager
Одна из двух [моделей развертывания](resource-manager-deployment-model.md), которые используются для развертывания ресурсов в Microsoft Azure (вторая — классическая модель развертывания). Одни ресурсы Azure можно развертывать только по одной из этих моделей, другие — по любой из них. Модели, которые можно использовать для развертывания определенных ресурсов Azure, указаны в соответствующих инструкциях.

### <a name="fault-domain"></a>Домен сбоя
Коллекция виртуальных машин в группе доступности, которые могут отказать в одно и то же время. Примером может послужить группа установленных в стойку машин с общим источником питания и сетевым коммутатором. В Azure виртуальные машины в группе доступности автоматически распределяются между несколькими доменами сбоя. См. также статьи, посвященные [управлению доступностью виртуальных машин Windows](virtual-machines/virtual-machines-windows-manage-availability.md) и [управлению доступностью виртуальных машин Linux](virtual-machines/virtual-machines-linux-manage-availability.md).

### <a name="geo"></a>геообъект
Определенная граница для резидентства данных, которое обычно включает два или несколько регионов. Границы могут проходить внутри государств или выходить за их пределы и подчиняются налоговому законодательству. Каждый геообъект включает как минимум один регион. Примеры геообъектов: Азиатско-Тихоокеанский регион и Япония. Также называется *географией*. См. также статью, посвященную [регионам Azure](best-practices-availability-paired-regions.md).

### <a name="geo-replication"></a>георепликация
Процесс автоматической репликации такого содержимого, как BLOB-объекты, таблицы и очереди, в паре регионов. См. также статью [Активная георепликация для базы данных SQL Azure](sql-database/sql-database-geo-replication-overview.md).

### <a name="image"></a>образ
Файл с конфигурацией операционной системы и приложения, который можно использовать для создания любого количества виртуальных машин. В Azure можно использовать два типа образов — образ виртуальной машины и образ ОС. Образ виртуальной машины включает операционную систему и все диски, присоединенные к виртуальной машине на момент создания образа. Образ ОС содержит только общие сведения об операционной системе и не включает конфигурации дисков данных. См. также статью, посвященную [просмотру и выбору образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](virtual-machines/virtual-machines-windows-cli-ps-findimage.md).

### <a name="limits"></a>ограничения
Количество доступных для создания ресурсов или максимально возможный уровень работоспособности. Обычно ограничения связаны с подписками, службами и предложениями. См. также статью, посвященную [ограничениям и квотам подписок и служб Azure](azure-subscription-service-limits.md).

### <a name="load-balancer"></a>подсистема балансировки нагрузки
Ресурс, который распределяет входящий трафик между компьютерами в сети. В Azure подсистема балансировки нагрузки направляет трафик на виртуальные машины, определенные в наборе подсистемы балансировки нагрузки. [Балансировщик нагрузки](load-balancer/load-balancer-overview.md) может быть внутренним или с выходом в Интернет.

### <a name="offer"></a>предложение
Цены, кредиты и связанные условия, применяемые к подписке Azure. См. также статью [Сведения о предложении Azure](https://azure.microsoft.com/support/legal/offer-details/).

### <a name="portal"></a>портал
Безопасный веб-портал, используемый для развертывания служб Azure и управления этими службами. Существует два портала: [портал Azure](http://portal.azure.com/) и [классический портал](http://manage.windowsazure.com/). Некоторые службы доступны в обоих порталах, в то время как другие доступны только в одном из них. В [таблице доступности портала Azure](https://azure.microsoft.com/features/azure-portal/availability/) указано, какие службы доступны на каждом портале.

### <a name="region"></a>регион
Область геообъекта, не пересекающая национальные границы и содержащая один или несколько центров обработки данных. Цены, региональные службы и типы предложений применяются на уровне регионов. Обычно регионы объединяются в пары, даже если находятся в нескольких сотнях километров друг от друга. Пары регионов можно использовать как механизм для аварийного восстановления и сценариев высокой доступности. Обычно также называется *расположением*. См. также статью, посвященную [регионам Azure](best-practices-availability-paired-regions.md).

### <a name="resource"></a>ресурс
Элемент, который является частью решения Azure. Каждая служба Azure позволяет развертывать различные типы ресурсов, такие как базы данных или виртуальные машины. См. также обзорную статью, посвященную [Azure Resource Manager](resource-group-overview.md).

### <a name="resource-group"></a>группа ресурсов
Контейнер в диспетчере ресурсов, содержащий связанные ресурсы для приложения. Группа ресурсов может содержать все ресурсы для приложения или только те ресурсы, которые логически сгруппированы вместе. Можно выбрать способ распределения ресурсов для групп ресурсов, который наиболее оптимален для вашей организации. См. также обзорную статью, посвященную [Azure Resource Manager](resource-group-overview.md).

### <a name="arm-template"></a>шаблон Resource Manager
JSON-файл, который декларативно определяет один или несколько ресурсов Azure и устанавливает зависимости между развернутыми ресурсами. Шаблон можно использовать для согласованного и многократного развертывания ресурсов. См. также [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).

### <a name="resource-provider"></a>поставщик ресурсов
Служба, которая предоставляет ресурсы для развертывания и управления посредством диспетчера ресурсов. Каждый поставщик ресурсов предоставляет операции REST API для работы с развернутыми ресурсами. Доступ к поставщикам ресурсов можно получить через портал Azure, Azure PowerShell и несколько пакетов SDK для программирования. См. также обзорную статью, посвященную [Azure Resource Manager](resource-group-overview.md).

### <a name="role"></a>роль
Средства для управления доступом, которые можно назначать пользователям, группам и службам. Роли могут выполнять такие действия, как создание, управление и чтение ресурсов Azure. См. также статью [RBAC: встроенные роли](active-directory/role-based-access-built-in-roles.md).

### <a name="sla"></a>соглашение об уровне обслуживания (SLA)
Соглашение, в котором описываются обязательства корпорации Майкрософт в отношении доступности и подключения. В каждой службе Azure есть отдельный SLA. См. также статью [Соглашения об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/).

### <a name="storage-account"></a>учетная запись хранения
Учетная запись хранения предоставляет доступ к службам больших двоичных объектов, очередей, таблиц и файлов в службе хранилища Azure. Учетная запись хранения предоставляет уникальное пространство имен для ваших объектов данных в службе хранилища Azure. См. также статью [Об учетных записях хранения Azure](storage/storage-create-storage-account.md).

### <a name="subscription"></a>подписка
Соглашение клиента с корпорацией Майкрософт, которое позволяет им получать доступ к службам Azure. Цены на подписки и связанные условия управляются предложением, выбранным для подписки. См. статью [Соглашение Microsoft Online Subscription](https://azure.microsoft.com/support/legal/subscription-agreement/). См. также статью [Связь между подписками Azure и Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).

### <a name="tag"></a>тег
Условие индексирования, которое позволяет классифицировать ресурсы в соответствии с вашими требованиями к управлению или выставлению счетов. Использование тегов может потребоваться, когда имеется сложная коллекция ресурсов и групп ресурсов и требуется визуализировать эти активы наиболее оптимальным для вас образом. Например, можно снабдить тегами ресурсы, которые выполняют в организации одну и ту же роль или относятся к одному отделу. См. также статью [Использование тегов для упорядочивания ресурсов Azure](resource-group-using-tags.md).

### <a name="update-domain"></a>домен обновления
Коллекция виртуальных машин в группе доступности, которые обновляются в одно и то же время. Во время планового обслуживания виртуальные машины в одном домене обновления перезапускаются вместе. Azure не перезапускает больше одного домена обновления за один раз. Он также называется доменом обновления. См. также статьи, посвященные [управлению доступностью виртуальных машин Windows](virtual-machines/virtual-machines-windows-manage-availability.md) и [управлению доступностью виртуальных машин Linux](virtual-machines/virtual-machines-linux-manage-availability.md).

### <a name="vm"></a>виртуальная машина
Программная реализация физического компьютера, на котором работает операционная система. Несколько виртуальных машин могут одновременно работать на одном физическом компьютере. В Azure виртуальные машины могут быть разных размеров. См. также статью [Документация по виртуальным машинам](https://azure.microsoft.com/documentation/services/virtual-machines/).

### <a name="vm-extension"></a>расширение виртуальной машины
Ресурс, реализующий поведение или функции, которые либо помогают другим программам работать, либо предоставляют возможность для взаимодействия с работающим компьютером. Например, расширение доступа к виртуальным машинам можно использовать для сброса или изменения значений удаленного доступа на виртуальной машине Azure. См. также статьи [Обзор расширений и компонентов виртуальной машины (Windows)](virtual-machines/virtual-machines-windows-extensions-features.md) и [Обзор расширений и компонентов виртуальной машины (Linux)](virtual-machines/virtual-machines-linux-extensions-features.md).

### <a name="vnet"></a>виртуальная сеть
Сеть, обеспечивающая соединение между ресурсами Azure и изолированная от всех клиентов Azure. Ее можно подключить к другой виртуальной сети Azure с помощью [VPN-шлюза Azure](vpn-gateway/vpn-gateway-about-vpngateways.md), а также к локальной сети с помощью [разных способов](./vpn-gateway/vpn-gateway-cross-premises-options.md). Вы можете полностью контролировать блоки IP-адресов, параметры DNS, политики безопасности и таблицы маршрутизации в этой сети. См. также статью [Общие сведения о виртуальных сетях](virtual-network/virtual-networks-overview.md).

### **См. также**
* [Приступая к работе с Azure](https://azure.microsoft.com/get-started/)
* [Центр облачных ресурсов](https://azure.microsoft.com/resources/)
* [Azure для бизнес-приложений](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure в вашем центре обработки данных](https://azure.microsoft.com/overview/business-apps-on-azure/)

<!---HONumber=AcomDC_0803_2016-->