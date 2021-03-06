---
title: Развертывание ресурсов с помощью REST API и шаблона | Microsoft Docs
description: Узнайте, как использовать Azure Resource Manager и REST API Resource Manager для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2016
ms.author: tomfitz

---
# Развертывание ресурсов с использованием шаблонов и REST API Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Интерфейс командной строки Azure](resource-group-template-deploy-cli.md)
> * [Портал](resource-group-template-deploy-portal.md)
> * [ИНТЕРФЕЙС REST API](resource-group-template-deploy-rest.md)
> 
> 

В этом разделе объясняется, как использовать шаблоны и REST API Resource Manager для развертывания ресурсов в Azure.

> [!TIP]
> Справку по отладке ошибок во время развертывания можно получить в следующих статьях.
> 
> * Статья [Просмотр операций развертывания с помощью REST API Azure Resource Manager](resource-manager-troubleshoot-deployments-rest.md) содержит информацию о том, как получить сведения, которые помогут устранить ошибку.
> * [Устранение распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md) — руководство по устранению распространенных ошибок при развертывании.
> 
> 

Шаблон может быть локальным файлом или внешним файл, доступным по универсальному коду ресурса (URI). Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS).

[!INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## Развертывание с помощью REST API
1. Задайте [общие параметры и заголовки](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), включая маркеры аутентификации.
2. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Укажите идентификатор группы ресурсов, имя новой группы ресурсов и расположение, необходимое для решения. Дополнительную информацию см. в разделе [Создание группы ресурсов](https://msdn.microsoft.com/library/azure/dn790525.aspx).
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
3. Проверьте развернутую службу перед ее выполнением. Для этого выполните операцию [проверки развертывания шаблонов](https://msdn.microsoft.com/library/azure/dn790547.aspx). При тестировании развернутой службы укажите точно такие же параметры, как и при ее выполнении (как показано на следующем шаге).
4. Создайте развертывание. Укажите идентификатор подписки, имя группы ресурсов для развертывания, имя развертывания и ссылку на шаблон. Дополнительную информацию о файле шаблона см. в разделе [Файл параметров](..md#parameter-file). Дополнительную информацию о REST API для создания группы ресурсов см. в разделе [Создание шаблона-развертывания](https://msdn.microsoft.com/library/azure/dn790564.aspx). Обратите внимание, что для параметра **Режим** выбрано значение **Добавочный**. Чтобы выполнить полное развертывание, установите для параметра **Режим** значение **Полный**. Будьте внимательны при использовании полного режима, так как вы можете случайно удалить ресурсы, которые находятся не в шаблоне.
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",
              }
            }
          }
   
      Если вы хотите регистрировать в журнале содержимое запроса или содержимое ответа (или и то, и другое), добавьте в запрос параметр **debugSetting**.
   
        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }
   
      Можно настроить учетную запись хранения для использования маркера подписанного URL-адреса (SAS). Дополнительные сведения см. в статье [Delegating Access with a Shared Access Signature](https://msdn.microsoft.com/library/ee395415.aspx) (Делегирование доступа с помощью подписанного URL-адреса).
5. Получите состояние развертывания шаблона. Дополнительную информацию см. в разделе [Получение сведений о шаблоне-развертывания](https://msdn.microsoft.com/library/azure/dn790565.aspx).
   
          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[!INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## Дальнейшие действия
* Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](virtual-machines/virtual-machines-windows-csharp-template.md).
* Сведения об определении параметров в шаблоне см. в разделе [Параметры](resource-group-authoring-templates.md#parameters).
* Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](solution-dev-test-environments.md).
* Дополнительные сведения об использовании ссылки на KeyVault для передачи безопасных значений см. в статье [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0824_2016-->