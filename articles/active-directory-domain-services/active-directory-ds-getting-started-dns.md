---
title: "Доменные службы Azure AD: обновление настроек DNS для виртуальной сети Azure | Документация Майкрософт"
description: "Приступая к работе с доменными службами Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/21/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 81c0564390c57c7ee001ce5a574ab670faf898b4


---
# <a name="azure-ad-domain-services-update-dns-settings-for-the-azure-virtual-network"></a>Доменные службы Azure AD — обновление настроек DNS для виртуальной сети Azure
## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Задача 4. Обновление настроек DNS для виртуальной сети Azure
В предыдущих задачах по настройке вы успешно включили доменные службы Azure AD для своего каталога. Следующая задача — сделать так, чтобы компьютеры в виртуальной сети могли подключаться к этим службам и использовать их. Обновите параметры DNS-сервера для виртуальной сети, указав IP-адреса, по которым доменные службы AD Azure доступны в виртуальной сети.

> [!NOTE]
> После включения доменных служб Azure AD для каталога запишите IP-адреса доменных служб Azure AD, которые отображаются на вкладке **Настройка** .
> 
> 

Выполните следующие этапы настройки, чтобы обновить параметр DNS-сервера для виртуальной сети, в которой были включены доменные службы Azure AD.

1. Перейдите на **классический портал Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Выберите узел **Сети** на панели слева.
   
    ![Узел "Виртуальные сети"](./media/active-directory-domain-services-getting-started/virtual-network-select.png)
3. На вкладке **Виртуальные сети** выберите виртуальную сеть, в которой были включены доменные службы Azure AD, чтобы просмотреть ее свойства.
4. Выберите вкладку **Настройка** .
   
    ![Узел "Виртуальные сети"](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)
5. В разделе **DNS-серверы** введите IP-адреса доменных служб Azure AD.
6. Убедитесь, что вы ввели оба IP-адреса, которые отображались в разделе **Доменные службы** на вкладке **Настройка** для вашего каталога.
7. Чтобы сохранить параметры DNS-сервера для этой виртуальной сети, щелкните **Сохранить** на панели задач в нижней части страницы.
   
   ![Обновление параметров DNS-сервера для виртуальной сети.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [!NOTE]
> После обновления параметров DNS-сервера для виртуальной сети может потребоваться некоторое время на обновление конфигурации DNS виртуальных машин в сети. Если виртуальная машина не может подключиться к домену, можно освободить кэш DNS на виртуальной машине (например, с помощью команды ipconfig /flushdns). Эта команда принудительно обновит параметры DNS на виртуальной машине.
> 
> 

## <a name="task-5-enable-password-synchronization-to-azure-ad-domain-services"></a>Задача 5. Включение синхронизации паролей в доменные службы Azure AD
Следующая задача конфигурации — [включить синхронизацию паролей в доменные службы Azure AD](active-directory-ds-getting-started-password-sync.md).




<!--HONumber=Nov16_HO2-->


