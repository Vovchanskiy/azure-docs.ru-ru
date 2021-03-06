---
title: Создание шлюза приложений с помощью правил маршрутизации URL-адресов | Microsoft Docs
description: На этой странице приводятся инструкции по созданию и настройке шлюза приложений Azure с помощью правил маршрутизации URL-адресов.
documentationcenter: na
services: application-gateway
author: georgewallace
manager: jdial
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/18/2016
ms.author: gwallace

---
# Создание шлюза приложений с помощью маршрутизации на основе пути
> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-url-route-arm-ps.md)
> 
> 

Маршрутизация на основе URL-адресов позволяет связывать маршруты на основе URL-пути HTTP-запроса. Она проверяет, настроен ли для спиcка URL-адресов в шлюзе приложений маршрут к пулу тыловых серверов, и отправляет сетевой трафик в указанный пул. Как правило, маршрутизация на основе URL-адресов используется для распределения запросов содержимого разных типов между разными пулами тыловых серверов.

При маршрутизации на основе URL-адресов к шлюзу приложений добавляется новый тип правил. Шлюз приложений имеет два типа правил: базовые правила и правила на основе пути. Основной тип правил обеспечивает циклическое обслуживание пулов тыловых серверов, а PathBasedRouting наряду с циклическим распространением при выборе пула тыловых серверов учитывает также шаблон URL-адреса запроса.

> [!IMPORTANT]
> PathPattern: список шаблонов пути для сопоставления. Каждый шаблон должен начинаться с косой черты (/). Знак "\*" может располагаться только в конце. Примеры допустимых значений: /xyz, /xyz* или /xyz/*. Строка, передаваемая для сопоставления пути, не должна содержать никакого текста после первого символа "?" или "#", и сами эти символы не допускаются.
> 
> 

## Сценарий
В следующем примере шлюз приложений обслуживает трафик веб-сайта contoso.com с помощью двух пулов тыловых серверов: пула серверов для видео и пула серверов для изображений.

Запросы к http://contoso.com/image* направляются в пул серверов для изображений (pool1), а запросы http://contoso.com/video* — в пул серверов для видео (pool2). Если ни один из шаблонов пути не подходит, выбирается пул серверов по умолчанию (pool1).

![Маршрут URL-адреса](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## Перед началом работы
1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Скачать и установить последнюю версию вы можете в разделе **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Создайте виртуальную сеть и подсеть для шлюза приложений. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен находиться в подсети виртуальной сети.
3. Для использования шлюза приложений существующие серверы или серверы, для которых в виртуальной сети созданы конечные точки либо же назначен общедоступный или виртуальный IP-адрес, добавляются в пул тыловых серверов.

## Что необходимо для создания шлюза приложений?
* **Пул тыловых серверов**. Список IP-адресов тыловых серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса.
* **Back-end server pool settings** (Параметры внутреннего пула серверов). Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
* **Внешний порт**. Общедоступный порт, открытый в шлюзе приложений. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
* **Прослушиватель**. У прослушивателя есть интерфейсный порт, протокол (Http или Https — с учетом регистра) и имя SSL-сертификата (в случае настройке разгрузки SSL).
* **Правило**. Правило связывает прослушиватель и пул тыловых серверов, а также определяет, в какой пул тыловых серверов следует направлять трафик, поступающий на определенный прослушиватель.

## Создание шлюза приложений
Разница между использованием классической модели развертывания Azure и модели Azure Resource Manager заключается в порядке создания шлюза приложения и элементов, которые нужно настроить.

При использовании Resource Manager все элементы, которые будут включены в единый ресурс шлюза приложений, сначала настраиваются по отдельности.

Ниже приведены пошаговые инструкции по созданию шлюза приложений.

1. Создание группы ресурсов для диспетчера ресурсов.
2. Создание виртуальной сети, подсети и общедоступного IP-адреса для шлюза приложений.
3. Создание объекта конфигурации шлюза приложений.
4. Создание ресурса шлюза приложений.

## Создание группы ресурсов для диспетчера ресурсов
Убедитесь, что у вас установлена последняя версия Azure PowerShell. Дополнительные сведения см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).

### Шаг 1
Вход в Azure

    Login-AzureRmAccount

Вам будет предложено указать свои учетные данные для аутентификации.<BR>

### Шаг 2
Просмотрите подписки учетной записи.

    Get-AzureRmSubscription

### Шаг 3.
Выберите, какие подписки Azure будут использоваться. <BR>

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Шаг 4.
Создайте группу ресурсов. Если вы используете существующую группу, пропустите этот шаг.

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"

В качестве альтернативы можно также создать теги группы ресурсов для шлюза приложений:

    $resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Убедитесь, что во всех командах для создания шлюза приложений используется одна группа ресурсов.

В приведенном выше примере мы создали группу ресурсов с именем appgw-RG и расположением West US (западная часть США).

> [!NOTE]
> Если вам нужно настроить пользовательскую пробу для шлюза приложений, ознакомьтесь со статьей [Создание пользовательской проверки для шлюза приложений с помощью PowerShell для диспетчера ресурсов Azure](application-gateway-create-probe-ps.md). Дополнительные сведения см. в статье [Обзор мониторинга работоспособности шлюза приложений](application-gateway-probe-overview.md).
> 
> 

## Создание виртуальной сети и подсети для шлюза приложений
В следующем примере показано создание виртуальной сети с помощью диспетчера ресурсов.

### Шаг 1
Назначьте диапазон адресов 10.0.0.0/24 переменной подсети, которая будет использоваться для создания виртуальной сети.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Шаг 2
Создайте виртуальную сеть с именем "appgwvnet" в группе ресурсов "appgw-rg" для региона запада США при помощи префикса 10.0.0.0/16 с подсетью 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Шаг 3.
Назначьте переменную подсети для дальнейших этапов создания шлюза приложений.

    $subnet=$vnet.Subnets[0]

## Создание общедоступного IP-адреса для конфигурации интерфейсной части
Создайте ресурс общедоступного IP-адреса "publicIP01" в группе ресурсов "appgw-rg" для западного региона США.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

IP-адрес назначается шлюзу приложений при запуске службы.

## Создание конфигурации шлюза приложений
Перед созданием шлюза приложений необходимо настроить все элементы конфигурации. В ходе следующих шагов создаются необходимые элементы конфигурации для ресурса шлюза приложений.

### Шаг 1
Создайте конфигурацию IP шлюза приложений с именем "gatewayIP01". При запуске шлюз приложений получает IP-адрес из настроенной подсети. Затем шлюз маршрутизирует сетевой трафик на IP-адреса из внутреннего пула IP-адресов. Помните, что для каждого экземпляра требуется отдельный IP-адрес.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Шаг 2
Настройте серверную часть пула IP-адресов pool01 и pool2 с IP-адресами 134.170.185.46, 134.170.188.221,134.170.185.50 для pool1 и 134.170.186.46, 134.170.189.221,134.170.186.50 для pool2.

    $pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

    $pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

В этом примере используются два пула тыловых серверов для маршрутизации сетевого трафика на основе URL-пути. Один пул принимает трафик для URL-пути /video, а другой — для пути /image. Замените приведенные выше IP-адреса и добавьте IP-адреса конечных точек своего приложения.

### Шаг 3.
Настройте параметры шлюзов приложений с именами poolsetting01 и poolsetting02 для балансировки нагрузки сетевого трафика в пуле серверной части. В этом примере вы настроите различные параметры пулов тыловых серверов. Для каждого пула тыловых серверов параметры можно настроить отдельно.

    $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

    $poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### Шаг 4.
Настройте внешний IP-адрес, используя конечную точку с общедоступным IP-адресом.

    $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### Шаг 5
Настройте внешний порт для шлюза приложений.

    $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### Шаг 6
Создайте прослушиватель. Этот шаг позволяет настроить прослушиватель для общедоступного IP-адреса и порта, используемых для получения входящего сетевого трафика.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### Шаг 7
Настройте пути URL-правил для пулов тыловых серверов. На этом этапе настраивается относительный путь, который шлюз приложений использует для сопоставления URL-пути с пулом тыловых серверов, который назначается для обработки входящего трафика.

В приведенном ниже примере создаются два правила: одно для пути маршрутизации трафика /image в пул тыловых серверов pool1, а другое для пути маршрутизации трафика /video в пул тыловых серверов pool2.

    $imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

    $videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02

При настройке сопоставления для пути правил также настраивается пул тыловых серверов по умолчанию, который будет использоваться, если путь на соответствует ни одному из предустановленных правил.

    $urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### Шаг 8
Создайте параметр правила. Этот шаг позволяет настроить шлюз приложений для маршрутизации на основе URL-путей.

    $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap

### Шаг 9.
Настройте количество экземпляров и размер шлюза приложений.

    $sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## Создание шлюза приложений
Создайте шлюз приложений со всеми объектами конфигурации, описанными выше.

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

## Получение шлюза приложений
    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG

## Дальнейшие действия
Указания по настройке разгрузки SSL см. в статье [Настройка шлюза приложений для разгрузки SSL с помощью Azure Resource Manager](application-gateway-ssl-arm.md).

<!---HONumber=AcomDC_0824_2016-->