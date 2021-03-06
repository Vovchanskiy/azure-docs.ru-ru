
---
title: Выбор размера виртуальной сети при работе в среде Azure RemoteApp | Microsoft Docs
description: Узнайте о требованиях к IP-адресам для Azure RemoteApp в виртуальной сети
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Выбор размера виртуальной сети при работе в среде Azure RemoteApp
> [!IMPORTANT]
> Мы выводим удаленное приложение Azure RemoteApp из эксплуатации. Дополнительные сведения см. в [объявлении](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Когда вы работаете с платформой Azure RemoteApp в виртуальной сети (VNET-сети), RemoteApp использует IP-адреса в пределах некоторой подсети. В связи с этим необходимо обеспечить наличие в подсети достаточного количества IP-адресов для виртуальных машин RemoteApp с учетом масштаба развертывания службы RemoteApp. Это руководство по определению размеров сети не является идеальным с учетом того факта, что среда RemoteApp динамически подключает и отключает виртуальные машины в пределах коллекции, однако оно поможет вам подобрать оптимальный диапазон для своей подсети. Это особенно важно в связи с тем, что после развертывания службы RemoteApp в виртуальной сети увеличить размер подсети без удаления RemoteApp невозможно.

Для каждой коллекции RemoteApp, которая должна поддерживать максимальное число пользователей, необходимо выделить по 100 IP-адресов. Например, если у вас есть одна коллекция RemoteApp в рамках стандартного плана, а максимальное число пользователей составляет 500, для этой коллекции вам потребуется 100 IP-адресов. И аналогично, вам потребуется 100 IP-адресов для коллекции RemoteApp в рамках базового плана с 800 пользователями. Если количество ваших пользователей будет меньше максимального, число IP-адресов можно сократить. Минимальный размер подсети составляет 30 IP-адресов (/27).

Ознакомьтесь со следующими сведениями, чтобы убедиться, что виртуальная сеть настроена и работает должным образом:

* [Переход с личной виртуальной сети на виртуальную сеть Azure](remoteapp-migratevnet.md)
* [Проверка виртуальной сети Azure для использования с Azure RemoteApp](remoteapp-vnet.md)

<!---HONumber=AcomDC_0817_2016-->