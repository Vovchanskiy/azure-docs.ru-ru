---
title: Политики диспетчера ресурсов Azure | Microsoft Docs
description: В этом разделе описывается, как предотвращать нарушения в различных областях, таких как подписки, ресурсы и группы ресурсов, с помощью политик диспетчера ресурсов Azure.
services: azure-resource-manager
documentationcenter: na
author: ravbhatnagar
manager: ryjones
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2016
ms.author: gauravbh;tomfitz

---
# Применение политик для управления ресурсами и контроля доступа
Теперь диспетчер ресурсов Azure позволяет управлять доступом с помощью пользовательских политик. С помощью политик можно запретить пользователям в организации нарушать соглашения, которые необходимы для управления ресурсами организации.

Можно создать определения политик, которые описывают действия или ресурсы, выполнение которых или доступ к которым запрещен. Эти определения политик назначаются для нужной области, такой как подписка, группа ресурсов или отдельный ресурс.

В этой статье мы продемонстрируем базовую структуру языка, который используется для создания политик. Затем мы рассмотрим применение этих политик в различных областях и, наконец, приведем несколько примеров решения этой задачи с помощью REST API.

## Чем это отличается от управления доступом на основе ролей?
Между политикой и контролем доступа на основе ролей существует ряд ключевых различий, но прежде всего необходимо понять, что политики и RBAC работают вместе. Чтобы использовать политику, пользователь должен пройти проверку подлинности с помощью RBAC. В отличие от RBAC, политика представляет собой систему разрешения по умолчанию и явного запрета.

Основное внимание RBAC уделяет действиям, которые может выполнять **пользователь** в различных областях. Например, определенный пользователь добавляется в роль участника для группы ресурсов в требуемой области, после чего он может вносить изменения в эту группу ресурсов.

Политика концентрируется на действиях **ресурсов** в различных областях. Например, с помощью политик можно управлять типами ресурсов, которые могут быть подготовлены, или ограничить расположения, в которых могут быть подготовлены ресурсы.

## Распространенные сценарии
Один из распространенных сценариев — запрос тегов подразделений для осуществления взимания средств за использование. Организация может разрешить выполнение операций только при указании соответствующего места возникновения затрат; в противном случае запрос будет отклонен. Это позволит организации взимать оплату за произведенные операции по месту возникновения соответствующих затрат.

Другой типичный сценарий — это контроль за местами создания ресурсов. Кроме того, организация может контролировать доступ к ресурсам, разрешая подготовку только определенного типа ресурсов.

Таким же образом организации могут управлять каталогом услуг или применять желаемые соглашения об именовании ресурсов.

Политики позволяют легко реализовать эти сценарии, как описано ниже.

## Структура определения политики
Определение политики создается с использованием JSON. Оно состоит из одного или нескольких условий или логических операторов, определяющих действия и результат, который будет достигнут при выполнении этих условий. Схема публикуется в [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json).

Как правило, политика содержит следующие элементы.

**Условие или логические операторы**: содержит набор условий, которые могут управляться с помощью набора логических операторов.

**Результат** — описание результата, который будет получен при удовлетворении заданного условия — отказ или проверка. В случае проверки откроется журнал службы событий предупреждений. Например, администратор может создать политику, которая включает аудит, если какой-то пользователь создал большую виртуальную машину. После этого администратор может просмотреть журналы.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }

## Вычисление политики
Политика вычисляется при создании ресурса или развертывании шаблона с помощью HTTP-запроса PUT. В случае развертывания шаблона политика вычисляется во время создания каждого ресурса в шаблоне.

> [!NOTE]
> Сейчас политика не вычисляет типы ресурсов, которые не поддерживают теги, вид и расположение, например тип ресурса Microsoft.Resources/deployments. Их поддержка будет добавлена в будущем. Чтобы избежать проблем с обратной совместимостью, необходимо явно указывать тип при создании политик. Например, политика тегов, которая не указывает типы, будет применяться для всех типов. В этом случае может произойти ошибка развертывания шаблона в будущем, если существует вложенный ресурс, который не поддерживает тег, и в вычисление политики был добавлен тип ресурса развертывания.
> 
> 

## Логические операторы
Поддерживаются следующие логические операторы и синтаксис.

| Имя оператора | Синтаксис |
|:--- |:--- |
| Not |"not" : {&lt;condition or operator &gt;} |
| И |"allOf" : [ {&lt;условие или оператор &gt;},{&lt;условие или оператор &gt;}] |
| Или |"anyOf" : [ {&lt;условие или оператор &gt;},{&lt;условие или оператор &gt;}] |

Диспетчер ресурсов позволяет задать сложную логическую схему в политике через вложенные операторы. Например, вы можете запретить создание ресурсов в определенном месте для указанного типа ресурса. Ниже показан пример вложенных операторов.

## Условия
Условие определяет, соответствует ли **поле** или **источник** определенным условиям. Ниже перечислены поддерживаемые условия и синтаксис.

| Имя условия | Синтаксис |
|:--- |:--- |
| Равно |"equals" : "&lt;value&gt;" |
| Like |"like" : "&lt;value&gt;" |
| Содержит |"contains" : "&lt;value&gt;" |
| В |"in" : [ "&lt;value1&gt;","&lt;value2&gt;" ] |
| ContainsKey |"containsKey" : "&lt;keyName&gt;" |
| Exists |"exists" : "&lt;bool&gt;" |

### Поля
Условия формируются с помощью полей и источников. Поле представляет свойства в полезных данных запроса ресурса, используемые для описания состояния ресурса. Источник представляет характеристики самого запроса.

Поддерживаются следующие поля и источники.

Поля: **name**, **kind**, **type**, **location**, **tags**, **tags.*** и **property alias**.

### Псевдонимы свойств
Псевдоним свойства — имя, которое можно использовать в определении политики для доступа к определенным свойствам типа ресурса, например параметрам и SKU. Он действует во всех версиях API, в которых существует это свойство. Псевдонимы можно получить с помощью приведенного ниже REST API (поддержка PowerShell будет добавлена в будущем).

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01

Ниже приведено определение псевдонима. Как видите, псевдоним определяет пути в различных версиях API, даже если имя свойства изменено.

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

Сейчас поддерживается следующие псевдонимы.

| Имя псевдонима | Описание |
| --- | --- |
| {resourceType}/sku.name |Поддерживаемые типы ресурса: Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts,<br />Microsoft.Web/serverFarms,<br /> Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft.CDN/profiles. |
| {resourceType}/sku.family |Поддерживаемый тип ресурса — Microsoft.Cache/Redis. |
| {resourceType}/sku.capacity |Поддерживаемый тип ресурса — Microsoft.Cache/Redis. |
| Microsoft.Compute/virtualMachines/imagePublisher | |
| Microsoft.Compute/virtualMachines/imageOffer | |
| Microsoft.Compute/virtualMachines/imageSku | |
| Microsoft.Compute/virtualMachines/imageVersion | |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |
| Microsoft.SQL/servers/version | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName | |
| Microsoft.SQL/servers/databases/edition | |
| Microsoft.SQL/servers/databases/elasticPoolName | |
| Microsoft.SQL/servers/elasticPools/dtu | |
| Microsoft.SQL/servers/elasticPools/edition | |

В настоящее время политика работает только для запросов PUT.

## Результат
Политика поддерживает три типа результата — **deny**, **audit** и **append**.

* "deny" — создание события в журнале аудита и сбой запроса.
* "audit" — создание события в журнале аудита и выполнение запроса.
* "append" — добавление определенного набора полей в запрос.

Для типа **append** необходимо предоставить сведения, как показано ниже.

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

Значением может быть строка или объект формата JSON.

## Примеры определения политик
Теперь посмотрим, как определить политику для реализации описанных выше сценариев.

### Взимание средств за использование: требовать теги отделов
Представленная ниже политика отклоняет все запросы без тега costCenter.

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

Приведенная ниже политика добавляет тег costCenter с предустановленным значением, если теги отсутствуют.

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }

Приведенная ниже политика добавляет тег costCenter с предустановленным значением, если имеются другие теги.

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]

      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### Соблюдение географических границ: контроль расположения ресурсов
Приведенный ниже пример показывает политику, которая отклоняет все запросы с размещением, отличным от Северной или Западной Европы.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Контроль служб: выбор каталога служб
В представленном ниже примере показано, как использовать источник. В нем указано, что допускаются только службы типа Microsoft.Resources/\*, Microsoft.Compute/\*, Microsoft.Storage/\*, Microsoft.Network/\*. Остальные службы не отклоняются.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Использование утвержденных SKU
Ниже примере показано использование псевдонима свойства для ограничения номеров SKU. В приведенном ниже примере для использования учетными записями хранения утверждены только Standard\_LRS и Standard\_GRS.

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }


### Соглашение об именовании
В приведенном ниже примере показано, как использовать знак подстановки, который поддерживается условием like. Это условие означает, что, в случае если имя не соответствует заданному шаблону (namePrefix*nameSuffix), запрос будет отклонен.

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Требование по пометке тегами только для ресурсов хранилища
В следующем примере показано, как вложить логические операторы, чтобы потребовать у приложения помечать тегами только ресурсы хранилища.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## Назначение политики
Политики могут применяться к различным областям, включая подписки, ресурсы и группы ресурсов. Политики наследуются всеми дочерними ресурсами. Это значит, что политики, применяемые к группе ресурсов, применяются также ко всем ресурсам в этой группе.

## Создание политики
В этом разделе подробно описано, как создавать политики с использованием API REST.

### Создание определения политики с использованием API REST
Для создания политики вы можете использовать [API REST для определений политик](https://msdn.microsoft.com/library/azure/mt588471.aspx). API REST позволяет создавать и удалять определения политик и получать сведения о существующих определениях.

Чтобы создать новую политику, выполните следующую команду:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Тело запроса будет похоже на следующее:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }


Определение политики может быть задано как в показанном выше примере. В качестве версии API (api-version) используйте значение *2016-04-01*. Примеры и дополнительные сведения см. в разделе [API REST для определения политик](https://msdn.microsoft.com/library/azure/mt588471.aspx).

### Создание определения политики с помощью PowerShell
Можно создать новое определение политики с помощью командлета New-AzureRmPolicyDefinition, как показано ниже. Приведенные ниже примеры создают политику, разрешающую использовать только ресурсы в Северной и Западной Европе.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'            

Результат выполнения сохраняется в объекте $policy и может использоваться позднее во время назначения политики. Вместо встроенного указания политики для параметра политики также можно указать путь к JSON-файлу, содержащему политику, как показано ниже.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain     regions" -Policy "path-to-policy-json-on-disk"

### Создание определения политики с помощью интерфейса командной строки Azure
Можно создать новое определение политики с помощью интерфейса командной строки Azure (Azure CLI) и команды определения политики, как показано ниже. Приведенные ниже примеры создают политику, разрешающую использовать только ресурсы в Северной и Западной Европе.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    


Вместо встроенного указания политики можно указать путь к файлу JSON, содержащему политику, как показано ниже.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"


## Применение политики
### Назначение политики с использованием API REST
Вы можете применить определение политики к требуемой области с помощью [API REST для назначений политик](https://msdn.microsoft.com/library/azure/mt588466.aspx). API REST позволяет создавать и удалять назначения политик и получать сведения о существующих назначениях.

Чтобы создать назначение политики, выполните следующую команду:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment} — это имя назначения политики. В качестве версии API (api-version) используйте значение *2016-04-01*.

Тело запроса будет похоже на следующее:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

Примеры и дополнительные сведения см. в разделе [API REST для назначений политик](https://msdn.microsoft.com/library/azure/mt588466.aspx).

### Назначение политики с помощью PowerShell
Политику, созданную выше, можно применить к требуемой области с помощью командлета PowerShell New-AzureRmPolicyAssignment, как показано ниже.

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Здесь $policy — объект политики, который был возвращен в результате выполнения командлета New-AzureRmPolicyDefinition, как показано выше. Область — это указанное имя группы ресурсов.

Если вы хотите удалить приведенное выше назначение политики, это можно сделать следующим образом.

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Можно получать, изменять или удалять определения политик с помощью командлетов Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition и Remove-AzureRmPolicyDefinition, соответственно.

Аналогично вы можете получать, изменять или удалять назначения политик с помощью командлетов Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment и Remove-AzureRmPolicyAssignment, соответственно.

### Назначение политики с помощью интерфейса командной строки Azure
Политику, созданную выше, можно применить к требуемой области с помощью интерфейса командной строки Azure и команды назначения политики, как показано ниже.

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Область — это указанное имя группы ресурсов. Если значение параметра policy-definition-id неизвестно, то его можно получить с помощью интерфейса командной строки Azure, как показано ниже.

    azure policy definition show <policy-name>

Если вы хотите удалить приведенное выше назначение политики, это можно сделать следующим образом.

    azure policy assignment remove --name regionPolicyAssignment --ccope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

С помощью команд policy definition show, policy definition set и policy definition delete можно соответственно получить, изменить или удалить определения политики.

Аналогичным образом с помощью команд policy assignment show, policy assignment set и policy assignment delete можно получить, изменить или удалить назначения политики.

## События аудита политики
После применения политики вы начнете видеть связанные с ней события. Для получения этих данных вы можете перейти на портал, воспользоваться PowerShell или интерфейсом командной строки Azure.

### Просмотр событий аудита политики с помощью PowerShell
Чтобы просмотреть все события, связанные с эффектом запрета, вы можете использовать следующую команду PowerShell.

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

Чтобы просмотреть все события, связанные с эффектом аудита, вы можете использовать следующую команду.

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### Просмотр событий аудита политики с помощью интерфейса командной строки Azure
Чтобы просмотреть все события в группе ресурсов, связанные с эффектом запрета, вы можете использовать следующую команду интерфейса командной строки.

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == "Microsoft.Authorization/policies/deny/action")"

Чтобы просмотреть все события, связанные с эффектом аудита, вы можете использовать следующую команду интерфейса командной строки.

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == "Microsoft.Authorization/policies/audit/action")"

<!---HONumber=AcomDC_0914_2016-->