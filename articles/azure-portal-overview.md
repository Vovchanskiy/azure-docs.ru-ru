---
title: "Обзор портала Microsoft Azure"
description: "Узнайте, как пользоваться порталом Microsoft Azure."
services: 
documentationcenter: 
author: davidwrede
manager: dwrede
editor: jimbe
ms.assetid: 53cb9df1-c96a-4f4e-b022-18336cd3d697
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/16/2015
ms.author: dwrede
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 31bea465db1afb5efc22b2280d83d3b50bced329


---
# <a name="microsoft-azure-portal-overview"></a>Обзор портала Microsoft Azure
Портал Microsoft Azure — это централизованное место, где можно подготавливать ресурсы Azure и управлять ими.  Это руководство поможет вам ознакомиться с порталом и узнать, как использовать некоторые его ключевые возможности.

* В **универсальном магазине Marketplace** представлены тысячи продуктов от корпорации Майкрософт и других поставщиков, и каждый из этих продуктов можно приобрести и подготовить.
* **Единая масштабируемая система** позволяет легко находить нужные ресурсы и выполнять различные операции по управлению.
* **Согласованные страницы управления** (так называемые колонки) дают возможность управлять самыми разнообразными службами Azure, используя унифицированную систему отображения параметров, действий, данных для выставления счетов, сведений о работоспособности и использовании и т. д.
* **Возможности персонализации** позволяют создать собственный начальный экран, на котором после входа в систему будет отображаться информация, которая нужна именно вам.  Кроме того, вы можете настроить под себя любую колонку управления с плитками.
  
  ![Знакомство с пользовательским интерфейсом портала Azure][UIOrientation]

## <a name="before-you-get-started"></a>Необходимые условия
Для прохождения этого учебника вам потребуется действительная подписка Azure.  Если у вас ее нет, [зарегистрируйтесь в бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/) прямо сейчас.  Оформив подписку, войдите на портал по адресу [https://portal.azure.com].

## <a name="how-to-create-a-resource"></a>Как создавать ресурсы
Azure Marketplace — это магазин, в котором на выбор предложены тысячи элементов, которые можно создавать, не выходя из него.  Предположим, вам нужно создать виртуальную машину Windows Server 2012.  Нажав кнопку «+СОЗДАТЬ», вы откроете набор рекомендованных категорий магазина.  В каждой категории есть некоторое количество готовых элементов и ссылка на полный каталог, в котором представлены все категории и окно поиска. Чтобы создать виртуальную машину Windows Server 2012, выполните следующие действия.  

1. Windows Server 2012 — рекомендуемый элемент, поэтому просто выберите его в категории «Среда выполнения приложений».  
2. Укажите в форме некоторые основные данные.
3. Нажмите кнопку «Создать», после чего сразу же начнется подготовка виртуальной машины.

Когда ресурс будет создан, в концентраторе уведомлений появится соответствующее сообщение и откроется колонка управления. (Ресурсы можно просмотреть в любой момент.)

![Категории портала][PortalCategories]

## <a name="how-to-find-your-resources"></a>Как найти свои ресурсы
Часто используемые ресурсы можно закрепить на начальной панели, но иногда возникает необходимость найти то, чем вы пользуетесь редко.  Показанная на рисунке ниже страница обзора позволяет просматривать все ваши ресурсы.  Вы можете отфильтровать их по подписке, выбрать столбцы и изменить их размер, а также перейти в колонку управления, щелкнув отдельные элементы.

![Обзор концентратора][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Как управлять ресурсами и предоставлять права доступа к ним
В этой колонке вы можете подключаться к виртуальной машине с помощью удаленного рабочего стола, отслеживать ключевые метрики производительности, управлять доступом к виртуальной машине, используя роли пользователей, изменять настройки виртуальной машины и выполнять другие важный задачи управления.  Предоставление доступа на основе роли — очень важный инструмент управления в средах с большим количеством пользователей.  Дополнительные сведения см. [здесь](active-directory/role-based-access-control-configure.md). Чтобы предоставить пользователю доступ к ресурсу, выполните следующие действия.

1. Перейдите к ресурсу.
2. В разделе «Основные сведения» нажмите кнопку «Все параметры».
3. В списке параметров щелкните «Пользователи».
4. На панели команд нажмите кнопку «Добавить».
5. Выберите пользователя и роль.

![Управление ресурсом][ManageResource]

## <a name="how-to-customize-a-resource-blade"></a>Как настраивать колонки ресурсов
Система Azure автоматически настраивает колонки ваших ресурсов, но управлять плитками в этих колонках вы можете сами.  Быстро активировав режим настройки, вы можете с легкостью добавить, удалить или переупорядочить плитки. В этом режиме также можно изменять их размер. Чтобы настроить колонку, выполните следующие действия.

1. Перейдите к ресурсу.
2. Нажмите кнопку "…" в верхней части колонки, которую нужно настроить.
3. Щелкните «Добавить части».
4. Перетащите нужные части.  

![Настройка колонок][CustomizeBlades]

## <a name="how-to-get-help"></a>Как получить справку
Если у вас возникнут какие-либо трудности, вы всегда можете обратиться к нам.  На портале есть страница справки и поддержки, которая поможет вам решить возникшую проблему.  Кроме того, прямо на портале можно создавать запросы в службу поддержки (зависит от вашего [плана поддержки](https://azure.microsoft.com/support/plans/)).  После создания запроса вы можете управлять его жизненным циклом, не покидая портал. Чтобы открыть страницу справки и поддержки, последовательно щелкните «Обзор» и «Справка + поддержка».  

![Справка и поддержка][HelpSupport]

## <a name="summary"></a>Сводка
Давайте подытожим, что вы узнали из этого руководства.

* Вы узнали, как зарегистрироваться в системе, оформить подписку и перейти на портал.
* Вы ознакомились с пользовательским интерфейсом портала, а также научились создавать и просматривать ресурсы.
* Вы узнали, как создавать и просматривать ресурсы.
* Вы изучили структуру колонок управления и научились согласованно управлять различными типами ресурсов.
* Мы рассказали вам, как настроить портал, чтобы важная для вас информация всегда была на виду.
* Мы также объяснили вам, как контролировать доступ к ресурсам с помощью управления доступом на основе ролей (RBAC).
* Вы узнали, как можно получить справку и поддержку.

Портал Microsoft Azure кардинально упрощает создание приложений в облаке и управление ими.  Чтобы всегда быть в курсе всех изменений и нововведений, подпишитесь на [блог по управлению](https://azure.microsoft.com/blog/topics/management/). Мы постоянно следим за [отзывами пользователей](https://feedback.azure.com/forums/223579-azure-preview-portal/) и реагируем на них.  Еще одним замечательным ресурсом, предоставляющим новости об Azure, является [блог ScottGu](http://weblogs.asp.net/scottgu).

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png



<!--HONumber=Nov16_HO2-->


