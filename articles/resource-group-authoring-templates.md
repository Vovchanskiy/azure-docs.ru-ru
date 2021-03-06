---
title: Создание шаблонов Azure Resource Manager | Microsoft Docs
description: Создание шаблонов диспетчера ресурсов Azure с помощью декларативного синтаксиса JSON для развертывания приложений в Azure.
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
ms.date: 07/19/2016
ms.author: tomfitz

---
# Создание шаблонов диспетчера ресурсов Azure
В этой статье описана структура шаблона Azure Resource Manager. Статья содержит информацию о разных разделах шаблона и свойствах, которые доступны в этих разделах. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания.

Указания по созданию шаблона см. в [пошаговом руководстве по созданию шаблона Resource Manager](resource-manager-template-walkthrough.md). Дополнительные рекомендации по созданию шаблонов см. в статье [Best practices for creating Azure Resource Manager templates](resource-manager-template-best-practices.md) (Рекомендации по созданию шаблонов Azure Resource Manager).

Хороший редактор JSON может упростить создание шаблонов. Сведения об использовании шаблонов в Visual Studio см. в статье [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Сведения об использовании VS Code см. в статье [Работа с шаблонами Azure Resource Manager в Visual Studio Code](resource-manager-vs-code.md).

Задайте максимальный размер шаблона, равный 1 МБ, а максимальный размер каждого файла параметров — 64 КБ. Ограничение в 1 МБ применяется к конечному состоянию шаблона после расширения с итеративным определением ресурсов, значениями переменных и параметров.

## Формат шаблона
В самой простой структуре шаблон содержит следующие элементы.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Имя элемента | Обязательно | Описание |
|:---:|:---:|:--- |
| $schema |Да |Расположение файла схемы JSON, который описывает версию языка шаблона. Следует использовать URL-адрес, показанный выше. |
| contentVersion |Да |Версия шаблона (например, 1.0.0.0). Для этого элемента можно предоставить любое значение. При развертывании ресурсов с помощью шаблона это значение позволяет убедиться в том, что используется нужный шаблон. |
| parameters |Нет |Значения, которые предоставляются при выполнении развертывания для настройки развертывания ресурсов. |
| variables |Нет |Значения, используемые в виде фрагментов JSON в шаблоне для упрощения выражений на языке шаблона. |
| ресурсов |Да |Типы ресурсов, которые развертываются или обновляются в группе ресурсов. |
| outputs |Нет |Значения, возвращаемые после развертывания. |

Разделы шаблона мы рассмотрим более подробно далее в этой статье. Сейчас мы рассмотрим элементы синтаксиса, составляющего шаблон.

## Выражения и функции
Базовым синтаксисом шаблона является JSON; однако выражения и функции расширяют JSON, который доступен в шаблоне, и позволяют создавать значения, не являющиеся строгими значениями литералов. Выражения заключаются в квадратные скобки ([ и ]) и вычисляются при развертывании шаблона. Выражения могут встречаться в любом месте строкового значения JSON и всегда возвращают другое значение JSON. Если нужно использовать строковый литерал, который начинается с квадратной скобки ([), необходимо указать две квадратные скобки ([[).

Как правило, выражения используются с функциями для выполнения операций по настройке развертывания. Как и в языке JavaScript, вызовы функций форматируются в виде **functionName(arg1,arg2,arg3)**. Обращение к свойствам производится с помощью точки и операторов [index].

Вот пример использования нескольких функций во время создания значений.

    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Полный список функций шаблонов см. в статье [Функции шаблонов диспетчера ресурсов Azure](resource-group-template-functions.md).

## Параметры
В разделе параметров шаблона указываются значения, которые вы можете вводить во время развертывания ресурсов. Значения этих параметров позволяют настраивать развертывание путем предоставления значений, предназначенных для конкретной среды (например, для среды разработки, тестирования и рабочей среды). Предоставление параметров в шаблоне не является обязательным требованием, однако без параметров шаблон всегда будет развертывать одни и те же ресурсы с одинаковыми именами, расположениями и свойствами.

Эти значения параметров можно использовать по всему шаблону, чтобы задать значения для развернутых ресурсов. В других разделах шаблона можно использовать только параметры, которые объявлены в разделе параметров.

Параметры определяются с помощью следующей структуры.

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ],
         "minValue": <optional-minimum-value-for-int-parameters>,
         "maxValue": <optional-maximum-value-for-int-parameters>,
         "minLength": <optional-minimum-length-for-string-secureString-array-parameters>,
         "maxLength": <optional-maximum-length-for-string-secureString-array-parameters>,
         "metadata": {
             "description": "<optional-description-of-the parameter>" 
         }
       }
    }

| Имя элемента | Обязательно | Описание |
|:---:|:---:|:--- |
| имя\_параметра |Да |Имя параметра. Должно быть допустимым идентификатором JavaScript. |
| type |Да |Тип значения параметра. Ниже приведен список разрешенных типов. |
| defaultValue |Нет |Значение параметра, используемое по умолчанию, если пользователь не задал иное значение. |
| allowedValues |Нет |Массив допустимых значений параметра, по которому сверяются правильные значения. |
| minValue |Нет |Минимальное значение для параметров типа int. Это включающее значение. |
| maxValue |Нет |Максимальное значение для параметров типа int. Это включающее значение. |
| minLength |Нет |Минимальная длина параметров типа string, secureString и array. Это включающее значение. |
| maxLength |Нет |Максимальная длина параметров типа string, secureString и array. Это включающее значение. |
| description |Нет |Описание параметра, которое будет отображаться для пользователей шаблона через интерфейс пользовательского шаблона портала. |

Допустимые типы и значения:

* string или secureString: любая допустимая строка JSON;
* int: любое допустимое целое число JSON;
* bool: любое допустимое логическое значение JSON;
* object или secureObject: любой допустимый объект JSON;
* array: любой допустимый массив JSON.

Чтобы указать параметр как необязательный, задайте defaultValue (это может быть пустая строка).

Вы можете указать имя параметра, которое совпадает с одним из параметров в команде для развертывания шаблона (например, включить в шаблон параметр с именем **ResourceGroupName**, которое совпадает с именем параметра **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment][deployment2cmdlet]). В таком случае вам будет предложено указать значение параметра с постфиксом **FromTemplate** (например, **ResourceGroupNameFromTemplate**). В общем случае следует избегать этой путаницы, не присваивая параметрам имена параметров, используемых для операций развертывания.

> [!NOTE]
> Все пароли, ключи и другие секреты данные должны использовать тип **secureString**. Параметры шаблона с типом secureString невозможно прочитать после развертывания ресурса.
> 
> 

В следующем примере показано, как определять параметры.

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

Сведения о том, как вводить значения параметров во время развертывания см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md#parameter-file).

## Переменные
В разделе переменных вы создаете значения, которые можно использовать в разных частях шаблона. Как правило, эти переменные основываются на указанных значениях параметров. Переменные определять не обязательно, однако они часто упрощают шаблон, снижая число сложных выражений.

Переменные определяются с помощью следующей структуры:

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

В следующем примере показано, как определить переменную, которая состоит из двух значений параметра.

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

В следующем примере показана переменная, которая является сложным типом JSON, и переменные, построенные на основе других переменных.

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## Ресурсы
В разделе resources определяются ресурсы, которые развертываются или обновляются. Это одна из самых сложных составляющих шаблона, поскольку вы должны понимать принципы работы развертываемых типов для предоставления правильных значений. Дополнительные сведения о поставщиках ресурсов см. в статье [Поставщики Resource Manager, регионы, версии API и схемы](resource-manager-supported-services.md).

Ресурсы определяются с помощью следующей структуры:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Имя элемента | Обязательно | Описание |
|:---:|:---:|:--- |
| версия\_API |Да |Версия REST API, которая будет использована для создания ресурса. Чтобы определить доступные номера версий заданного типа ресурсов, см. раздел [Поддерживаемые версии API](resource-manager-supported-services.md#supported-api-versions). |
| type |Да |Тип ресурса. Это значение представляет собой сочетание пространства имен поставщика ресурсов и типа ресурса, который поддерживается поставщиком ресурсов. |
| name |Да |Имя ресурса. Имя должно соответствовать ограничениям компонентов URI, определенным в RFC3986. Кроме того, службы Azure, которые предоставляют имя ресурса внешним пользователям, проверяют это имя, чтобы убедиться, что это не попытка подделки другого удостоверения. Ознакомьтесь со статьей [Проверка имени ресурса](https://msdn.microsoft.com/library/azure/mt219035.aspx). |
| location |Varies |Поддерживаемые географические расположения указанного ресурса. Сведения о том, как определить доступные расположения, см. в разделе [Поддерживаемые регионы](resource-manager-supported-services.md#supported-regions). Большинству типов ресурсов нужно расположение, но некоторым типам (например, назначению роли) оно не требуется. |
| tags |Нет |Теги, связанные с ресурсом. |
| комментарии |Нет |Заметки по ресурсам в шаблоне |
| Свойство dependsOn |Нет |Ресурсы, от которых зависит определяемый ресурс. Вычисляются зависимости между ресурсами, и ресурсы развертываются в порядке зависимости. Если ресурсы не зависят друг от друга, выполняется попытка их параллельного развертывания. Значение может представлять собой разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов. |
| properties |Нет |Параметры конфигурации для конкретного ресурса. Значения свойств в точности совпадают со значениями, указываемыми в тексте запроса для операции REST API (метод PUT) для создания ресурса. Ссылки на документацию по схеме ресурсов или REST API см. в статье [Поставщики Resource Manager, регионы, версии API и схемы](resource-manager-supported-services.md). |
| ресурсов |Нет |Дочерние ресурсы, которые зависят от определяемого ресурса. Можно указать только те типы ресурсов, которые разрешены для использования в схеме родительского ресурса. Полное имя типа дочернего ресурса содержит тип родительского ресурса, например **Microsoft.Web/sites/extensions**. Зависимость от родительского ресурса не подразумевается; необходимо явно определить нужную зависимость. |

Если имя ресурса не является уникальным, можно использовать вспомогательную функцию **resourceId** (описанную ниже) для получения уникального идентификатора любого ресурса.

Раздел ресурсов содержит набор ресурсов для развертывания. Внутри каждого ресурса можно также определить набор дочерних ресурсов для этого ресурса. Таким образом раздел ресурсов может иметь примерно следующую структуру:

    "resources": [
       {
           "name": "resourceA",
           ...
       },
       {
           "name": "resourceB",
           ...
           "resources": [
               {
                   "name": "firstChildResourceB",
                   ...
               },
               {   
                   "name": "secondChildResourceB",
                   ...
               }
           ]
       },
       {
           "name": "resourceC",
           ...
       }
    ]



В следующем примере показан ресурс **Microsoft.Web/serverfarms**, а также ресурс **Microsoft.Web/sites** с дочерним ресурсом **Extensions**. Обратите внимание, что сайт помечен как зависимый от фермы серверов, поскольку ферма серверов должна существовать перед развертыванием сайта. Кроме того, обратите внимание, что ресурс **Extensions** является дочерним для сайта.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "ARM"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## Выходные данные
В разделе выходных данных следует указать значения, которые возвращаются после развертывания. Например, можно возвращать URI для доступа к развернутому ресурсу.

В следующем примере показана структура определения выходных данных:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| Имя элемента | Обязательно | Описание |
|:---:|:---:|:--- |
| outputName |Да |Имя выходного значения. Должно быть допустимым идентификатором JavaScript. |
| type |Да |Тип выходного значения. Выходные значения поддерживает те же типы, что и входные параметры шаблона. |
| value |Да |Выражение на языке шаблона, которое будет вычислено и возвращено в качестве выходного значения. |

В следующем примере показано значение, которое возвращается в разделе выходных данных.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

Дополнительные сведения о работе с выходными данными см. в статье [Совместное использование состояния в шаблонах Resource Manager](best-practices-resource-manager-state.md).

## Дальнейшие действия
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Инструкции по объединению нескольких шаблонов при развертывании см. в разделе [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа см. в разделе [Создание нескольких экземпляров ресурсов в диспетчере ресурсов Azure](resource-group-create-multiple.md).
* Может потребоваться использовать ресурсы, которые существуют в другой группе ресурсов. Это распространенная ситуация при работе с учетными записями хранения или виртуальными сетями, которые совместно используются в нескольких группах ресурсов. Дополнительные сведения см. в описании [функции resourceId](resource-group-template-functions.md#resourceid).

[deployment2cmdlet]: https://msdn.microsoft.com/library/mt740620(v=azure.200).aspx

<!---HONumber=AcomDC_0928_2016-->