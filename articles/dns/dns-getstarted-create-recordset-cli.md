---
title: "Создание набора записей и записей зоны DNS с помощью интерфейса командной строки | Документация Майкрософт"
description: "Как создать записи узла для Azure DNS. Настройка наборов записей и записей с помощью интерфейса командной строки."
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 40accee35eca32eefd4afc2315c70d51e0edcdcd


---
# <a name="create-dns-record-sets-and-records-by-using-cli"></a>Создание записей и наборов записей DNS с помощью интерфейса командной строки
> [!div class="op_single_selector"]
> * [Портал Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Интерфейс командной строки Azure](dns-getstarted-create-recordset-cli.md)
> 
> 

В этой статье показано, как создавать записи и наборы записей с помощью интерфейса командной строки. После создания зоны DNS вам необходимо добавить DNS-записи для своего домена. Для этого сначала требуется изучить DNS-записи и наборы записей.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="create-a-record-set-and-record"></a>Создание набора записей и записи
В этом разделе показано, как создавать записи и набор записей. В этом примере создается набор записей с относительным именем www в зоне DNS contoso.com. Полное доменное имя записи — www.contoso.com. Тип записи — A, а срок жизни (TTL) составляет 60 секунд. По завершении этого шага будет создан пустой набор записей.

Чтобы создать набор записей на вершине зоны (в нашем примере — contoso.com), используйте имя записи "@",, включая кавычки. Это общее соглашение при работе с DNS.

### <a name="1-create-a-record-set"></a>1. Создание набора записей
Чтобы создать набор записей, используйте `azure network dns record-set create`. Укажите группу ресурсов, имя зоны, относительное имя набора записей, тип записей и TTL. Если параметр `--ttl` не определен, используется стандартное значение — 4 (в секундах). По завершении этого шага будет создан пустой набор записей www.

*Usage: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>*

    azure network dns record-set create myresourcegroup  contoso.com  www A  60

### <a name="2-add-records"></a>2) Добавление записей
Чтобы использовать созданный набор записей www, в него нужно добавить записи. Это можно сделать с помощью `azure network dns record-set add-record`.

Параметры для добавления записей в набор записей зависят от типа набора записей. Например, при использовании набора записей типа A вы можете указать записи только с параметром `-a <IPv4 address>`.

Записи типа *A* (IPv4) можно добавить в набор записей www с помощью следующей команды:

*Usage: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>*

    azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## <a name="additional-record-type-examples"></a>Дополнительные примеры типов записей
В следующих примерах показано, как создать набор записей каждого типа. Каждый такой набор содержит одну запись.

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
Сведения об управлении записями и набором записей см. в статье [Управление записями и наборами записей DNS с помощью CLI](dns-operations-recordsets-portal.md).

Дополнительные сведения об Azure DNS см. в статье [Обзор Azure DNS](dns-overview.md).




<!--HONumber=Nov16_HO2-->


