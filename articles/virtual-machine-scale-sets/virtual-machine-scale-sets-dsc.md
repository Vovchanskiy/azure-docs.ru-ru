---
title: 'Использование расширения '
настройка: ''
требуемого: ''
состояния": ''
(dsc): ''
с: ''
наборами: ''
масштабирования: ''
виртуальных: ''
машин: ''
'|': ''
microsoft: ''
azure": ''
description: Использование наборов масштабирования виртуальных машин с помощью расширения Azure DSC
services: virtual-machine-scale-sets
documentationcenter: ''
author: zjalexander
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager
keywords: ''

ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal

---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Использование наборов масштабирования виртуальных машин с помощью расширения Azure DSC
[Наборы масштабирования виртуальных машин (VMSS)](virtual-machine-scale-sets-overview.md) могут использоваться с обработчиком расширения [Настройка требуемого состояния (DSC) Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). Наборы масштабирования виртуальных машин позволяют развертывать большое количество виртуальных машин и управлять ими, а также гибко масштабировать ресурсы согласно нагрузке. Расширение DSC используется для настройки виртуальных машин при их подключении для работы с программным обеспечением в рабочей среде.

## <a name="differences-between-deploying-to-vm-and-vmss"></a>Различия между развертываниями на виртуальной машине и в наборе масштабирования виртуальных машин
Базовая структура шаблона для VMSS немного отличается от одиночной виртуальной машины. В частности, на одиночной виртуальной машине расширения развертываются в узле virtualMachines. Существует запись с типом extensions, где DSC добавляется в шаблон:

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Узел VMSS содержит раздел properties с атрибутами VirtualMachineProfile и extensionProfile. DSC добавляется в раздел extensions:

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-vmss"></a>Поведение для наборов VMSS
Поведение при использовании наборов VMSS идентично поведению для одиночной виртуальной машины. При создании виртуальной машины она автоматически подготавливается с помощью расширения DSC. Если для расширения требуется новая версия WMF, то перед подключением виртуальная машина перезагружается. Когда виртуальная машина подключена, она загружает файл конфигурации DSC в формате ZIP и подготавливает его на виртуальной машине. Дополнительные сведения см. в статье [Общие сведения об обработчике расширения DSC в Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

## <a name="next-steps"></a>Дальнейшие действия
Изучите [шаблон Azure Resource Manager для расширения DSC](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md).

Узнайте, как [расширение DSC безопасно обрабатывает учетные данные](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md). 

Дополнительные сведения об обработчике расширений DSC см. в статье [Общие сведения об обработчике расширения для настройки требуемого состояния в Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). 

Для получения дополнительных сведений о DSC PowerShell [посетите центр документации PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

<!--HONumber=Oct16_HO2-->


