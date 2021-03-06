---
title: Предсказуемые подготовка и развертывание микрослужб в Azure
description: Узнайте, как с помощью шаблонов групп ресурсов JSON и скриптов PowerShell предсказуемо подготовить и развернуть приложение, состоящее из микрослужб, в службу приложений Azure как единое целое.
services: app-service
documentationcenter: ''
author: cephalin
manager: wpickett
editor: jimbe

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin

---
# Предсказуемые подготовка и развертывание микрослужб в Azure
В этой статье описано, как предсказуемо с помощью шаблонов групп ресурсов JSON и скриптов PowerShell подготовить и развернуть приложение, состоящее из [микрослужб](https://en.wikipedia.org/wiki/Microservices), в [службу приложений Azure](/services/app-service/) как единое целое.

Залогом успеха при подготовке и развертывании крупномасштабных приложений, которые состоят из разъединенных микрослужб, являются повторяемость и предсказуемость. [Служба приложений Azure](/services/app-service/) позволяет создавать микрослужбы, которые содержат веб-приложения, мобильные приложения, приложения API и приложения логики. [Диспетчер ресурсов Azure](../resource-group-overview.md) позволяет управлять микрослужбами как единым целым, вместе с зависимостями ресурсов, например параметрами управления базами данных и систем управления версиями. Теперь можно также развернуть такое приложение с помощью шаблонов JSON и простых скриптов PowerShell.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Выполняемая задача
В этом учебнике будет развернуто приложение, которое включает в себя:

* два веб-приложения (т. е. две микрослужбы);
* базу данных SQL внутреннего сервера;
* параметры приложения, строки подключения и система управления версиями;
* анализы приложений, предупреждения, параметры автоматического масштабирования.

## Используемые инструменты
В этом учебнике будут использоваться следующие инструменты. Так как подробное изучение инструментов не входит в задачу данного учебника, будем придерживаться беглого сценария и остановимся лишь на кратком введении к каждому инструменту и скажем, где можно будет найти дополнительную информацию о них.

### Шаблоны диспетчера ресурсов Azure (JSON)
Каждый раз при создании веб-приложения, например, в службе приложений Azure диспетчер ресурсов Azure использует шаблон JSON для создания всей группы ресурсов с ресурсами компонентов. Сложный шаблон из [Azure Marketplace](/marketplace), такой как приложение [Scalable WordPress](/marketplace/partners/wordpress/scalablewordpress/), может содержать базу данных MySQL, учетные записи хранения, план службы приложения, само веб-приложение, правила оповещений, параметры приложения, параметры автоматического масштабирования и др., и все эти шаблоны доступны через PowerShell. Информацию о том, как скачивать и использовать эти шаблоны, см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md).

Дополнительную информацию о шаблонах диспетчера ресурсов Azure см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

### Пакет SDK для Azure 2.6 для Visual Studio
Новейший пакет SDK содержит улучшения для поддержки шаблонов диспетчера ресурсов в редакторе JSON. Здесь можно быстро создать шаблон группы ресурсов с нуля или открыть существующий шаблон JSON (например, скачанный шаблон коллекции) для изменения, заполнения файла параметров и даже развертывания группы ресурсов непосредственно из решения для групп ресурсов Azure.

Дополнительную информацию см. в статье [Пакет SDK для Azure версии 2.6 для Visual Studio](/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### Azure PowerShell 0.8.0 или более поздней версии
Начиная с версии 0.8.0, установка Azure PowerShell в дополнение к модулю Azure содержит модуль диспетчера ресурсов Azure. Этот новый модуль позволяет создавать скрипты развертывания групп ресурсов.

Дополнительную информацию см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md).

### Обозреватель ресурсов Azure
Этот [инструмент предварительного просмотра](https://resources.azure.com) позволяет изучать определения JSON всех групп ресурсов в подписке и отдельных ресурсах. Этот инструмент предназначен для изменения определений JSON ресурса, удаления всей иерархии ресурсов и создания новых ресурсов. Информация, которая уже доступна в этом инструменте, особенно полезна при создании шаблонов, так как она показывает, какие свойства необходимо задать для ресурса определенного типа, правильных значений и т. д. Вы даже можете создать группу ресурсов на [портале Azure](https://portal.azure.com/) и проверить ее определения JSON в инструменте обозревателя, чтобы упростить разработку шаблона группы ресурсов.

### Кнопка "Развертывание в Azure"
При использовании GitHub для управления версиями можно поместить [кнопку "Развертывание в Azure"](/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) в свой файл README.MD, которая обеспечивает пользовательский интерфейс развертывания в Azure одним действием. Хотя это можно сделать для любого простого веб-приложения, этот параметр можно расширить, чтобы организовать развертывание группы ресурсов как единого целого, помещая файл azuredeploy.json в корневой репозиторий. Этот файл JSON, который содержит шаблон группы ресурсов, используется кнопкой "Развертывание в Azure" для создания группы ресурсов. Например, см. образец [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp), который будет использоваться в этом учебнике.

## Получение образца шаблона группы ресурсов
Теперь давайте сразу к делу.

1. Перейдите к образцу службы приложений [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp).
2. В readme.md щелкните **Развертывание в Azure**.
3. Вы будете перенаправлены на веб-сайт [Развертывание в Azure](https://deploy.azure.com) и получите запрос на ввод параметров развертывания. Обратите внимание, что большая часть полей уже заполнена именем репозитория и какими-либо случайными строками. При необходимости можно изменить все поля, но для этого нужно ввести имя и пароль администратора SQL Server, а затем щелкнуть **Далее**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Затем щелкните **Развернуть** для запуска процесса развертывания. Когда процесс подойдет к завершению, перейдите по ссылке http://todoapp*XXXX*.azurewebsites.net для просмотра развернутого приложения.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   При первом переходе к приложению пользовательский интерфейс будет работать немного медленнее, так как приложение только запускается, но убедитесь, что это полнофункциональное приложение.
5. На странице «Развертывание» откройте ссылку **Управление**, чтобы увидеть новое приложение на портале Azure.
6. В раскрывающемся списке **Основные компоненты** щелкните ссылку "Группа ресурсов". Обратите внимание, что веб-приложение уже подключено к репозиторию GitHub в разделе **Внешний проект**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Обратите внимание, что в колонке "Группа ресурсов" уже есть два веб-приложения и одна база данных SQL в группе ресурсов.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Все, что вы могли видеть в течение этих нескольких минут, — это полностью развернутое приложение, состоящее из двух микрослужб, со всеми компонентами, зависимостями, параметрами, базами данных и непрерывной публикацией, которое настроено диспетчером ресурсов Azure с помощью автоматической оркестрации. Все это было проделано с помощью двух вещей:

* кнопки "Развертывание в Azure";
* файла azuredeploy.json в корневом каталоге репозитория.

Это же приложение можно развернуть десятки, сотни или тысячи раз и каждый раз с точно такой же конфигурацией. Повторяемость и предсказуемость такого подхода позволяет легко и надежно развертывать крупномасштабные приложения.

## Изучение (или изменение) файла AZUREDEPLOY.JSON
Теперь давайте посмотрим, как был настроен репозиторий GitHub. Вы будете использовать редактор JSON в пакете SDK для Azure .NET, поэтому, если вы еще не установили [пакет SDK для Azure .NET 2.6](/downloads/), сделайте это сейчас.

1. Клонируйте репозиторий [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) с помощью выбранного инструмента git. На снимке экрана ниже я делаю это в Team Explorer в Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. В корневом каталоге репозитория откройте файл azuredeploy.json в Visual Studio. Если панель структуры JSON не отображается, необходимо установить пакет SDK для Azure .NET.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Я не буду описывать все подробности формата JSON, но в разделе [Дополнительные ресурсы](#resources) приведены ссылки для изучения языка шаблонов групп ресурсов. Здесь я просто выберу для отображения интересные возможности, которые помогут вам приступить к работе при создании пользовательского шаблона для развертывания приложения.

### Параметры
Обратите внимание на раздел "Параметры", чтобы увидеть, что большинство из этих параметров можно ввести на запрос, формируемый командой кнопки **Развертывание в Azure**. Веб-сайт кнопки **Развертывание в Azure** заполняет входные элементы пользовательского интерфейса на основе параметров, определенных в файле azuredeploy.json. Эти параметры используются в определениях ресурсов, таких как имена ресурсов, значения свойств и т. д.

### Ресурсы
В узле "ресурсы" можно видеть, что определены 4 ресурса верхнего уровня, включая экземпляр SQL Server, план службы приложений и два веб-приложения.

#### План службы приложений
Начнем с простого ресурса корневого уровня в JSON. В структуре JSON щелкните план службы приложений с именем **[hostingPlanName]**, чтобы выделить соответствующий код JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Обратите внимание, что элемент `type` указывает на строку для плана службы приложений (он был вызван с фермы серверов уже довольно давно), а другие элементы и свойства заполняются на основе параметров, определенных в файле JSON, и этот ресурс не содержит никаких вложенных ресурсов.

> [!NOTE]
> Обратите внимание, что значение `apiVersion` сообщает Azure, какая версия интерфейса REST API будет использоваться с определением ресурса JSON, и оно может повлиять на форматирование ресурса в `{}`.
> 
> 

#### SQL Server
Затем щелкните ресурс SQL Server с именем **SQLServer** в структуре JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Обратите внимание на следующее в выделенном коде JSON.

* Использование параметров гарантирует, что создаваемые ресурсы именуются и настраиваются так, чтобы они были согласованы друг с другом.
* Ресурс SQLServer содержит два вложенных ресурса, у которых имеются разные значения для `type`.
* Вложенные ресурсы в `“resources”: […]`, где определены база данных и правила брандмауэра, содержат элемент `dependsOn`, указывающий на идентификатор ресурса для ресурса SQLServer корневого уровня. Этим диспетчеру ресурсов Azure сообщается, что "перед созданием ресурса уже должен существовать другой ресурс и, если этот другой ресурс определен в шаблоне, сначала создайте его".
  
  > [!NOTE]
  > Дополнительную информацию об использовании `resourceId()` см. в статье [Функции шаблонов в диспетчере ресурсов Azure](../resource-group-template-functions.md).
  > 
  > 
* Результатом работы элемента `dependsOn` будет то, что диспетчер ресурсов Azure сможет узнать, какие ресурсы могут быть созданы в параллельном режиме и какие ресурсы должны быть созданы последовательно.

#### Веб-приложение
Теперь перейдем к реальным веб-приложениям, которые значительно сложнее. Щелкните веб-приложение [variables('apiSiteName')] в структуре JSON и выделите его код JSON. Теперь вы увидите гораздо более интересные вещи. Поэтому я расскажу об этих особенностях по очереди.

##### Корневой ресурс
Веб-приложение зависит от двух разных ресурсов. Это означает, что диспетчер ресурсов Azure создаст веб-приложение только после создания плана службы приложений и экземпляра SQL Server.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### Параметры приложения
Параметры приложения также определены в качестве вложенного ресурса.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

В элементе `properties` для `config/appsettings` имеется два параметра приложений в формате `“<name>” : “<value>”`.

* Элемент `PROJECT` — это [параметр KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments), который сообщает развертыванию Azure, какой проект требуется использовать в решении Visual Studio с несколькими проектами. Позже я расскажу, как настроить систему управления версиями, но, поскольку код ToDoApp входит в многопроектное решение Visual Studio, этот параметр необходим.
* Элемент `clientUrl` — это просто параметр приложения, используемый кодом приложения.

##### Строки подключения
Строки подключения также определены в качестве вложенного ресурса.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

В элементе `properties` для `config/connectionstrings` каждая строка подключения также определяется как пара "имя:значение" с определенным форматом `“<name>” : {“value”: “…”, “type”: “…”}`. Возможные значения для элемента `type`: `MySql`, `SQLServer`, `SQLAzure` и `Custom`.

> [!TIP]
> Чтобы открыть полный список типов строк подключения, выполните следующую команду в Azure PowerShell: [Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType").
> 
> 

##### Система управления версиями
Параметры системы управления версиями также определены в качестве вложенного ресурса. Диспетчер ресурсов Azure использует этот ресурс для настройки непрерывной публикации (см. пояснение `IsManualIntegration` ниже) и для автоматического запуска развертывания кода приложения во время обработки JSON-файла.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` и `branch` должны быть интуитивно понятны, а также должны указывать на репозиторий Git и имя ветви для публикации. Опять же, они определяются входными параметрами.

Обратите внимание, что в элементе `dependsOn` в дополнение к самому ресурсу веб-приложение `sourcecontrols/web` также зависит от `config/appsettings` и `config/connectionstrings`. Это происходит потому, что после настройки `sourcecontrols/web` процесс развертывания Azure автоматически попытается развернуть, собрать и запустить код приложения. Таким образом, вставка этой зависимости помогает убедиться, что перед запуском кода приложение обладает доступом к необходимым параметрам приложения и строкам подключения.

> [!NOTE]
> Обратите внимание, что для `IsManualIntegration` установлено значение `true`. Это свойство в этом учебнике необходимо, так как у вас фактически нет репозитория GitHub, поэтому в действительности вы не можете предоставить разрешение в Azure для настройки непрерывной публикации из [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (т. е. передавать автоматические обновления репозитория в Azure). Значение по умолчанию `false` для указанного репозитория можно использовать только в том случае, если вы заранее настроили учетные данные владельца GitHub на [портале Azure](https://portal.azure.com/). Другими словами, если вы ранее настроили систему управления версиями для GitHub или BitBucket для любого приложения на [портале Azure](https://portal.azure.com/) с помощью своих учетных данных пользователя, Azure запомнит данные и в будущем будет использовать их при развертывании любого приложения из GitHub или BitBucket. Однако, если вы еще не сделали этого, развертывание шаблона JSON завершится ошибкой при попытке диспетчера ресурсов Azure настроить параметры системы управления версиями для веб-приложения, так как невозможно будет войти в GitHub или BitBucket с учетными данными владельца репозитория.
> 
> 

## Сравнение шаблона JSON с развернутой группой ресурсов
Здесь можно просмотреть все колонки веб-приложений на [портале Azure](https://portal.azure.com/), однако есть и другой, не менее полезный инструмент. Перейдите к инструменту предварительного просмотра [Обозреватель ресурсов Azure](https://resources.azure.com), формирующему JSON-представление всех групп ресурсов в ваших подписках, которые фактически существуют на внутреннем сервере Azure. Вы также можете просмотреть, как иерархия JSON группы ресурсов в Azure соответствует иерархии в файле шаблона, использованного для его создания.

Например, при переходе в инструмент [Обозреватель ресурсов Azure](https://resources.azure.com) и развертывании узлов в обозревателе отображаются группы ресурсов и ресурсы корневого уровня, которые собираются по соответствующим типам ресурсов.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Если внимательнее рассмотреть веб-приложение, можно увидеть подробности конфигурации веб-приложения, как это показано на снимке экрана ниже.

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Кроме того, вложенные ресурсы должны иметь иерархию, очень похожую на иерархию в файле шаблона JSON, и вы должны убедиться, что параметры приложения, строки подключения и т. д. правильно отображаются в области JSON. Отсутствие здесь параметров может указывать на проблему с файлом JSON и может помочь при отладке файла шаблона JSON.

## Развертывание шаблона группы ресурсов вручную
Кнопка **Развертывание в Azure** — это отлично, но она позволяет развернуть шаблон группы ресурсов в azuredeploy.json только в том случае, если azuredeploy.json уже помещен в GitHub. Пакет SDK для Azure .NET также предоставляет средства для развертывания любого файла шаблона JSON непосредственно с локального компьютера. Для этого сделайте следующее.

1. В Visual Studio щелкните **Файл** > **Создать** > **Проект**.
2. Щелкните **Visual C#** > **Облако** > **Группа ресурсов Azure**, а затем нажмите кнопку **ОК**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. В разделе **Выбор шаблона Azure** выберите **Пустой шаблон** и нажмите кнопку **ОК**.
4. Перетащите файл azuredeploy.json в папку **Шаблон** нового проекта.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. В обозревателе решений откройте скопированный файл azuredeploy.json.
6. Только для примера давайте добавим некоторые стандартные ресурсы Application Insight нашего файла JSON, нажав кнопку **Добавить ресурс**. Если вас интересует только развертывание файла JSON, пропустите шаги развертывания.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Выберите **Application Insights для веб-приложений**, убедитесь, что выбран существующий план службы приложений и выбрано веб-приложение, а затем нажмите кнопку **Добавить**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Теперь вы сможете увидеть несколько новых ресурсов в зависимости от ресурса и его роли, его зависимости от плана службы приложений или веб-приложения. Эти ресурсы не включены существующим определением, и вы собираетесь изменить их.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. В структуре JSON щелкните **appInsights AutoScale**, чтобы выделить его код JSON. Это параметр масштабирования для плана службы приложений.
9. В выделенном коде JSON найдите свойства `location` и `enabled`, а затем задайте их, как показано ниже.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. В структуре JSON щелкните **CPUHigh appInsights**, чтобы выделить код JSON. Это предупреждение.
11. Найдите свойства `location` и `isEnabled`, а затем задайте их, как показано ниже. Выполните то же самое для других трех предупреждений (фиолетового цвета).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Теперь все готово для развертывания. Щелкните правой кнопкой мыши проект и выберите **Развернуть** > **Новое развертывание**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Войдите в свою учетную запись Azure, если вы еще этого не сделали.
14. Выберите существующую группу ресурсов в подписке или создайте новую, выберите файл **azuredeploy.json**, а затем нажмите кнопку **Изменить параметры**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Теперь в удобной таблице можно изменить все параметры, определенные в файле шаблона. Параметры, определяющие значения по умолчанию, уже будут иметь значения по умолчанию, а параметры, определяющие список допустимых значений, будут отображаться в виде раскрывающихся списков.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Заполните все пустые параметры и используйте [адрес репозитория GitHub для ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) в **repoUrl**. Нажмите кнопку **Сохранить**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Автоматическое масштабирование — это функция, предлагаемая в категории **Standard** или более высокой категории. Предупреждения уровня плана — это возможности, предлагаемые в категории **Basic** или более высокой. Для просмотра всех новых ресурсов Application Insights необходимо задать для параметра **sku** значение **Standard** или **Premium**.
    > 
    > 
16. Щелкните **Развернуть**. Если вы выбрали параметр **Сохранить пароль**, пароль будет сохранен в файле параметров **в виде обычного текста**. В противном случае вам будет предложено ввести пароль для базы данных во время развертывания.

Это все! Теперь нужно просто перейти на [портал Azure](https://portal.azure.com/) и с помощью инструмента [Обозреватель ресурсов Azure](https://resources.azure.com) просмотреть новые параметры автоматического масштабирования и предупреждения, добавляемые в развертываемое приложение JSON.

Действия, описанные в этом разделе, главным образом охватывают следующее.

1. Подготовка файла шаблона
2. Создание файла параметров для использования в файле шаблона
3. Развертывание файла шаблона с файлом параметров

Последний шаг можно легко выполнить с помощью командлета PowerShell. Чтобы увидеть, что делает Visual Studio при развертывании приложения, откройте сценарий Scripts\\Deploy-AzureResourceGroup.ps1. Не пугайтесь обилия кода, а просто обратите внимание на код, необходимый для выполнения развертывания файла шаблона с файлом параметров.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Именно последний командлет `New-AzureResourceGroup` выполняет это действие. Все это должно показать вам, как с помощью соответствующего инструментария сравнительно легко выполнить предсказуемое развертывание приложения в облаке. Каждый раз при запуске командлета с тем же шаблоном и тем же файлом параметров вы получите тот же результат.

## Сводка
В DevOps повторяемость и предсказуемость являются ключевыми факторами для любого успешного развертывания крупномасштабных приложений, состоящих из микрослужб. В этой статье рассматривалось развертывание приложения, состоящего из двух микрослужб, в Azure как отдельной группы ресурсов с помощью шаблона диспетчера ресурсов Azure. Надеемся, что вы смогли узнать все, что необходимо для преобразования приложения в Azure в шаблон, и сможете предсказуемо подготовить его и развернуть в Azure.

## Дальнейшие действия
Узнайте о том, как [применять быстрые методологии и непрерывно и удобно публиковать приложения, состоящие из микрослужб,](app-service-agile-software-development.md) и легко применять удобные технологии развертывания, такие как [развертывание в режиме фокус-тестирования](app-service-web-test-in-production-controlled-test-flight.md).

<a name="resources"></a>

## Дополнительные ресурсы
* [Язык шаблонов в диспетчере ресурсов Azure](../resource-group-authoring-templates.md)
* [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md)
* [Функции шаблонов в диспетчере ресурсов Azure](../resource-group-template-functions.md)
* [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](../resource-group-template-deploy.md)
* [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md)
* [Устранение неполадок при развертывании групп ресурсов в Azure](../resource-manager-troubleshoot-deployments-portal.md)

<!---HONumber=AcomDC_0330_2016-->