---
title: Доступ и безопасность в шаблонах Azure Resource Manager | Microsoft Docs
description: Руководство по .NET Core для виртуальных машин Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/21/2016
ms.author: nepeters

---
# <a name="access-and-security-in-azure-resource-manager-templates"></a>Доступ и безопасность в шаблонах Azure Resource Manager
Обычно к приложениям, размещенным в Azure, нужен доступ через Интернет или VPN/ExpressRoute-подключение. В нашем примере с приложением музыкального магазина мы открыли доступ к веб-сайту через Интернет по общедоступному IP-адресу. Теперь нужно защитить подключения к приложению и доступ к ресурсам виртуальной машины. Защиту доступа обеспечивает группа безопасности сети. 

В этом документе объясняется, каким образом приложение музыкального магазина защищено на уровне шаблона Azure Resource Manager. Здесь будут описаны все зависимости и уникальные настройки. Чтобы оптимизировать процесс, заранее разверните экземпляр решения в подписке Azure, а затем установите шаблон Azure Resource Manager. Полный шаблон можно найти [здесь](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="public-ip-address"></a>Общедоступный IP-адрес
Чтобы предоставить общий доступ к ресурсу Azure, используйте ресурс общедоступного IP-адреса. Общедоступный IP-адрес может быть статическим или динамическим. Если используется динамический адрес, он удаляется при остановке или освобождении виртуальной машины. При повторном запуске виртуальной машины она может получить другой общедоступный IP-адрес. Чтобы IP-адрес не изменялся, используйте зарезервированный IP-адрес. 

Чтобы добавить в шаблон Azure Resource Manager общедоступный IP-адрес, используйте мастер добавления нового ресурса в Visual Studio или вставьте в шаблон допустимый объект JSON. 

Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager — [Общедоступный IP-адрес](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L121).

```none
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicipaddressName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "public-ip-front"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

Общедоступный IP-адрес может быть связан с виртуальным сетевым адаптером или с балансировщиком нагрузки. В нашем примере общедоступный IP-адрес связан с балансировщиком нагрузки, так как нагрузка веб-сайта музыкального магазина распределена среди нескольких виртуальных машин.

Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager — [Связь балансировщика нагрузки с общедоступным IP-адресом](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L208).

```none
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicipaddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Общедоступный IP-адрес на портале Azure. Обратите внимание, что общедоступный IP-адрес связан с балансировщиком нагрузки, а не с виртуальной машиной. Балансировщики нагрузки подробно описаны в следующем документе этой серии.

![Общедоступный IP-адрес](./media/virtual-machines-linux-dotnet-core/pubip.png)

Дополнительные сведения об общедоступных IP-адресах Azure см. в статье [IP-адреса в Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

## <a name="network-security-group"></a>Группа безопасности сети
Когда мы открываем доступ к ресурсам Azure, этот доступ желательно ограничить. Для виртуальных машин Azure безопасный доступ гарантирует группа безопасности сети. В примере приложения музыкального магазина закрыт любой доступ к виртуальной машине, кроме порта 80 для доступа по протоколу HTTP и порта 22 для SSH-доступа. Чтобы добавить в шаблон Azure Resource Manager группу безопасности сети, используйте мастер добавления нового ресурса в Visual Studio или вставьте в шаблон допустимый объект JSON.

Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager — [Группа безопасности сети](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L68).

```none
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('nsgfront')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "nsg-front"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
}
```

В нашем примере группа безопасности сети связана с объектом подсети, объявленным в ресурсе виртуальной сети. 

Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager — [Связь группы безопасности сети в виртуальной сетью](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L158).

```none
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
```

Так группа безопасности сети выглядит на портале Azure. Обратите внимание, что группу безопасности сети можно связать с подсетью и (или) с сетевым интерфейсом. В этом случае она связана с подсетью. В такой конфигурации правила для входящего трафика применяются для всех виртуальных машин, подключенных к подсети.

![Группа безопасности сети](./media/virtual-machines-linux-dotnet-core/nsg.png)

Дополнительные сведения о группах безопасности сети см. в статье [Группа безопасности сети](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/).

## <a name="next-step"></a>Дальнейшие действия
<hr>

[Шаг 3. Доступность и масштабирование в шаблонах Azure Resource Manager](virtual-machines-linux-dotnet-core-4-avalibility-scale.md)

<!--HONumber=Oct16_HO2-->


