---
title: "Расположения ExpressRoute | Документация Майкрософт"
description: "В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure."
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4aa1e62b4758481b40c4b1c8a632c8bb72ab4ce6


---
# <a name="expressroute-partners-and-peering-locations"></a>Партнеры и одноранговые расположения ExpressRoute
В данной статье приведены таблицы со сведениями о поставщиках услуг подключения ExpressRoute, географическом покрытии ExpressRoute, облачных службах Майкрософт, поддерживаемых через ExpressRoute, и системных интеграторах ExpressRoute.

## <a name="a-namepartnersaexpressroute-connectivity-providers"></a><a name="partners"></a>Поставщики услуг подключения ExpressRoute
ExpressRoute поддерживается во всех регионах и расположениях Azure. На следующей карте обозначены регионы Azure и расположения ExpressRoute. Расположения ExpressRoute соответствуют тем территориям, где Майкрософт взаимодействует с несколькими одноранговыми поставщиками услуг.

![Карта расположения][0]

Вы сможете получить доступ ко всем службам Azure во всех регионах соответствующего геополитического региона, если вы подключены по меньшей мере к одному расположению ExpressRoute в этом регионе. В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Северная Америка** |Восточная часть США, западная часть США, восточная часть США 2, центральная часть США, юго-центральная часть США, северо-центральная часть США, центральная часть Канады, восточная часть Канады |Атланта, Чикаго, Даллас, Лас-Вегас, Лос-Анджелес, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон, округ Колумбия, Монреаль+, Квебек+, Торонто |
| **Северная Америка** |Южная часть Бразилии |Сан-Паулу |
| **Европа** |Северная Европа, Западная Европа, запад Соединенного Королевства, юг Соединенного Королевства |Амстердам, Дублин, Лондон, Ньюпорт (Уэльс)+, Париж |
| **Азия** |Восточная Азия, Юго-Восточная Азия |Гонконг, Сингапур |
| **Япония** |Западная Япония, Восточная Япония |Осака, Токио |
| **Австралия** |Восточная Австралия, Юго-Восточная Австралия |Мельбурн, Сидней |
| **Индия** |Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Мумбаи |

В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- | --- |
| **Облако правительства США** |Правительство штата Айова, США, Правительство штата Вирджиния, США |Чикаго, Даллас, Нью-Йорк, Вашингтон (округ Колумбия) |
| **Китай** |Северный Китай, Восточный Китай |Пекин, Шанхай |
| **Германия** |Центральная Германия, восточная Германия |Берлин, Франкфурт |

В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.

## <a name="connectivity-provider-locations"></a>Расположения поставщиков услуг подключения
> [!div class="op_single_selector"]
> [Расположения по поставщику](expressroute-locations.md#connectivity-provider-locations)
> [Поставщики по расположению](expressroute-locations-providers.md#connectivity-provider-locations)
> 
> 

### <a name="production-azure"></a>Рабочая среда Azure
| **Поставщик услуг** | **Microsoft Azure** | **Office 365 и CRM Online** | **Расположения** |
| --- | --- | --- | --- |
| **AARNet** |Поддерживаются |Поддерживаются |Мельбурн, Сидней |
| **[Aryaka Networks](http://www.aryaka.com/)** |Поддерживаются |Поддерживаются |Амстердам, Даллас, Кремниевая долина, Сингапур, Токио, Вашингтон (округ Колумбия) |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживаются |Поддерживаются |Амстердам, Чикаго, Даллас, Лондон, Кремниевая долина, Сингапур, Сидней, Вашингтон (округ Колумбия) |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Поддерживаются |Поддерживаются |Амстердам, Гонконг, Лондон, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **CenturyLink** |Скоро |Скоро |Кремниевая долина |
| **China Telecom Global** |Поддерживаются |Не поддерживается |Гонконг |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Поддерживаются |Скоро |Даллас, Монреаль+, Торонто |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживаются |Поддерживаются |Амстердам, Дублин, Лондон, Токио |
| **Comcast** |Поддерживаются |Поддерживаются |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Поддерживаются |Поддерживаются |Лос-Анджелес |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Поддерживаются |Амстердам, Атланта, Чикаго, Даллас, Гонконг, Лондон, Лос-Анджелес, Мельбурн, Нью-Йорк, Осака, Париж+, Сан-Пауло, Сиэтл, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия), Торонто |
| **euNetworks** |Поддерживаются |Поддерживаются |Амстердам |
| **GÉANT** |Поддерживаются |Поддерживаются |Амстердам |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Поддерживаются |Поддерживаются |Осака, Токио |
| **[InterCloud](https://www.intercloud.com/)** |Поддерживаются |Поддерживаются |Амстердам, Лондон, Сингапур, Вашингтон (округ Колумбия) |
| **Internet Solutions - Cloud Connect** |Поддерживаются |Поддерживаются |Амстердам, Лондон |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)** |Поддерживаются |Поддерживаются |Амстердам, Лондон, Париж |
| **Jisc** |Поддерживаются |Поддерживаются |Лондон |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживаются |Поддерживаются |Амстердам, Чикаго, Даллас, Лас-Вегас+, Лондон, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **Megaport** |Поддерживаются |Поддерживаются |Даллас, Гонконг, Лас-Вегас, Лос-Анджелес, Мельбурн, Нью-Йорк, Сиэтл, Сингапур, Сидней, Вашингтон (округ Колумбия) |
| **MTN** |Поддерживаются |Поддерживаются |Лондон |
| **Next Generation Data** |Скоро |Скоро |Newport(Wales)+ |
| **NEXTDC** |Поддерживаются |Поддерживаются |Мельбурн, Сидней |
| **NTT Communications** |Поддерживаются |Поддерживаются |Лондон, Лос-Анджелес, Осака, Сингапур, Токио, Вашингтон (округ Колумбия) |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Поддерживаются |Поддерживаются |Амстердам, Гонконг, Лондон, Кремниевая долина, Сингапур, Сидней, Вашингтон (округ Колумбия) |
| **PCCW Global Limited** |Поддерживаются |Поддерживаются |Гонконг |
| **SIFY** |Поддерживаются |Поддерживаются |Ченнай |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Поддерживаются |Поддерживаются |Сингапур |
| **Softbank** |Поддерживаются |Поддерживаются |Осака, Токио |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Поддерживаются |Поддерживаются |Амстердам, Ченнаи, Гонконг, Лондон, Мумбаи, Кремниевая долина, Сингапур, Вашингтон (округ Колумбия) |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Поддерживаются |Поддерживаются |Амстердам, Дублин, Лондон |
| **Telefonica** |Поддерживаются |Поддерживаются |Сан-Паулу |
| **Telenor** |Поддерживаются |Поддерживаются |Амстердам, Лондон |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Поддерживаются |Поддерживаются |Мельбурн, Сидней |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Поддерживаются |Поддерживаются |Амстердам, Гонконг, Лондон, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **Vodafone** |Поддерживаются |Не поддерживается |Лондон |
| **[Zayo Group](http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** |Поддерживаются |Поддерживаются |Чикаго, Лос-Анджелес, Нью-Йорк, Кремниевая долина, Торонто, Вашингтон (округ Колумбия) |

 **+** означает "скоро"

### <a name="national-cloud-environments"></a>Национальные облачные среды
#### <a name="us-government-cloud"></a>Облако правительства США
| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживаются |Поддерживаются |Чикаго, Вашингтон (округ Колумбия) |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Поддерживаются |Чикаго, Даллас, Нью-Йорк, Вашингтон (округ Колумбия) |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживаются |Поддерживаются |Чикаго, Нью-Йорк+, Вашингтон (округ Колумбия) |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Поддерживаются |Поддерживаются |Чикаго, Даллас, Нью-Йорк, Вашингтон (округ Колумбия) |

#### <a name="china"></a>Китай
| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **China Telecom** |Поддерживаются |Не поддерживается |Пекин, Шанхай |

Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

#### <a name="germany"></a>Германия
| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживаются |Не поддерживается |Берлин+, Франкфурт |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Не поддерживается |Франкфурт |
| **e-shelter** |Поддерживаются |Не поддерживается |Берлин |
| **Interxion** |Поддерживаются |Не поддерживается |Франкфурт |

## <a name="a-namenonpartnersaconnectivity-through-service-providers-not-listed"></a><a name="nonpartners"></a>Подключение через других поставщиков услуг
Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

* Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.
  
  * [Equnix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
  * [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
  * [NextDC](http://www.nextdc.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Cologix](http://www.cologix.com/)
* Обратитесь к своему поставщику услуг подключения, чтобы он расширил вашу сеть, добавив необходимое пиринговое расположение.
  * Убедитесь, что поставщик услуг подключения расширяет границы вашего подключения, сохраняя высокую доступность во избежание влияния единых точек отказа.
* Закажите канал ExpressRoute с Exchange у вашего поставщика услуг подключения для связи с Майкрософт.
  * Для настройки подключения выполните действия, описанные в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-classic.md) .

| **Поставщик услуг подключения** | **Exchange** | **Расположения** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Сингапур |
| **Alaska Communications** |Equinix |Сиэтл; |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |Нью-Йорк, Вашингтон (округ Колумбия) |
| **[XO Communications](http://www.xo.com/)** |Equinix |Кремниевая долина |

## <a name="expressroute-system-integrators"></a>Системные интеграторы ExpressRoute
Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

| **Системный интегратор** | **Континент** |
| --- | --- |
| **[Avanade Inc.](http://www.avanade.com/)** |Азия, Европа, США |
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** |Европа |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** |США |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** |Азия |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** |США |
| **[Project Leadership](http://www.projectleadership.net/azure)** |США |

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Убедитесь, что выполнены все необходимые условия. См. статью [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположений"



<!--HONumber=Nov16_HO2-->


