---
title: Общие сведения о диспетчере ресурсов Azure | Microsoft Docs
description: Описывает, как использовать диспетчер ресурсов Azure для развертывания, контроля ресурсов в Azure и управления доступом к ним.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2016
ms.author: tomfitz

---
# Общие сведения о диспетчере ресурсов Azure
Обычно инфраструктура приложения состоит из ряда компонентов, например из виртуальной машины, учетной записи хранения и виртуальной сети или веб-приложения, базы данных, сервера базы данных и служб сторонних поставщиков. Эти компоненты не отображаются как отдельные сущности, вместо этого они воспринимаются как связанные и взаимозависимые части одной сущности. Их нужно развертывать, контролировать и отслеживать в виде группы. Диспетчер ресурсов Azure позволяет работать с ресурсами решения как с группой. Вы можете развертывать, обновлять или удалять все ресурсы решения в рамках одной скоординированной операции. Развертывание осуществляется на основе шаблона, используемого для разных сред, в том числе для тестовой, промежуточной и рабочей. Диспетчер ресурсов предоставляет функции безопасности, аудита и добавления тегов, помогающие управлять ресурсами после развертывания.

## Терминология
Если у вас еще нет опыта работы с Azure Resource Manager, возможно, некоторые термины окажутся незнакомыми.

* **Ресурс** — управляемый элемент, доступный в Azure. Самые распространенные ресурсы — виртуальная машина, учетная запись хранения, веб-приложение, база данных и виртуальная сеть, но существуют и многие другие.
* **Группа ресурсов** — контейнер, содержащий связанные ресурсы для решения Azure. В группу ресурсов могут входить все ресурсы приложения или только те, которыми необходимо управлять совместно. Кроме того, пользователи могут выбрать оптимальный для своей организации способ распределения ресурсов в группах ресурсов. См. раздел [Группы ресурсов](#resource-groups).
* **Поставщик ресурсов** — это служба, которая предоставляет ресурсы для развертывания и управления посредством Resource Manager. Каждый поставщик ресурсов предоставляет операции REST API для работы с развернутыми ресурсами. К популярным поставщикам ресурсов относится служба Microsoft.Compute, предоставляющая ресурсы виртуальных машин, служба Microsoft.Storage, предоставляющая ресурсы учетных записей хранения, и служба Microsoft.Web, которая предоставляет ресурсы, связанные с веб-приложениями. См. раздел [Поставщики ресурсов](#resource-providers).
* **Шаблон Resource Manager** — файл в формате JSON (нотация объектов JavaScript), определяющий один или несколько ресурсов для развертывания в группе ресурсов. Он также определяет зависимости между развернутыми ресурсами. Шаблон можно использовать для согласованного и многократного развертывания ресурсов. См. раздел [Развертывание шаблона](#template-deployment).
* **Декларативный синтаксис** — синтаксис, позволяющий указать объект, который вы собираетесь создать. При этом для создания объекта не нужно писать последовательность команд. Шаблон Resource Manager — пример декларативного синтаксиса. В файле можно задать свойства для инфраструктуры, развертываемой в Azure.

## Преимущества использования диспетчера ресурсов
Диспетчер ресурсов предоставляет несколько преимуществ.

* Вы можете развертывать и отслеживать все ресурсы вашего решения как единую группу, а не работать с ними по отдельности.
* Вы можете повторно развертывать решение на протяжении всего цикла разработки и гарантировать, что ресурсы развертываются в согласованном состоянии.
* Инфраструктурой можно управлять с помощью декларативных шаблонов, а не сценариев.
* Вы можете определять зависимости между ресурсами, так чтобы они разворачивались в правильном порядке.
* Вы можете применить контроль доступа ко всем службам, так как контроль доступа на основе ролей (RBAC) изначально интегрирован в платформу управления.
* Для логического упорядочивания всех ресурсов в вашей подписке к ним можно применять теги.
* Чтобы узнать о выставлении счетов для вашей организации, просмотрите затраты на группу ресурсов с одним тегом.

Диспетчер ресурсов предоставляет новый способ развертывания ваших решений и управления ими. Если вы использовали более раннюю модель развертывания и хотите узнать о произошедших изменениях, см. статью [Общие сведения о развертывании диспетчера ресурсов и классическом развертывании](resource-manager-deployment-model.md).

## Руководство
Следующие рекомендации помогут использовать все преимущества Resource Manager при работе с решениями.

1. Определите и разверните инфраструктуру с помощью декларативного синтаксиса в шаблонах диспетчера ресурсов, а не с помощью безусловных команд.
2. Определите все шаги развертывания и настройки в шаблоне. При настройке решения вы не должны выполнять какие-либо действия вручную.
3. Используйте безусловные команды для управления ресурсами, например для запуска и остановки приложения или компьютера.
4. Объедините ресурсы с одинаковым жизненным циклом в группу ресурсов. Для упорядочивания всех остальных ресурсов используйте теги.

Дополнительные сведения см. в статье [Best practices for creating Azure Resource Manager templates](resource-manager-template-best-practices.md) (Рекомендации по созданию шаблонов Azure Resource Manager).

## Группы ресурсов
Существуют некоторые важные факторы, которые необходимо учитывать при определении группы ресурсов:

1. Все ресурсы в группе должны совместно использовать один и тот же жизненный цикл. Развертывание, обновление и удаление производится сразу для всех ресурсов. Если один ресурс, например сервер базы данных, должен присутствовать в другом цикле развертывания, его следует поместить в другую группу ресурсов.
2. Каждый ресурс может существовать только в одной группе ресурсов.
3. Ресурс можно добавить в группу ресурсов или удалить из нее в любое время.
4. Ресурс можно перемещать из одной группы ресурсов в другую. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).
5. Группа ресурсов может содержать ресурсы, которые находятся в разных регионах.
6. Группу ресурсов можно использовать, чтобы определить область действия управления доступом для административных действий.
7. Ресурс может взаимодействовать с ресурсом в других группах. Это взаимодействие чаще всего происходит, если эти два ресурса связаны, но имеют разные жизненные циклы (например, веб-приложения, подключенные к базе данных).

При создании группы ресурсов необходимо указать ее расположение. У пользователя может возникнуть вопрос, зачем для группы ресурсов нужно расположение. И если ресурсы и группа ресурсов могут находиться в разных расположениях, то какой смысл указывать расположение группы ресурсов? В группе ресурсов хранятся метаданные о ресурсах. Исходя из этого, при указании расположения группы ресурсов вы определяете расположение метаданных. В целях обеспечения соответствия необходимо убедиться, что данные хранятся в определенном регионе.

## Поставщики ресурсов
Каждый поставщик ресурсов предоставляет набор ресурсов и операций для работы с техническим комплексом. Например, если вы хотите хранить ключи и секреты, вам придется работать с поставщиком ресурсов **Microsoft.KeyVault**. Этот поставщик ресурсов предлагает тип ресурса с именем **vaults** для создания хранилища ключей и тип ресурса с именем **vaults/secrets** для создания секрета в хранилище ключей. Он также предоставляет операции с помощью [операций REST API хранилища ключей](https://msdn.microsoft.com/library/azure/dn903609.aspx). Для управления хранилищем ключей можно вызвать REST API напрямую или использовать [командлеты PowerShell хранилища ключей](https://msdn.microsoft.com/library/dn868052.aspx) и [интерфейс командной строки Azure для хранилища ключей](key-vault/key-vault-manage-with-cli.md). Кроме того, для работы с большинством ресурсов можно использовать несколько языков программирования. Дополнительные сведения см. в разделе [Пакеты SDK и примеры](#sdks-and-samples).

Чтобы развернуть инфраструктуру и управлять ею, необходимо получить сведения о поставщике ресурсов: типы ресурсов, номера версий для операций REST API, поддерживаемые операции и схему создания ресурсов. Дополнительные сведения о поставщиках поддерживаемых ресурсов см. в статье [Поставщики Resource Manager, регионы, версии API и схемы](resource-manager-supported-services.md).

## Развертывание шаблона
С помощью Resource Manager можно создать шаблон (в формате JSON), определяющий развертывание и конфигурацию приложения. С помощью шаблона можно повторно развернуть приложение на протяжении всего его жизненного цикла и гарантировать, что ресурсы развертываются в согласованном состоянии. Azure Resource Manager анализирует зависимости, чтобы обеспечить создание ресурсов в правильном порядке. Дополнительные сведения см. в статье [Определение зависимостей в шаблонах диспетчера ресурсов Azure](resource-group-define-dependencies.md).

Если решение создается на портале, оно автоматически включает в себя шаблон развертывания. Вам не нужно создавать шаблон с нуля, поскольку можно начать с шаблона для своего решения и настроить его в соответствии с конкретными потребностями. Шаблон для имеющейся группы ресурсов можно получить, либо экспортировав текущее состояние группы ресурсов, либо просмотрев шаблон, использованный для конкретного развертывания. Чтобы лучше понять синтаксис шаблона, изучите экспортированный шаблон. Дополнительные сведения о работе с экспортированными шаблонами см. в статье [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).

Вам не нужно определять всю инфраструктуру в одном шаблоне. Во многих случаях целесообразно разделять требования к развертыванию между несколькими наборами шаблонов, предназначенными для определенных задач. Эти шаблоны можно многократно использовать для различных решений. Чтобы развернуть конкретное решение, создайте главный шаблон, связывающий все необходимые шаблоны. Дополнительные сведения см. в статье [Использование связанных шаблонов в диспетчере ресурсов Azure](resource-group-linked-templates.md).

Кроме того, шаблон можно использовать для внесения обновлений в инфраструктуру. Например, можно добавить новый ресурс в решение и правила конфигурации для уже развернутых ресурсов. Если шаблон указывает создание ресурса, но этот ресурс уже существует, то Azure Resource Manager обновляет его, а не создает новый. Диспетчер ресурсов Azure обновляет существующий актив до того состояния, который бы имел новый ресурс. Или можно дать диспетчеру ресурсов инструкцию удалять все ресурсы, которые не указаны в шаблоне. Чтобы понять отличия параметров при развертывании, см. статью [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).

В шаблоне можно указать параметры, чтобы сделать развертывание настраиваемым и гибким. Например, можно передать значения параметров, которые подстраивают развертывание под тестовую среду. Задавая параметры, можно использовать один и тот же шаблон для развертывания решения в разных средах.

Диспетчер ресурсов предоставляет расширения для сценариев, когда необходимы дополнительные операции, такие как установка определенного программного обеспечения, не включенного в программу установки. Если вы уже используете службу управления конфигурацией, такую как DSC, Chef или Puppet, можете продолжить работу с ней с помощью расширений.

Наконец, шаблон становится частью исходного кода для вашего приложения. Можно включить его в репозиторий исходного кода и обновлять по мере развития приложения. Шаблон можно изменить в Visual Studio.

Дополнительную информацию об определении шаблона см. в разделе [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).

Инструкции по созданию шаблона см. в статье [Пошаговое руководство по созданию шаблона Resource Manager](resource-manager-template-walkthrough.md).

Инструкции по развертыванию своего решения в различных средах см. в статье [Среды разработки и тестирования в Microsoft Azure](solution-dev-test-environments.md).

## Теги
Диспетчер ресурсов предоставляет возможность добавления тегов, которая позволяет классифицировать ресурсы в соответствии с вашими требованиями к управлению или выставлению счетов. Используйте теги, если у вас есть сложная коллекция ресурсов и групп ресурсов и требуется визуализировать эти ресурсы самым оптимальным для вас образом. Например, можно снабдить тегами ресурсы, которые выполняют в организации одну и ту же роль или относятся к одному отделу. Без тегов пользователи в организации могут создать несколько ресурсов, которые впоследствии будет трудно идентифицировать. То же касается и управления такими ресурсами. Например, вы хотите удалить все ресурсы для конкретного проекта. Если к этим ресурсам не добавлены проектные теги, ресурсы придется искать вручную. Добавление тегов может позволить сократить затраты при работе с подпиской.

Один тег могут иметь ресурсы, находящиеся в разных группах ресурсов. Можно создать собственную классификацию тегов, чтобы все пользователи в организации использовали одинаковые теги и никто из них не мог случайно указать немного отличающиеся теги (например, "отдел" вместо "отделения").

Дополнительные сведения о тегах см. в статье [Использование тегов для организации ресурсов Azure](resource-group-using-tags.md). Вы можете создать [настроенную политику](#manage-resources-with-customized-policies), которая предусматривает добавление тегов в ходе развертывания.

## управление доступом;
Диспетчер ресурсов позволяет управлять доступом к определенным действиям внутри вашей организации. Он по умолчанию интегрирует OAuth и управление доступом на основе ролей (RBAC) в платформу управления и применяет такое управление доступом ко всем службам в группе ресурсов. Вы можете добавить пользователей в заранее определенную платформу и относящиеся к конкретному ресурсу роли, а также применить эти роли к подписке, группе ресурсов или ресурсу для ограничения доступа. Например, можно воспользоваться преимуществами предварительно определенной роли с именем "Участник баз данных SQL", которая позволяет пользователям управлять базами данных, но не серверами баз данных или политиками безопасности. Вы добавляете пользователей организации, которым требуется такой тип доступа, в роль "Участник баз данных SQL" и применяете роль к подписке, группе ресурсов или ресурсу.

Диспетчер ресурсов автоматически регистрирует действия пользователя для аудита. Дополнительные сведения о работе с журналами аудита см. в статье [Операции аудита с помощью Resource Manager](resource-group-audit.md).

Дополнительные сведения о контроле доступа на основе ролей см. в статье [Контроль доступа на основе ролей Azure Active Directory](active-directory/role-based-access-control-configure.md). Статья [RBAC: встроенные роли](active-directory/role-based-access-built-in-roles.md) содержит список встроенных ролей и разрешенных действий. К встроенным ролям относятся общие роли, такие как владелец, читатель и участник, а также роли для конкретной службы, например участник виртуальной машины, участник виртуальной сети и диспетчер безопасности SQL (и это только незначительная часть существующих ролей).

Можно также явно блокировать критически важные ресурсы, чтобы запретить пользователям удалять или изменять их. Дополнительные сведения см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).

Рекомендации см. в статье [Вопросы безопасности при работе с диспетчером ресурсов Azure](best-practices-resource-manager-security.md).

## Настраиваемые политики управления ресурсами
Диспетчер ресурсов позволяет создавать настраиваемые политики для управления ресурсами. Типы создаваемых политик могут охватывать самые разные сценарии, включая применение соглашения об именовании ресурсов, ограничение типов и экземпляров развертываемых ресурсов, а также ограничение регионов, в которых могут размещаться ресурсы. Для ресурсов можно задать значение тега, чтобы реализовать выставление счетов по отделам. Политики создаются для того, чтобы снижать затраты и поддерживать согласованность в подписке. Дополнительные сведения см. в статье [Применение политик для управления ресурсами и контроля доступа](resource-manager-policy.md).

## Уровень согласованного управления
Resource Manager предоставляет совместимые операции в рамках Azure PowerShell, интерфейса командной строки Azure для Mac, Linux и Windows, портала Azure и REST API. Можно использовать тот интерфейс, который подходит вам лучше всего, и быстрого переключаться между интерфейсами безо всякой путаницы.

Сведения о PowerShell см. в разделах [Использование Windows PowerShell с диспетчером ресурсов](powershell-azure-resource-manager.md) и [Командлеты диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn757692.aspx).

(Информацию об интерфейсе командной строки Azure см. в разделе [Использование интерфейса командной строки Azure для Mac, Linux и Windows со средствами управления ресурсами Azure](xplat-cli-azure-resource-manager.md).)

Сведения о API REST см. в разделе [Справочник по API REST диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx). Дополнительные сведения об операциях REST для развернутых ресурсов см. в статье [Использование обозревателя ресурсов Azure для просмотра и изменения ресурсов](resource-manager-resource-explorer.md).

Сведения об использовании портала см. в разделе [Развертывание ресурсов с использованием шаблонов Resource Manager и портала Azure](resource-group-template-deploy-portal.md).

Диспетчер ресурсов Azure поддерживает общий доступ к ресурсам независимо от источника (CORS). Доступ CORS позволяет вызывать REST API диспетчера ресурсов или REST API служб Azure из веб-приложения, которое расположено в другом домене. Без поддержки CORS веб-браузер запрещает приложению, расположенному в одном домене, обращаться к ресурсам, расположенным в другом домене. Диспетчер ресурсов включает доступ CORS для всех запросов с учетными данными, прошедшими проверку подлинности.

## Пакеты SDK
Пакеты SDK для Azure доступны для нескольких языков и платформ. Каждая из этих языковых реализаций доступна в диспетчере пакетов экосистемы и на сайте GitHub.

Код в каждом из этих пакетов SDK генерируется из спецификаций RESTful API Azure. Эти спецификации с открытым исходным кодом основаны на спецификации Swagger 2.0. Код пакета SDK создается в проекте с открытым исходным кодом под названием AutoRest. AutoRest преобразует эти спецификации RESTful API в клиентские библиотеки на нескольких языках. Если требуется изменить какие-либо аспекты созданного кода в пакетах SDK, можно воспользоваться открытым, бесплатным, поддерживающим распространенный формат спецификации API набором средств для создания пакетов SDK.

Вот наши репозитории пакета SDK Open Source. Присылайте отзывы, сообщения о проблемах и запросы на включение внесенных изменений.

[.NET](https://github.com/Azure/azure-sdk-for-net) | [Java](https://github.com/Azure/azure-sdk-for-java) | [Node.js](https://github.com/Azure/azure-sdk-for-node) | [PHP](https://github.com/Azure/azure-sdk-for-php) | [Python](https://github.com/Azure/azure-sdk-for-python) | [Ruby](https://github.com/Azure/azure-sdk-ruby)

> [!NOTE]
> Если пакет SDK не обеспечивает требуемую функциональность, можно также напрямую вызвать интерфейс [REST API Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx).
> 
> 

## Примеры
### .NET
* [Manage Azure resources and resource groups with .NET (Управление ресурсами и группами ресурсов Azure с помощью .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
* [Deploy an SSH Enabled VM with a Template with .NET (Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона c помощью .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)

### Java
* [Getting Started with Resources - Manage Resource - in Java (Приступая к работе с ресурсами: управление ресурсами в Java)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource/)
* [Приступая к работе с ресурсами: управление группой ресурсов на Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
* [Deploy an SSH Enabled VM with a Template with .NET (Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона c помощью .NET)](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)

### Node.js
* [Manage Azure resources and resource groups with .NET (Управление ресурсами и группами ресурсов Azure с помощью .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
* [Deploy an SSH Enabled VM with a Template with .NET (Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона c помощью .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)

### Python
* [Manage Azure resources and resource groups with .NET (Управление ресурсами и группами ресурсов Azure с помощью .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
* [Deploy an SSH Enabled VM with a Template with .NET (Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона c помощью .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)

### Ruby
* [Manage Azure resources and resource groups with .NET (Управление ресурсами и группами ресурсов Azure с помощью .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)
* [Deploy an SSH Enabled VM with a Template with .NET (Развертывание виртуальной машины с включенным протоколом SSH на основе шаблона c помощью .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)

Наряду с этими образцами можно также выполнять поиск по коллекции образцов.

[.NET](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=dotnet) | [Java](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=java) | [Node.js](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=nodejs) | [Python](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=python) | [Ruby](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=ruby)

## Дальнейшие действия
* Вводные сведения о работе с шаблонами см. в статье [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).
* Более подробные инструкции по созданию шаблона см. в статье [Пошаговое руководство по созданию шаблона Resource Manager](resource-manager-template-walkthrough.md).
* Сведения о том, какие функции можно использовать в шаблоне, см. в статье [Функции шаблонов](resource-group-template-functions.md).
* Сведения об использовании Resource Manager в Visual Studio см. в статье [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
* Сведения об использовании VS Code с Resource Manager см. в статье [Работа с шаблонами Azure Resource Manager в Visual Studio Code](resource-manager-vs-code.md).

Краткий обзор диспетчера ресурсов также представлен в этом видео:

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Azure-Resource-Manager-Overview/player]


<!---HONumber=AcomDC_0921_2016-->