---
title: "Служба приложений Azure для приложений API, мобильных приложений и веб-приложений | Документация Майкрософт"
description: "Узнайте, как служба приложений Azure позволяет разрабатывать и развертывать веб-приложения и мобильные приложения, а также управлять ими."
keywords: "служба приложений, служба приложений Azure, стоимость службы приложений, масштабировать, масштабируемый, развертывание приложений, развертывание приложений Azure, PaaS, платформа как услуга, веб-сайт, веб, мобильные службы Azure"
services: app-service
documentationcenter: 
author: omarkmsft
manager: erikre
editor: cephalin
ms.assetid: 979cafa8-eeb6-4d3b-87cf-764a821c3e4f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/26/2016
ms.author: omark
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 042da7ac46fd20bd0e9af33f449f8ea4a6a42089


---
# <a name="what-is-azure-app-service"></a>Что такое служба приложений Azure?
*Служба приложений* — это решение на основе модели [платформа как услуга](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) от Microsoft Azure, которое позволяет создавать веб- и мобильные приложения для любой платформы или устройства. Кроме того, с его помощью можно интегрировать приложения с решениями SaaS, подключаться к локальным приложениям и автоматизировать бизнес-процессы. Azure запускает приложения на полностью управляемых виртуальных машинах. При этом вы можете выбрать тип виртуальных машин, которые необходимо использовать для вашей нагрузки (общие или выделенные).

Служба приложений обладает возможностями для работы в сетевой и мобильной среде, которые ранее предлагались в составе отдельных решений Веб-сайты Azure и мобильные службы Azure. Она также предусматривает новые возможности для автоматизации бизнес-процессов и размещения облачных API. Интегрированная служба приложений позволяет легко создать единое решение, объединяющее несколько компонентов (например, веб-сайты, серверные части мобильных приложений, интерфейсы API RESTful и бизнес-процессы).

Следующее 4-минутное видео знакомит вас с новыми возможностями службы приложений и объясняет ее связь с предыдущими предложениями Azure.

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/app-service-history-lesson/player]
> 
> 

## <a name="why-use-app-service"></a>Преимущества службы приложений
Ниже приведены некоторые основные функции и возможности службы приложений.

* **Поддержка нескольких языков и платформ.** Служба приложений превосходно поддерживает ASP.NET, Node.js, Java, PHP и Python. Кроме того, на виртуальных машинах службы приложений можно запустить [Windows PowerShell и другие сценарии или исполняемые файлы](../app-service-web/web-sites-create-web-jobs.md).
* **Оптимизация DevOps.** Настраивайте [непрерывную интеграцию и развертывание](../app-service-web/app-service-continuous-deployment.md) в Visual Studio Team Services, GitHub или BitBucket. Повышайте уровень обновлений с помощью [тестовых и промежуточных сред](../app-service-web/web-sites-staged-publishing.md). Выполняйте [тестирование A/B](../app-service-web/app-service-web-test-in-production-get-start.md). Управляйте приложениями в службе приложений с помощью оболочки [Azure PowerShell](../powershell-install-configure.md) или [кроссплатформенного интерфейса командной строки (CLI)](../xplat-cli-install.md).
* **Высокодоступное глобальное масштабирование.** [Увеличивайте](../app-service-web/web-sites-scale.md) либо [уменьшайте](../monitoring-and-diagnostics/insights-how-to-scale.md) размер вручную или автоматически. Храните приложения в любом месте глобальной инфраструктуры центра обработки данных. При этом [соглашение об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/app-service/) гарантирует высокую доступность.
* **Подключение к платформам SaaS и локальным данным.** На выбор доступно более 50 [соединителей](../connectors/apis-list.md) для корпоративных систем (например, SAP, Siebel и Oracle), служб SaaS (например, Salesforce и Office 365) и популярных интернет-служб (например, Facebook и Twitter). Получайте доступ к локальным данным с помощью [гибридных подключений](../biztalk-services/integration-hybrid-connection-overview.md) и [виртуальных сетей Azure](../app-service-web/web-sites-integrate-with-vnet.md).
* **Безопасность и соответствие требованиям.** Служба приложений совместима со стандартами [ISO, SOC и PCI](https://www.microsoft.com/TrustCenter/).
* **Шаблоны приложений.** Вы можете выбрать любой шаблон из обширного списка шаблонов приложений в [Azure Marketplace](https://azure.microsoft.com/marketplace/). Эти шаблоны позволяют устанавливать популярное программное обеспечение с открытым исходным кодом, например WordPress, Joomla и Drupal, с помощью мастера.
* **Интеграция с Visual Studio.** Выделенные инструменты в Visual Studio упрощают создание, развертывание и отладку приложений.

## <a name="app-types-in-app-service"></a>Типы приложений в службе приложений
Служба приложений предоставляет несколько *типов приложений*, которые предназначены для размещения разных рабочих нагрузок.

* [**Веб-приложения**](../app-service-web/app-service-web-overview.md) — для размещения веб-сайтов и веб-приложений.
* [**Мобильные приложения**](../app-service-mobile/app-service-mobile-value-prop.md) — для размещения серверной части мобильных приложений.
* [**Приложения API**](../app-service-api/app-service-api-apps-why-best-platform.md) — для размещения API RESTful.
* [**Приложения Logic Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md) — для автоматизации бизнес-процессов и интеграции систем и данных в облаках без необходимости создавать код.

Здесь под *приложением* подразумеваются ресурсы размещения, выделенные для запуска рабочей нагрузки. Большинство пользователей думают, что для функционирования веб-приложения в браузере требуются вычислительные ресурсы и код приложения. В службе приложений *веб-приложение* — это вычислительные ресурсы, предоставляемые средой Azure для размещения кода приложения. Если приложение состоит из веб-интерфейса и серверной части API RESTful, можно развернуть оба этих компонента или только код внешнего интерфейса в веб-приложении, а код серверной части — в приложении API. Ваше приложение может состоять из нескольких разных приложений службы приложений.

## <a name="app-service-plans"></a>Планы службы приложений
[Планы службы приложений](azure-web-sites-web-hosting-plans-in-depth-overview.md) определяют тип вычислительных ресурсов, которые используются приложениями. Если ожидаются незначительные объемы трафика, можно использовать общие виртуальные машины (ценовая категория **Бесплатный** и **Общий**). Для более высоких нагрузок следует использовать выделенные виртуальные машины (ценовая категория **Базовый**, **Стандартный** и **Премиум**). Для нескольких приложений службы приложений можно использовать один и тот же план. Изменение ценовых категорий зависит от используемого плана.

Если вам требуется больше возможностей в отношении масштабируемости и сетевой изоляции, для работы с приложениями можно использовать [среду службы приложений](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Цены
См. дополнительные сведения о [расценках на службу приложений](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="testdrive-app-service"></a>Оцените возможности службы приложений
[Создайте пример приложения логики, мобильного или веб-приложения](http://go.microsoft.com/fwlink/?LinkId=523751) и поэкспериментируйте с ним в течение часа бесплатно: без кредитных карт, обязательств и проблем.

Или создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/pricing/free-trial/)и ознакомьтесь с одним из руководств по началу работы:

* [Создание веб-приложения](../app-service-web/app-service-web-get-started.md)
* [Создание мобильного приложения](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Создание приложения API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Создание приложения логики](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO2-->


