---
title: Вопросы безопасности для диспетчера ресурсов | Microsoft Docs
description: Здесь содержатся рекомендации по защите ресурсов с помощью ключей и секретов, управлению доступом на основе ролей и сетевых групп безопасности при работе с диспетчером ресурсов Azure.
services: azure-resource-manager
documentationcenter: ''
author: george-moore
manager: georgem
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2016
ms.author: georgem;tomfitz

---
# Вопросы безопасности при работе с диспетчером ресурсов Azure
Среди аспектов, определяющих безопасность при работе с шаблонами диспетчера ресурсов Azure, существует несколько ключевых – это ключи и секреты, управление доступом на основе ролей и группы сетевой безопасности.

При работе с материалами этого раздела предполагается, что вы знакомы с механизмом управления доступом на основе ролей (RBAC) диспетчера ресурсов Azure. Дополнительные сведения см. в разделе [Контроль доступа на основе ролей на портале Azure](active-directory/role-based-access-control-configure.md).

Данная тема является частью другого технического документа. Чтобы ознакомиться с полным текстом этого документа, загрузите файл [Рекомендации по работе с шаблонами ARM мирового класса](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates — Considerations and Proven Practices.pdf).

## Сертификаты и секреты
Виртуальные машины Azure, диспетчер ресурсов Azure и хранилище ключей Azure полностью интегрированы для обеспечения безопасной обработки сертификатов, которые должны быть развернуты в виртуальной машине. Использование хранилища ключей Azure с диспетчером ресурсов для настройки и хранения сертификатов и секретов виртуальной машины является наилучшим решением, характеризующееся следующими преимуществами.

* Шаблоны содержат только URI-ссылки на секреты, то есть фактические секретные данные не содержатся в коде, конфигурации или репозиториях исходного кода. Это позволяет предотвращать основные фишинговые атаки на внутренние или внешние репозитории, например с использованием ботов-собирателей на GitHub.
* Секреты, содержащиеся в хранилище ключей, находятся под полным контролем RBAC доверенного оператора. Если доверенный оператор уходит из компании или переходит в другую корпоративную группу, он теряет доступ к созданным в хранилище ключам.
* Полное обособление всех ресурсов:
  * шаблоны для развертывания ключей;
  * шаблоны для развертывания виртуальной машины со ссылками на ключи;
  * материалы фактического ключа в хранилище. Каждый шаблон (и действие) может иметь разные роли RBAC для полного разделения обязанностей.
* Загрузка секретов в виртуальную машину во время развертывания выполняется по прямому каналу между структурой Azure и хранилищем ключей в пределах центра обработки данных Майкрософт. После перемещения в хранилище ключи уже не будут передаваться за пределы центра обработки данных в незащищенном режиме по ненадежному каналу.
* Хранилища ключей всегда региональные, поэтому секреты всегда имеют определенное расположение (и независимый статус) в виртуальной машине. Глобальных хранилищ ключей не существует.

### Разделение ключей из развертываний
Рекомендуется хранить отдельные шаблоны для выполнения следующих действий.

1. Создание хранилищ (которые будут содержать материал ключа).
2. Развертывание виртуальной машины (со ссылками URI для ключей, содержащихся в хранилищах).

Типичный корпоративный сценарий предусматривает небольшую группу доверенных операторов, имеющих доступ к критически важным секретам в пределах развернутых рабочих нагрузок, и более широкую группу персонала по разработке и эксплуатации, которые могут создавать или обновлять развертываемые виртуальные машины. Ниже приведен пример шаблона ARM, который создает и настраивает новое хранилище в контексте удостоверения пользователя, прошедшего проверку в Azure Active Directory. Этому пользователю будет предоставлено разрешение по умолчанию создавать, удалять, создавать список, обновлять, выполнять резервное копирование, восстанавливать и получать общедоступную половину ключей из этого нового хранилища ключей.

Хотя большинство полей в этом шаблоне должны иметь описательные имена, параметр **enableVaultForDeployment** заслуживает дополнительного объяснения: хранилища не имеют постоянного доступа по умолчанию к любому другому компоненту инфраструктуры Azure. Если это значение установлено, оно позволяет компонентам инфраструктуры вычислительной операции Azure получать доступ только для чтения к этому определенному именованному хранилищу. Следовательно, также не рекомендуется помещать корпоративные конфиденциальные данные в одно хранилище наряду с секретами виртуальной машины.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the Vault"
                }
            },
            "location": {
                "type": "string",
                "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
                "metadata": {
                    "description": "Location of the Vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                    "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AD user. Get using Get-AzureADUser cmdlet"
                }
            },
            "skuName": {
                "type": "string",
                "allowedValues": ["Standard", "Premium"],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enableVaultForDeployment": {
                "type": "bool",
                "allowedValues": [true, false],
                "metadata": {
                    "description": "Specifies if the vault is enabled for a VM deployment"
                }
            }
        },
        "resources": [{
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2014-12-19-preview",
            "location": "[parameters('location')]",
            "properties": {
                "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
                "tenantid": "[parameters('tenantId')]",
                "accessPolicies": [{
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "secrets": ["all"],
                        "keys": ["all"]
                    }
                }],
                "sku": {
                    "name": "[parameters('skuName')]",
                    "family": "A"
                }
            }
        }]
    }

Следующим шагом после создания хранилища будет создание ссылки на это хранилище в шаблоне развертывания новой виртуальной машины. Как упоминалось выше, для управления развертываниями виртуальной машины рекомендуется иметь отдельную группу разработчиков без прямого доступа к ключам, содержащимся в хранилище.

Представленный ниже фрагмент шаблона будет включен в конструкции развертывания высшего порядка, надежно и безопасно ссылаясь на конфиденциальные секреты, которые не управляются непосредственно оператором.

    "vaultName": {
        "type": "string",
        "metadata": {
            "description": "Name of Key Vault that has a secret"
        }
    },
    {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[parameters('vmName')]",
        "location": "[parameters('location')]",
        "properties": {
            "osProfile": {
                "secrets": [{
                    "sourceVault": {
                        "id": "[resourceId('vaultrg', 'Microsoft.KeyVault/vaults', 'kayvault')]"
                    },
                    "vaultCertificates": [{
                        "certificateUrl": "[parameters('secretUrlWithVersion')]",
                        "certificateStore": "My"
                    }]
                }]
            }
        }
    }

Для передачи значения из хранилища ключей в качестве параметра во время развертывания шаблона обратитесь к статье [Безопасная передача значений во время развертывания](resource-manager-keyvault-parameter.md).

## Субъекты-службы для взаимодействия между подписками
Удостоверения служб представлены в Active Directory как субъекты-службы. Субъекты-службы будут в центре обеспечения основных сценариев для корпоративных ИТ-организаций, системных интеграторов (SI) и поставщиков облачных услуг (CSV). В частности, будут такие варианты использования, когда одной из этих организаций необходимо будет взаимодействовать с подпиской одного из своих клиентов.

Ваша организация может предоставлять предложения по мониторингу решений, развернутых в клиентской среде в рамках подписки. В этом случае вам будет необходимо получить доступ к журналам и другим данным учетной записи клиентов, чтобы вы могли использовать их в своем решении для мониторинга. Если вы являетесь корпоративной ИТ-организацией или системным интегратором, вы можете предоставить клиенту предложение, в котором вы будете осуществлять развертывание и управлять возможностями клиента, такими как платформа аналитики данных. Это предложение будет содержаться в собственной подписке клиента.

Такие варианты использования предполагают, что вашей организации требуется удостоверение, которое может предоставлять доступ на выполнение этих действий в контексте подписки клиента.

Эти сценарии также включают определенный набор рекомендаций для клиента.

* Из соображений безопасности может потребоваться, чтобы доступ ограничивался определенным типом действий, включая доступ только для чтения.
* Поскольку предоставление развернутых ресурсов это затратный процесс, аналогичные ограничения доступа могут накладываться и по финансовым соображениям.
* Из соображений безопасности может потребоваться, чтобы доступ ограничивался только конкретным ресурсом (учетные записи хранения) или ресурсами (группа ресурсов, содержащая среду или решение).
* Поскольку отношения с поставщиком могут измениться, клиент может захотеть иметь возможность как предоставления доступа системному интегратору или поставщику облачных служб, так и его отзыва.
* Поскольку действия в отношении этой учетной записи связаны с выставлением счетов, клиенту необходимо иметь возможность выполнения аудита и ведения учета для выставления счетов.
* С точки зрения соответствия клиент должен иметь возможность выполнения аудита вашего поведения в рамках своей среды.

Для удовлетворения этих требований можно использовать сочетание субъекта-службы и RBAC.

## Группы безопасности сети
Многие сценарии будут иметь требования, определяющие трафик одного или нескольких экземпляров виртуальной машины в виртуальной сети. Вы можете использовать сетевую группу безопасности (NSG), чтобы во время развертывания шаблона ARM указать, как этот трафик будет контролироваться.

Сетевая группа безопасности – это объект верхнего уровня, связанный с вашей подпиской. Сетевая группа безопасности содержит правила управления доступом, регулирующие трафик экземпляров виртуальной машины. Эти правила можно изменять в любое время, при этом изменения применяются ко всем связанным экземплярам. Для работы с сетевой группой безопасности у вас должна быть виртуальная сеть, связанная с определенным регионом (расположением). Сетевые группы безопасности несовместимы с виртуальными сетями, которые связаны с территориальной группой. Если у вас нет региональной виртуальной сети, но вам нужно управлять трафиком конечных точек, см. статью [Списки контроля доступа к сети (ACL)](virtual-network/virtual-networks-acl.md).

Сетевую группу безопасности можно связать с виртуальной машиной или подсетью в рамках виртуальной сети. Если группа NSG связана с виртуальной машиной, ее правила применяются ко всему входящему и исходящему трафику экземпляра виртуальной машины. Если группа связана с подсетью виртуальной сети, ее правила применяются ко всему входящему и исходящему трафику всех экземпляров виртуальной машины в подсети. Виртуальную машину или подсеть можно связать только с одной сетевой группой безопасности; при этом каждая группа может содержать не более 200 правил. В одной подписке может быть не более 100 групп NSG.

> [!NOTE]
> На одном экземпляре виртуальной машины нельзя одновременно использовать списки ACL для конечных точек и группы NSG. Если вам нужна группа NSG, но у вас уже есть список ACL для конечных точек, сначала удалите этот список. Сведения о том, как это сделать, см. в статье [Управление списками управления доступом для конечных точек с помощью PowerShell](virtual-network/virtual-networks-acl-powershell.md).
> 
> 

### Как работают сетевые группы безопасности
Группы безопасности сети отличаются от списков ACL для конечных точек. Списки ACL работают только с общим портом входной конечной точки. Группа NSG работает с экземплярами виртуальных машин и управляет всем трафиком, который виртуальная машина принимает и отправляет.

Сетевая группа безопасности имеет *имя*; она связана с *регионом* (одно из поддерживаемых расположений Azure) и имеет описательную метку. Группа содержит правила двух типов: для входящего и исходящего трафика. Правила для входящего трафика применяются к входящим пакетам виртуальной машины, а правила для исходящего трафика – к исходящим пакетам. Правила применяются на сервере, где расположена виртуальная машина. Входящий или исходящий пакет должен соответствовать разрешающему правилу; в противном случае пакет удаляется.

Правила обрабатываются в порядке приоритета. Например, правило с меньшим номером приоритета (например, 100) обрабатывается раньше правила с большим номером приоритета (например, 200). Если обнаружено совпадение, дальнейшая обработка правил прекращается.

В правиле указываются такие данные:

* Имя: уникальный идентификатор правила.
* Тип: входящий или исходящий трафик.
* Приоритет: целочисленное значение от 100 до 4096 (правила обрабатываются от низких к высоким значениям)
* Исходный IP-адрес: маршрутизация CIDR для исходного диапазона IP-адресов.
* Исходный диапазон портов: целое число в диапазоне от 0 до 65 536.
* Конечный диапазон IP-адресов: маршрутизация CIDR для конечного диапазона IP-адресов.
* Целевой диапазон портов: целое число в диапазоне от 0 до 65 536.
* Протокол: TCP, UDP или «*».
* Доступ: разрешение или запрет.

### Правила по умолчанию
Группа NSG содержит правила по умолчанию. Эти правила нельзя удалить, но, поскольку у них самый низкий приоритет, их можно переопределить, создав другие правила. В правилах по умолчанию определены рекомендуемые платформой параметры. Как показано в таблице правил по умолчанию ниже, входящий и исходящий трафик виртуальной сети разрешен в обоих направлениях.

Хотя подключение к Интернету в исходящем направлении разрешено, входящее направление по умолчанию заблокировано. Правило по умолчанию позволяет балансировщику нагрузки Azure выполнять проверку работоспособности виртуальной машины. Вы можете переопределить это правило, если виртуальная машина или набор виртуальных машин в группе NSG не входят в набор с балансировкой нагрузки.

В таблицах ниже приведены правила по умолчанию.

**Правила по умолчанию для входящего трафика**

| Имя | Приоритет | Исходный IP-адрес | Исходный порт | Конечный IP-адрес | Конечный порт | Протокол | Доступ |
| --- | --- | --- | --- | --- | --- | --- | --- |
| РАЗРЕШИТЬ ВХОДЯЩИЙ ТРАФИК ВИРТУАЛЬНОЙ СЕТИ |65000 |VIRTUAL\_NETWORK |* |VIRTUAL\_NETWORK |* |* |РАЗРЕШИТЬ |
| РАЗРЕШИТЬ ВХОДЯЩИЙ ТРАФИК БАЛАНСИРОВЩИКА НАГРУЗКИ AZURE |65001 |AZURE\_LOADBALANCER |* |* |* |* |РАЗРЕШИТЬ |
| ЗАПРЕТИТЬ ВЕСЬ ВХОДЯЩИЙ ТРАФИК |65500 |* |* |* |* |* |ЗАПРЕТИТЬ |

**Правила по умолчанию для исходящего трафика**

| Имя | Приоритет | Исходный IP-адрес | Исходный порт | Конечный IP-адрес | Конечный порт | Протокол | Доступ |
| --- | --- | --- | --- | --- | --- | --- | --- |
| РАЗРЕШИТЬ ИСХОДЯЩИЙ ТРАФИК ВИРТУАЛЬНОЙ СЕТИ |65000 |VIRTUAL\_NETWORK |* |VIRTUAL\_NETWORK |* |* |РАЗРЕШИТЬ |
| РАЗРЕШИТЬ ИСХОДЯЩИЙ ИНТЕРНЕТ-ТРАФИК |65001 |* |* |ИНТЕРНЕТ |* |* |РАЗРЕШИТЬ |
| ЗАПРЕТИТЬ ВЕСЬ ИСХОДЯЩИЙ ТРАФИК |65500 |* |* |* |* |* |ЗАПРЕТИТЬ |

### Специальные правила для инфраструктуры
Правила NSG являются явными. Трафик запрещается или разрешается только в соответствии с правилами NSG. Но существует два типа трафика, которые разрешены всегда независимо от спецификации сетевой группы безопасности. Это требуется для обеспечения надлежащей работы инфраструктуры.

* **Виртуальный IP-адрес главного узла**: базовые службы инфраструктуры, включая DHCP, DNS и службу наблюдения за работоспособностью системы, работают через IP-адрес виртуализированного узла 168.63.129.16. Этот общедоступный IP-адрес принадлежит корпорации Майкрософт. Он является единственным виртуализированным IP-адресом, используемым для этих целей во всех регионах. Адрес сопоставляется с физическим IP-адресом сервера (главного узла), на котором размещена виртуальная машина. Главный узел выполняет функции ретранслятора DHCP, рекурсивного сопоставителя DNS-имен и источника проб работоспособности, инициируемых балансировщиком нагрузки и виртуальными машинами. Обмен данными с этим IP-адресом не является атакой.
* **Лицензирование (служба управления ключами)**: для используемых на виртуальных машинах образов Windows требуется лицензия. С этой целью на соответствующие серверы узлов, на которых работает служба управления ключами, отправляется запрос на получение лицензии. Для этого всегда используется исходящий порт 1688.

### Теги по умолчанию
Теги по умолчанию – это системные идентификаторы для определения категорий IP-адресов. Теги задаются в пользовательских правилах.

**Теги по умолчанию для сетевых групп безопасности**

| Тег | Описание |
| --- | --- |
| VIRTUAL\_NETWORK |Обозначает все адресное пространство сети. Сюда входят адресное пространство виртуальной сети (маршрутизация CIDR IP-адресов в Azure) и все адресное пространство подключенных локальных сетей. Сюда также входит адресное пространство между виртуальными сетями. |
| AZURE\_LOADBALANCER |Обозначает балансировщик нагрузки инфраструктуры Azure; преобразовывается в IP-адрес центра обработки данных Azure, где инициируются пробы работоспособности Azure. Тег необходим, только если связанная с группой NSG виртуальная машина или набор виртуальных машин входит в состав набора с балансировкой нагрузки. |
| ИНТЕРНЕТ |Обозначает пространство IP-адресов, которые находятся за пределами виртуальной сети и к которым можно получить доступ из общедоступного сегмента Интернета. К этим IP-адресам относится также общедоступный IP-адрес, принадлежащий Azure. |

### Порты и их диапазоны
Правила сетевой группы безопасности можно задавать как для одного исходного или конечного порта, так и для целого диапазона портов. Это особенно полезно, когда для какого-то приложения нужно открыть широкий диапазон портов (например, FTP). Диапазон должен состоять только из последовательного набора номеров портов; он не должен включать спецификации отдельных портов. Чтобы указать диапазон портов, используйте дефис (-). Например, **100–500**.

### ICMP-трафик
В текущих правилах сетевой группы безопасности в качестве протоколов можно указать TCP или UDP, но не ICMP. Тем не менее по умолчанию ICMP-трафик в виртуальной сети разрешен правилами для входящего трафика виртуальной сети. Эти правила поддерживают входящий и исходящий трафик в виртуальной сети на любом порте и по любому протоколу («*»).

### Связывание сетевой группы безопасности с виртуальной машиной
Когда сетевая группа безопасности напрямую связана с виртуальной машиной, правила доступа к сети в этой группе применяются ко всему входящему трафику виртуальной машины. Когда правила группы сетевой группы безопасности обновляются, новые параметры трафика вступают в силу через несколько минут. Когда группа сетевой безопасности отсоединяется от виртуальной машины, состояние возвращается к предыдущему, то есть к настройкам системы по умолчанию до присоединения сетевой группы безопасности.

### Связывание сетевой группы безопасности с подсетью
Когда сетевая группа безопасности связывается с подсетью, правила доступа к сети в этой группе применяются ко всем виртуальным машинам подсети. Когда правила доступа в сетевой группе безопасности меняются, изменения вступают в силу через несколько минут.

### Связывание сетевой группы безопасности с подсетью и виртуальной машиной
Одну сетевую группу безопасности можно связать с виртуальной машиной и другой сетевой группой безопасности в рамках подсети, в которой находится виртуальная машина. Этот сценарий поддерживается для обеспечения двух уровней защиты виртуальной машины. Во входящем трафике пакет сначала управляется правилами доступа, заданными в подсети, а затем – правилами, определенными в виртуальной машине. В исходящем трафике пакет сначала управляется правилами, заданными в виртуальной машине, а затем – правилами, определенными в подсети, как показано ниже.

![Связывание сетевой группы безопасности с подсетью и виртуальной машиной](./media/best-practices-resource-manager-security/nsg-subnet-vm.png)

Когда сетевая группа безопасности связана с виртуальной машиной или подсетью, правила управления доступом к сети становятся очень явными. Платформа не будет использовать неявные правила, чтобы разрешить трафик к тому или иному порту. В таком случае, если вы создадите конечную точку виртуальной машины, вам также нужно будет создать правило, разрешающее входящий трафик из Интернета. Если этого не сделать, значение *VIP:{порт}* будет недоступно извне.

Рассмотрим пример. Вы создаете виртуальную машину и сетевую группу безопасности, а затем связываете их. Обмен данными между этой виртуальной машиной и другими виртуальными машинами в виртуальной сети регулируется правилом «РАЗРЕШИТЬ ВХОДЯЩИЙ ТРАФИК ВИРТУАЛЬНОЙ СЕТИ». Кроме того, правило «РАЗРЕШИТЬ ИСХОДЯЩИЙ ИНТЕРНЕТ-ТРАФИК» разрешает виртуальной машине устанавливать исходящее подключение к Интернету. Затем вы создаете конечную точку на порте 80, чтобы разрешить входящий трафик к веб-сайту, который запущен на виртуальной машине. Пакеты, отправляемые из Интернета на порт 80 общедоступного виртуального IP-адреса, не будут пропускаться на виртуальную машину, пока вы не создадите в сетевой группе безопасности примерно такое правило.

**Явное правило, разрешающее трафик к конкретному порту**

| Имя | Приоритет | Исходный IP-адрес | Исходный порт | Конечный IP-адрес | Конечный порт | Протокол | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ВЕБ |100 |ИНТЕРНЕТ |* |* |80 |TCP |РАЗРЕШИТЬ |

## Определяемые пользователем маршруты
Azure использует таблицу маршрутизации, чтобы определить, как пересылать IP-трафик на основе места назначения каждого пакета. Хотя Azure предоставляет таблицу маршрута по умолчанию на основе заданных параметров виртуальной сети, в нее может потребоваться добавить пользовательские маршруты.

Чаще всего потребность в пользовательских записях в таблице маршрутов связана с использованием виртуального устройства в среде Azure. Рассмотрите сценарий, показанный на рисунке ниже. Предположим, что нужно убедиться, что весь трафик, направленный в подсети среднего уровня и резервные подсети, инициируемый из подсети интерфейса, проходит через брандмауэр виртуального устройства. Это не получится сделать, просто добавив устройство в виртуальную сеть и подключив его к разным подсетям. Необходимо также изменить таблицу маршрутизации, применяемую к подсети, чтобы пакеты перенаправлялись в брандмауэр виртуального устройства.

Это так же верно, если вы внедрили виртуальное устройство NAT для контроля трафика между виртуальной сетью Azure и Интернетом. Чтобы убедиться, что виртуальное устройство используется, необходимо создать маршрут, указав, что весь трафик, предназначенный для Интернета, должен передаваться в виртуальное устройство.

### Маршрутизация
Пакеты маршрутизируются по сети TCP/IP на основе таблицы маршрутов, определенной на каждом узле физической сети. Таблица маршрутов — набор отдельных маршрутов, с помощью которой определяется, куда пересылать пакеты по IP-адресу назначения. Маршрут состоит из следующих частей:

* Префикс адреса. Маршрутизация CIDR назначения, к которой относится маршрут, например 10.1.0.0/16.
* Тип следующего прыжка. Тип прыжка Azure, куда должны отправляться пакеты. Возможные значения:
  * Локальный. Представляет локальную виртуальную сеть. Например, при наличии двух подсетей (10.1.0.0/16 и 10.2.0.0/16) в одной и той же виртуальной сети следующий прыжок маршрута для каждой подсети в таблице маршрутов будет иметь значение «Локальный».
  * VPN-шлюз. Представляет шлюз VPN типа «сеть-сеть» Azure.
  * Интернет. Представляет используемый по умолчанию шлюз Интернета, предоставленный инфраструктурой Azure.
  * Виртуальное устройство. Представляет виртуальное устройство, добавленное в виртуальную сеть Azure.
  * NULL. Представляет «черную дыру». Пакеты, пересылаемые в «черную дыру», вообще никуда не пересылаются.
* Значение следующего прыжка. Значение следующего прыжка содержит IP-адрес, на который должны быть переадресованы пакеты. Значения следующего прыжка допускаются только в маршрутах, где следующий тип перехода — *Виртуальное устройство*. Следующий прыжок должен быть в подсети (на локальном интерфейсе виртуального устройства в соответствии с идентификатором сети), а не в удаленной подсети.

![Маршрутизация](./media/best-practices-resource-manager-security/routing.png)

### Маршруты по умолчанию
Каждая подсеть, созданная в виртуальной сети, автоматически связывается с таблицей маршрутов, которая содержит следующие правила по умолчанию для маршрутов.

* Локальное правило виртуальной сети: это правило создается автоматически для каждой подсети в виртуальной сети. Оно указывает, что существует прямая связь между виртуальными машинами в виртуальной сети, без промежуточного следующего прыжка. Благодаря этому виртуальные машины в той же подсети, независимо от идентификатора сети, в которой существуют виртуальные машины, могут взаимодействовать друг с другом, даже если не указан адрес шлюза по умолчанию.
* Правило локальной среды: это правило применяется для всего трафика, предназначенного для локального диапазона адресов; оно использует VPN-шлюз в качестве назначения следующего прыжка.
* Правило Интернета: это правило обрабатывает весь трафик, направляемый в общедоступный Интернет; оно использует шлюз Интернета инфраструктуры в качестве следующего прыжка для всего трафика, предназначенного для Интернета.

### Маршруты BGP
На момент написания этой статьи [ExpressRoute](expressroute/expressroute-introduction.md) еще не поддерживается в [поставщике сетевых ресурсов](virtual-network/resource-groups-networking.md) для диспетчера ресурсов Azure. При наличии подключения ExpressRoute между локальной сетью и Azure можно включить BGP, чтобы распространить маршруты из локальной сети в Azure, как только ExpressRoute будет поддерживаться сетевой группой безопасности. Эти маршруты BGP используются так же, как и маршруты по умолчанию и определенные пользователем маршруты в каждой подсети Azure. Дополнительную информацию см. в разделе [Введение в ExpressRoute](expressroute/expressroute-introduction.md).

> [!NOTE]
> Когда будет реализована поддержка ExpressRoute в NRP, можно будет настроить среду Azure для использования принудительного туннелирования через локальную сеть путем создания определенного пользователем маршрута для подсети 0.0.0.0/0, который будет использовать VPN-шлюз в качестве следующего прыжка. Тем не менее, это работает только при использовании шлюза VPN, не ExpressRoute. Для ExpressRoute принудительное туннелирование настраивается с помощью BGP.
> 
> 

### Определяемые пользователем маршруты
Невозможно просмотреть маршруты по умолчанию, указанных выше, в среде Azure, и для большинства сред достаточно только этих маршрутов. Тем не менее, в конкретных случаях может потребоваться создать таблицу маршрутов и добавить один или несколько маршрутов, например:

* Принудительное туннелирование в Интернет через вашу локальную сеть.
* Использование виртуальных устройств в среде Azure.

В приведенном выше сценарии необходимо будет создать таблицу маршрутов и добавить в нее определяемые пользователем маршруты. Можно использовать несколько таблиц маршрутов, и одна таблица маршрутов может быть связана с одной или несколькими подсетями. А каждая подсеть может быть связана только с одной таблицей маршрутов. Все виртуальные машины и облачные службы в подсети используют таблицу маршрутов, связанную с этой подсетью.

Пока подсетью не связана таблица маршрутов, она использует маршруты по умолчанию. После установления связи маршрутизация выполняется на основе [совпадения наиболее длинного префикса (LPM)](https://en.wikipedia.org/wiki/Longest_prefix_match) среди определенных пользователем маршрутов и маршрутов по умолчанию. При наличии более одного маршрута с одинаковыми совпадающими значениями LPM маршрут выбирается по источнику в следующем порядке:

1. определяемый пользователем маршрут;
2. маршрут BGP (если используется ExpressRoute);
3. маршрут по умолчанию.

> [!NOTE]
> Определяемые пользователем маршруты применяются только для виртуальных машин и облачных служб Azure. Например, если нужно добавить виртуальное устройство брандмауэра между локальной сетью и Azure, необходимо создать определенный пользователем маршрут для ваших таблиц маршрутов Azure, который будет перенаправлять на виртуальное устройство весь трафик, поступающий в локальное адресное пространство. Однако входящий трафик из локального адресного пространства будет проходить через ваш шлюз VPN или канал ExpressRoute непосредственно в среду Azure, обходя виртуальное устройство.
> 
> 

### IP-пересылка
Как описано выше, одной из основных причин для создания пользовательского маршрута является переадресация трафика на виртуальное устройство. Виртуальное устройство — это просто виртуальная машина, которая запускает приложение, используемое для обработки сетевого трафика определенным образом, например, как брандмауэр или устройство NAT.

Эта виртуальная машина виртуального устройства должна иметь возможность получать входящий трафик, предназначенный не ей. Чтобы разрешить виртуальной машине получать трафик, направленный в другие назначения, в ней необходимо включить IP-пересылку.

## Дальнейшие действия
* Сведения о настройке субъектов безопасности с нужным уровнем доступа для работы с ресурсами в организации см. в разделе [Проверка подлинности субъекта-службы с помощью диспетчера ресурсов Azure](resource-group-authenticate-service-principal.md).
* Если необходимо заблокировать доступ к ресурсу, можно воспользоваться блокировкой управления. См. статью [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).
* Сведения о настройке маршрутизации и переадресации IP-адресов см. в разделе [Создание определяемых пользователем маршрутов (UDR) в диспетчере ресурсов с помощью шаблона](virtual-network/virtual-network-create-udr-arm-template.md).
* Общие сведения об управлении доступом на основе ролей см. в статье [Управление доступом на основе ролей на портале Microsoft Azure](active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0803_2016-->