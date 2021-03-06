
---
title: Создание шлюза приложений с помощью шаблонов диспетчера ресурсов Azure | Microsoft Docs
description: На этой странице приводятся инструкции по созданию шлюза приложений Azure с помощью шаблона диспетчера ресурсов Azure.
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2016
ms.author: gwallace

---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Создание шлюза приложений с помощью шаблона диспетчера ресурсов Azure
Шлюз приложений — это балансировщик нагрузки уровня 7. Он отвечает за отработку отказов и эффективную маршрутизацию HTTP-запросов между разными серверами (облачными и локальными). Шлюз приложений выполняет такие функции доставки приложений: балансировка HTTP-нагрузки, определение сходства сеансов по файлам cookie, разгрузка SSL.

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-create-gateway-arm.md)
> * [Классическая модель — Azure PowerShell](application-gateway-create-gateway.md)
> * [Шаблон диспетчера ресурсов Azure](application-gateway-create-gateway-arm-template.md)
> * [Интерфейс командной строки Azure](application-gateway-create-gateway-cli.md)
> 
> 

Вы узнаете, как скачивать и изменять существующий шаблон Azure Resource Manager из GitHub, а также развертывать шаблон из GitHub, PowerShell и интерфейса командной строки Azure.

Если вы развертываете шаблон ARM непосредственно из GitHub без изменений, перейдите к соответствующему разделу.

## <a name="scenario"></a>Сценарий
Из этой статьи вы узнаете:

* как создать шлюз приложений с двумя экземплярами;
* как создать виртуальную сеть с именем VirtualNetwork1 и зарезервированным блоком CIDR (10.0.0.0/16);
* как создать подсеть с именем Appgatewaysubnet и блоком CIDR (10.0.0.0/28);
* как задать два ранее настроенных внутренних IP-адреса для веб-серверов, которые будут балансировать трафик. В этом примере в качестве внутренних IP-адресов используются адреса 10.0.1.10 и 10.0.1.11.

> [!NOTE]
> Приведенные значения представляют в этом шаблоне параметры. Чтобы настроить шаблон, можно обновить правила, прослушиватель и SSL, внеся необходимые изменения в файл azuredeploy.json.
> 
> 

![Сценарий](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Скачивание и использование шаблона диспетчера ресурсов Azure
Вы можете скачать из GitHub уже существующий шаблон диспетчера ресурсов Azure (ARM), чтобы создать виртуальную сеть и две подсети, а также внести в него нужные изменения и применить. Для этого выполните следующие действия.

1. Перейдите к этапу [Создание шлюза приложений](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Щелкните **azuredeploy.json** и нажмите кнопку **RAW**.
3. Сохраните файл в локальную папку на своем компьютере.
4. Если вы знакомы с шаблонами ARM, перейдите к шагу 7.
5. Откройте только что сохраненный файл и просмотрите содержимое раздела **parameters** в строке 5. В параметрах шаблона ARM есть заполнитель для значений, которые могут подставляться во время развертывания.
   
   | Параметр | Описание |
   | --- | --- |
   | **расположение** |Регион Azure, в котором создается шлюз приложений. |
   | **VirtualNetwork1** |Имя новой виртуальной сети. |
   | **addressPrefix** |Адресное пространство виртуальной сети в формате CIDR. |
   | **ApplicationGatewaysubnet** |Имя подсети шлюза приложений. |
   | **subnetPrefix** |Блок CIDR для подсети шлюза приложений. |
   | **skuname** |Размер экземпляра SKU. |
   | **емкость** |Количество экземпляров. |
   | **backendaddress1** |IP-адрес первого веб-сервера |
   | **backendaddress2** |IP-адрес второго веб-сервера |

    >[AZURE.IMPORTANT] Шаблоны ARM, хранящиеся в GitHub, со временем могут изменяться. Перед использованием шаблона обязательно его проверьте.

1. Проверьте содержимое раздела **resources** и обратите внимание на следующие параметры:
   
   * **type**. Тип ресурса, который создается шаблоном. В этом случае используется тип **Microsoft.Network/applicationGateways**, который представляет шлюз приложений.
   * **name**. Имя ресурса. Обратите внимание на фрагмент кода **[parameters('applicationGatewayName')]**. Он означает, что имя предоставляется пользователем или извлекается из файла параметров при развертывании.
   * **properties**. Список свойств для ресурса. Во время создания шлюза приложений этот шаблон использует виртуальную сеть и общедоступный IP-адрес.
2. Вернитесь на страницу [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create).
3. Щелкните **azuredeploy paremeters.json** и нажмите кнопку **RAW**.
4. Сохраните файл в локальную папку на своем компьютере.
5. Откройте сохраненный файл и измените значения параметров. Чтобы развернуть шлюз приложений в соответствии с задачами этого руководства, используйте следующие значения.
   
       {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       {
       "location" : {
       "value" : "West US"
       },
       "addressPrefix": {
       "value": "10.0.0.0/16"
       },
       "subnetPrefix": {
       "value": "10.0.0.0/24"
       },
       "skuName": {
       "value": "Standard_Small"
       },
       "capacity": {
       "value": 2
       },
       "backendIpAddress1": {
       "value": "10.0.1.10"
       },
       "backendIpAddress2": {
       "value": "10.0.1.11"
       }
       }
6. Сохраните файл. Вы можете проверить шаблон JSON и шаблон параметров с помощью таких веб-инструментов проверки JSON, как [JSlint.com](http://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Развертывание шаблона диспетчера ресурсов Azure с помощью PowerShell
Если вы ранее не использовали Azure PowerShell, то ознакомьтесь со статьей [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Следуйте инструкциям в статье, чтобы войти в Azure и выбрать подписку.

### <a name="step-1"></a>Шаг 1
    Login-AzureRmAccount

### <a name="step-2"></a>Шаг 2
Просмотрите подписки учетной записи.

    Get-AzureRmSubscription

Вам будет предложено указать свои учетные данные для аутентификации.<BR>

### <a name="step-3"></a>Шаг 3.
Выберите подписку Azure. <BR>

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Шаг 4.
При необходимости создайте новую группу ресурсов с помощью командлета **New-AzureResourceGroup** . В примере ниже создается группа ресурсов с именем AppgatewayRG, расположенная в восточной части США.

    New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"

Запустите командлет **New-AzureRmResourceGroupDeployment** , чтобы развернуть новую виртуальную сеть с помощью шаблона и файлов параметров, которые вы скачали и изменили ранее.

    New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
        -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Развертывание шаблона ARM с помощью интерфейса командной строки Azure
Развернуть шаблон ARM, скачанный с помощью Azure CLI, можно так.

### <a name="step-1"></a>Шаг 1
Если вы еще не использовали Azure CLI, ознакомьтесь со статьей [Установка и настройка CLI Azure](../xplat-cli-install.md) и следуйте инструкциям вплоть до выбора учетной записи Azure и подписки.

### <a name="step-2"></a>Шаг 2
Выполните команду **azure config mode** , чтобы переключиться в режим диспетчера ресурсов, как показано ниже.

    azure config mode arm

Вот результат, ожидаемый для указанной выше команды:

    info:   New mode is arm

### <a name="step-3"></a>Шаг 3.
При необходимости выполните команду **azure group create**, как показано ниже, чтобы создать группу ресурсов. Обратите внимание на результат выполнения команды. В списке, который откроется после выполнения команды, будут указаны используемые параметры. Дополнительные сведения о группах ресурсов см. в статье [Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md).

    azure group create -n appgatewayRG -l eastus

**-n (или --name)**. Имя для новой группы ресурсов. В нашем примере это *appgatewayRG*.

**-l (или --location)**. Регион Azure, в котором создается группа ресурсов. В нашем примере это *eastus*.

### <a name="step-4"></a>Шаг 4.
Запустите командлет **azure group deployment create** , чтобы развернуть новую виртуальную сеть с помощью шаблона и файлов параметров, которые вы скачали и изменили ранее. В списке, который откроется после выполнения команды, будут указаны используемые параметры.

    azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Развертывание шаблона ARM с помощью интерфейса кнопки развертывания
Развертывание с помощью кнопки развертывания — еще один способ использования шаблонов ARM. Он позволяет быстро и удобно работать с шаблонами на портале Azure.

### <a name="step-1"></a>Шаг 1.
Перейдите на страницу, посвященную [созданию шлюза приложений с использованием общедоступного IP-адреса](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/).

### <a name="step-2"></a>Шаг 2
Нажмите кнопку **Развернуть в Azure**.

![Развернуть в Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### <a name="step-3"></a>Шаг 3.
На портале укажите параметры шаблона развертывания и нажмите кнопку **OК**.

![parameters](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### <a name="step-4"></a>Шаг 4.
Выберите **Условия** и щелкните **Купить**.

### <a name="step-5"></a>Шаг 5
В колонке "Настраиваемое развертывание" щелкните **Создать**.

## <a name="next-steps"></a>Дальнейшие действия
Чтобы настроить разгрузку SSL, ознакомьтесь с [настройкой шлюза приложений для разгрузки SSL](application-gateway-ssl.md).

Указания по настройке шлюза приложений для использования с внутренним балансировщиком нагрузки см. в статье [Создание шлюза приложений с внутренней подсистемой балансировщика нагрузки (ILB)](application-gateway-ilb.md).

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

* [Подсистема балансировщика нагрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!--HONumber=Oct16_HO2-->


