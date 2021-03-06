
---
title: Обзор внутренней подсистемы балансировки нагрузки | Microsoft Docs
description: Обзор внутренней подсистемы балансировки нагрузки и ее функций, а также информация о том, как она работает в Azure и возможные сценарии настройки внутренних конечных точек.
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2016
ms.author: sewhee

---
# Обзор внутренней подсистемы балансировки нагрузки
В отличие от балансировщика нагрузки для Интернета, внутренний балансировщик нагрузки (ILB) имеет доступ только к ресурсам внутри облачной службы или использует VPN для доступа к инфраструктуре Azure. Эта инфраструктура ограничивает доступ к виртуальным IP-адресам с балансировкой нагрузки для облачной службы или виртуальной сети. Таким образом, они никогда не будут доступны напрямую конечной точке в Интернете. Это позволяет выполнять внутренние бизнес-приложения в Azure и получать к ним доступ в облаке или из локальных ресурсов.

## Сценарии для внутреннего балансировщика нагрузки
Внутренняя балансировка нагрузки Azure обеспечивает балансировку нагрузки между виртуальными машинами, которые размещены в облачной службе или региональной виртуальной сети. Информацию об использовании и настройке региональных виртуальных сетей см. в разделе [Региональные виртуальные сети](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) в блоге Azure. В имеющихся виртуальных сетях, настроенных для территориальной группы, невозможно использовать внутреннюю балансировку нагрузки.

Внутренний балансировщик нагрузки поддерживает следующие сценарии:

* в облачной службе от виртуальных машин к набору виртуальных машин, размещенных в той же облачной службе (см. рис. 1);
* в виртуальной сети от виртуальных машин в виртуальной сети к набору виртуальных машин, размещенных в той же облачной службе виртуальной сети (см. рис. 2);
* для распределенной виртуальной сети от локальных компьютеров к набору виртуальных машин, размещенных в той же облачной службе виртуальной сети (см. рис. 3).
* Доступные в Интернете многоуровневые приложения, серверные уровни в которых недоступны в Интернете, но требуют балансировки нагрузки для трафика на уровне, доступном в Интернете,
* Для балансировки нагрузки в бизнес-приложениях, размещенных в Azure. При этом не требуется дополнительное оборудование или программное обеспечение для распределения нагрузки. включая локальные серверы в наборе компьютеров, чей трафик балансируется.

## Многоуровневые приложения, доступные в Интернете
Веб-уровень содержит конечные точки, доступные в Интернете, для интернет-клиентов и входит в набор балансировки нагрузки. Подсистема балансировки нагрузки распределяет трафик от веб-клиентов на TCP-порт 443 (HTTPS) для веб-серверов.

Серверы баз данных находятся в пределах конечной точки внутренней балансировки нагрузки, которую веб-серверы используют для хранения данных. Это конечная точка с балансировкой нагрузки службы баз данных, трафик которой распределяется по серверам баз данных во внутреннем наборе балансировки нагрузки.

На следующем рисунке описано многоуровневое приложение, доступное в Интернете, в пределах одной и той же облачной службы.

На рисунке 1

![Внутренняя балансировка нагрузки для одной облачной службы](./media/load-balancer-internal-overview/IC736321.png)

Другой возможный сценарий для многоуровневого приложения: внутренняя подсистема балансировки нагрузки развертывается в облачной службе, отличной от той, которая использует службу для внутренней подсистемы балансировки нагрузки.

Облачные службы, использующие одну и ту же виртуальную сеть, будут иметь доступ к конечной точке внутренней подсистемы балансировки нагрузки.

Можно просмотреть рисунок ниже, на котором показано, что веб-серверы переднего плана размещены в облачной службе, отличной от той, в которой размещен сервер базы данных, и они используют конечную точку внутренней подсистемы балансировки нагрузки в пределах одной и той же виртуальной сети.

На рис. 2

![Внутренняя балансировка нагрузки между облачными службами](./media/load-balancer-internal-overview/IC744147.png)

## Бизнес-приложения в интрасети
Трафик от клиентов в локальной сети распределяется по набору серверов бизнес-приложений с помощью VPN-подключения к сети Azure.

Клиентский компьютер получит доступ к IP-адресу из службы VPN Azure, используя VPN типа «точка-сеть». Это позволит использовать бизнес-приложение, размещенное в пределах конечной точки внутренней подсистемы балансировки нагрузки.

Рис. 3

![Внутренняя балансировка нагрузки с использованием VPN типа «точка-сеть»](./media/load-balancer-internal-overview/IC744148.png)

Другой сценарий для бизнес-приложения: использование VPN типа «сеть-сеть» для виртуальной сети, в которой настроена конечная точка внутренней подсистемы балансировки нагрузки. Это позволит направлять локальный сетевой трафик к конечной точке внутренней подсистемы балансировки нагрузки.

Рис. 4

![Внутренняя балансировка нагрузки с помощью VPN типа «сеть-сеть»](./media/load-balancer-internal-overview/IC744150.png)

## Дальнейшие действия
[Приступая к настройке подсистемы балансировки нагрузки, доступной в Интернете](load-balancer-get-started-internet-arm-ps.md)

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-ps.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0831_2016-->