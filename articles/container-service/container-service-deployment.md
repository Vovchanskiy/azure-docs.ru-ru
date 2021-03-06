---
title: "Развертывание кластера службы контейнеров Azure | Документация Майкрософт"
description: "Развертывание кластера службы контейнеров Azure с помощью портала Azure, интерфейса командной строки Azure или PowerShell."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Mesos, Azure"
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c8c06906a5f99890295ff2b2433ff6f7e02dece5


---
# <a name="deploy-an-azure-container-service-cluster"></a>Развертывание кластера службы контейнеров Azure
Служба контейнеров Azure предусматривает быстрое развертывание популярных решений с открытым кодом для кластеризации и оркестрации контейнеров. Служба контейнеров Azure позволяет развертывать кластеры DC/OS и Docker Swarm, используя шаблоны Azure Resource Manager или портал Azure. Эти кластеры развертываются с помощью наборов масштабирования виртуальных машин Azure. Они также могут использовать возможности сети и хранилища Azure. Для доступа к службе контейнеров Azure нужна подписка Azure. Если у вас ее нет, зарегистрируйтесь, чтобы воспользоваться [бесплатной пробной версией](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

В этом документе описано развертывание кластера службы контейнеров Azure с использованием [портала Azure](#creating-a-service-using-the-azure-portal), [интерфейса командной строки Azure](#creating-a-service-using-the-azure-cli) и [модуля Azure PowerShell](#creating-a-service-using-powershell).  

## <a name="create-a-service-by-using-the-azure-portal"></a>Создание службы с помощью портала Azure
Войдите на портал Azure, выберите **Создать** и найдите **службу контейнеров Azure** в Azure Marketplace.

![Создание развертывания 1](media/acs-portal1.png)  <br />

Выберите **службу контейнеров Azure** и щелкните **Создать**.

![Создание развертывания 2](media/acs-portal2.png)  <br />

Введите следующие сведения:

* **Имя пользователя**— это имя будет использоваться для учетной записи на каждой виртуальной машине, а также в наборах масштабирования виртуальных машин в кластере службы контейнеров Azure.
* **Подписка**— выберите подписку Azure.
* **Группа ресурсов**— создайте группу ресурсов или выберите имеющуюся.
* **Расположение**— выберите регион Azure для развертывания службы контейнеров Azure.
* **Открытый ключ SSH** — добавьте открытый ключ, который будет использоваться для проверки подлинности при входе в виртуальные машины службы контейнеров Azure. Важно! Убедитесь, что ключ не разбит разрывами строки, но содержит префикс ssh-rsa и постфикс 'username@domain'. Он должен выглядеть так: **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. Инструкции по созданию ключей SSH см. в соответствующих статьях для [Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) и [Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/).

Когда будете готовы продолжить, нажмите кнопку **ОК** .

![Создание развертывания 3](media/acs-portal3.png)  <br />

Выберите тип оркестрации. Доступны следующие параметры.

* **DC/OS**— выполняет развертывание кластера DC/OS.
* **Swarm**— выполняет развертывание кластера Docker Swarm.

Когда будете готовы продолжить, нажмите кнопку **ОК** .

![Создание развертывания 4](media/acs-portal4.png)  <br />

Введите следующие сведения:

* **Количество главных серверов**— количество главных серверов в кластере.
* **Количество агентов**— для Docker Swarm здесь указывается начальное количество агентов в наборе масштабирования агента. Для DC/OS этот параметр означает начальное количество агентов в частном наборе масштабирования. Кроме того, создается общедоступный набор масштабирования с заранее определенным количеством агентов. Количество агентов в этом общедоступном наборе определяется с учетом количества главных серверов в кластере: 1 общедоступный агент для 1 главного сервера, 2 общедоступных агента для 3 или 5 главных серверов.
* **Размер виртуальной машины агента**— размер виртуальных машин агентов.
* **Префикс DNS**— уникальное имя, которое будет использоваться в качестве префикса для полных доменных имен службы.

Когда будете готовы продолжить, нажмите кнопку **ОК** .

![Создание развертывания 5](media/acs-portal5.png)  <br />

Когда завершится проверка службы, нажмите кнопку **ОК** .

![Создание развертывания 6](media/acs-portal6.png)  <br />

Нажмите кнопку **Создать** , чтобы начать развертывание.

![Создание развертывания 7](media/acs-portal7.png)  <br />

Если вы решили выполнить развертывание на портале Azure, вы сможете просматривать состояние развертывания.

![Создание развертывания 8](media/acs-portal8.png)  <br />

Когда развертывание кластера завершится, служба контейнеров Azure будет готова к использованию.

## <a name="create-a-service-by-using-the-azure-cli"></a>Создание службы с помощью интерфейса командной строки Azure
Чтобы создать экземпляр службы контейнеров Azure с помощью командной строки, вам нужна подписка Azure. Если у вас ее нет, зарегистрируйтесь, чтобы воспользоваться [бесплатной пробной версией](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Вам также нужно [установить](../xplat-cli-install.md) и [настроить](../xplat-cli-connect.md) интерфейс командной строки Azure.

Чтобы развернуть кластер DC/OS или Docker Swarm, выберите на GitHub один из следующих шаблонов. (обратите внимание, что эти шаблоны отличаются только выбором оркестратора по умолчанию):

* [шаблон DC/OS;](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [шаблон Swarm.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Затем убедитесь, что интерфейс командной строки Azure подключен к подписке Azure. Это можно сделать с помощью следующей команды:

```bash
azure account show
```
Если она не вернет учетную запись Azure, войдите в интерфейс командной строки Azure с помощью следующей команды:

```bash
azure login -u user@domain.com
```

Затем настройте средства интерфейса командной строки Azure для использования Azure Resource Manager.

```bash
azure config mode arm
```

Создайте группу ресурсов Azure и кластер службы контейнеров со следующими параметрами:

* **RESOURCE_GROUP** — имя группы ресурсов, которая будет использоваться с этой службой.
* **LOCATION** — регион Azure, в котором будут созданы группа ресурсов и развернутая служба контейнеров Azure.
* **TEMPLATE_URI** — расположение файла развертывания. Это должен быть необработанный (RAW) файл, который не указывает на пользовательский интерфейс GitHub. Чтобы найти этот URL-адрес, выберите в GitHub файл azuredeploy.json и нажмите кнопку **RAW** .

> [!NOTE]
> При выполнении этой команды оболочка запросит ввод значений параметров развертывания.
> 
> 

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Выбор параметров шаблона
Эта версия команды предполагает определение параметров в интерактивном режиме. Если вы хотите указать параметры (например, в виде строки в формате JSON), это можно сделать с помощью параметра `-p` . Например:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

Кроме того, вы можете указать файл параметров в формате JSON, используя параметр `-e`.

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

Пример файла параметров с именем `azuredeploy.parameters.json` см. в шаблонах службы контейнеров Azure, размещенных на GitHub.

## <a name="create-a-service-by-using-powershell"></a>Создание службы с помощью PowerShell
Кластер службы контейнеров Azure также можно развернуть с помощью PowerShell. В этом документе используется [модуль Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/)версии 1.0.

Выберите один из следующих шаблонов, чтобы развернуть кластер DC/OS или Docker Swarm. (обратите внимание, что эти шаблоны отличаются только выбором оркестратора по умолчанию):

* [шаблон DC/OS;](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [шаблон Swarm.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Прежде чем создать кластер в подписке Azure, убедитесь, что для сеанса PowerShell выполнен вход в Azure. Это можно сделать с помощью команды `Get-AzureRMSubscription` .

```powershell
Get-AzureRmSubscription
```

Войти в Azure можно с помощью команды `Login-AzureRMAccount` .

```powershell
Login-AzureRmAccount
```

Если вы хотите развернуть кластер в новой группе ресурсов, такую группу необходимо сначала создать. Чтобы создать группу ресурсов, используйте команду `New-AzureRmResourceGroup`, указав имя группы ресурсов и регион назначения.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

После создания группы ресурсов можно создать кластер, используя следующую команду: Универсальный код ресурса (URI) нужного шаблона указывается в параметре `-TemplateUri`. При выполнении этой команды в PowerShell отобразится запрос на ввод значений параметров развертывания.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Выбор параметров шаблона
Если вы знакомы с PowerShell, вы знаете, что можете проходить по кругу все доступные параметры для командлета. Для этого введите знак минус (-) и нажмите клавишу TAB. Точно так же это работает с параметрами, которые вы определили в шаблоне. Сразу после ввода имени шаблона командлет выбирает шаблон, анализирует его параметры и динамически добавляет параметры шаблона в команду. Таким образом можно легко указать значения параметров шаблона. Если вы забыли значение обязательного параметра, PowerShell подскажет его вам.

Ниже показана полная версия команды, в которой указаны параметры. Вы можете указать собственные значения для имен ресурсов.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда у вас есть работающий кластер, выберите ссылки ниже, чтобы узнать о возможностях подключения и управления.

* [Подключение к кластеру службы контейнеров Azure](container-service-connect.md)
* [Работа со службой контейнеров Azure и DC/OS](container-service-mesos-marathon-rest.md)
* [Работа со службой контейнеров Azure и Docker Swarm](container-service-docker-swarm.md)




<!--HONumber=Nov16_HO2-->


