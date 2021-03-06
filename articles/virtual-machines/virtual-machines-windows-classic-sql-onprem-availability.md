---
title: Расширение локальных групп доступности Always On в Azure | Microsoft Docs
description: В этом руководстве используются ресурсы, созданные с помощью классической модели развертывания. Также здесь показано, как с помощью мастера добавления реплики в SQL Server Management Studio (SSMS) добавить реплику группы доступности Always On в Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/12/2016
ms.author: MikeRayMSFT

---
# Расширение локальных групп доступности Always On в Azure
Группы доступности Always On обеспечивают высокий уровень доступности для групп базы данных путем добавления вторичных реплик. Эти реплики позволяют отрабатывать отказы баз данных. Кроме того, их можно использовать для разгрузки рабочих нагрузок чтения или задач резервного копирования.

Вы можете расширить локальные группы доступности в Microsoft Azure. Для этого подготовьте одну или несколько виртуальных машин Azure с помощью SQL Server, а затем добавьте их в локальные группы доступности в качестве реплик.

В этом учебнике предполагается, что у вас есть следующие компоненты.

* Активная подписка Azure. Вы можете [воспользоваться бесплатной пробной версией](https://azure.microsoft.com/pricing/free-trial/).
* Существующая локальная группа доступности Always On. Дополнительные сведения о группах доступности см. в статье [Группы доступности Always On](https://msdn.microsoft.com/library/hh510230.aspx).
* Подключение между локальной сетью и виртуальной сетью Azure. Дополнительные сведения о создании этой виртуальной сети см. в статье [Создание виртуальной сети с VPN-подключением типа "сеть-сеть" с помощью классического портала Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## Мастер добавления реплики Azure
В этом разделе показано, как с помощью **мастера добавления реплик Azure** расширить решение группы доступности Always On и включить реплики Azure.

1. В SQL Server Management Studio разверните раздел **Высокий уровень доступности Always On** > **Группы доступности** > **[Имя группы доступности]**.
2. Щелкните правой кнопкой мыши **Реплики доступности** и выберите пункт **Добавить реплику**.
3. По умолчанию откроется **мастер добавления реплики в группу доступности**. Нажмите кнопку **Далее**. Если вы установили флажок **Больше не показывать эту страницу** в нижней части страницы во время предыдущего запуска этого мастера, этот экран не откроется.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Вам потребуется подключиться ко всем существующим вторичным репликам. Вы можете щелкнуть **Подключить...** рядом с каждой репликой или выбрать пункт **Подключить все…** в нижней части экрана. После проверки подлинности нажмите кнопку **Далее**, чтобы перейти на следующий экран.
5. В верхней части страницы **Указание реплик** есть несколько вкладок: **Реплики**, **Конечные точки**, **Параметры резервного копирования** и **Прослушиватель**. На вкладке **Реплики** нажмите **Добавить реплику Azure…**, чтобы запустить мастер добавления реплики Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Выберите существующий сертификат управления Azure в локальном хранилище сертификатов Windows, если он уже установлен. Выберите или введите идентификатор подписки Azure, если вы ранее использовали его. Вы можете нажать кнопку "Загрузить", чтобы скачать и установить сертификат управления Azure, а также скачать список подписок, используя учетную запись Azure.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Каждое поле на странице будет заполнено значениями, которые будут использоваться для создания виртуальной машины Azure, на которой будет размещена реплика.
   
   | Настройка | Описание |
   | --- | --- |
   | **Образ —** |Выберите нужное сочетание операционной системы и SQL Server. |
   | **Размер виртуальной машины** |Выберите размер виртуальной машины в соответствии с вашими бизнес-потребностями. |
   | **Имя виртуальной машины** |Укажите уникальное имя для новой виртуальной машины. Имя должно содержать от 3 до 15 символов, может включать только буквы, цифры и дефисы, а также должно начинаться с буквы и заканчиваться буквой или цифрой. |
   | **Имя пользователя виртуальной машины** |Укажите имя пользователя учетной записи администратора на виртуальной машине. |
   | **Пароль администратора виртуальной машины** |Укажите пароль для новой учетной записи. |
   | **Подтверждение пароля** |Подтвердите пароль для новой учетной записи. |
   | **Виртуальная сеть** |Укажите виртуальную сеть Azure, которую будет использовать новая виртуальная машина. Дополнительные сведения о виртуальных сетях см. в статье [Обзор виртуальной сети](../virtual-network/virtual-networks-overview.md). |
   | **Подсеть виртуальной сети** |Укажите подсеть виртуальной сети, которую будет использовать новая виртуальная машина. |
   | **Домен** |Подтвердите правильность предварительно заполненного значения для домена. |
   | **Имя пользователя домена** |Укажите учетную запись, входящую в группу локальных администраторов на локальных узлах кластера. |
   | **Пароль** |Укажите пароль для имени пользователя домена. |
8. Нажмите кнопку **ОК**, чтобы проверить параметры развертывания.
9. После этого отобразятся условия соглашения. Прочтите соглашение и нажмите кнопку **ОК**, если вы согласны с этими условиями.
10. Снова откроется страница **Указание реплик**. Проверьте параметры новой реплики Azure на вкладках **Реплики**, **Конечные точки** и **Параметры резервного копирования**. Измените параметры в соответствии со своими бизнес-требованиями. Дополнительные сведения о параметрах на этих вкладках см. в статье [Страница "Выбор реплик" (мастер создания группы доступности/мастер добавления реплики)](https://msdn.microsoft.com/library/hh213088.aspx). Обратите внимание, что на вкладке "Прослушиватель" невозможно создать прослушиватели для групп доступности, содержащих реплики Azure. Кроме того, если прослушиватель создан еще до запуска мастера, вы получите сообщение о том, что служба Azure не поддерживает прослушиватель. Создание прослушивателей рассматривается в разделе **Создание прослушивателя группы доступности**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Нажмите кнопку **Далее**.
12. Выберите необходимый метод синхронизации данных на странице **Выбор начальной синхронизации данных** и нажмите кнопку **Далее**. В большинстве случаев следует выбирать вариант **Полная синхронизация данных**. Дополнительные сведения о методах синхронизации данных см. в статье [Страница "Выбор начальной синхронизации данных" (мастера группы доступности Always On)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Просмотрите результаты на странице **Проверка**. Исправьте нерешенные ошибки и при необходимости перезапустите проверку. Нажмите кнопку **Далее**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Просмотрите параметры на странице **Сводка**, а затем нажмите кнопку **Готово**.
15. Начнется процесс подготовки. После успешного завершения работы мастера нажмите кнопку **Закрыть**, чтобы выйти из мастера.

> [!NOTE]
> Мастер добавления реплики Azure создает файл журнала в папке Пользователи\\<имя\_пользователя>\\AppData\\Local\\SQL Server\\AddReplicaWizard. С помощью этого файла журнала можно устранить ошибки при развертывании реплик Azure. Если мастеру не удается выполнить любое действие, выполняется откат всех предыдущих операций, в том числе удаление подготовленной виртуальной машины.
> 
> 

## Создание прослушивателя группы доступности
После создания группы доступности необходимо создать прослушиватель для подключения клиентов к репликам. Прослушиватели направляют входящие подключения на первичную или доступную только для чтения вторичную реплику. Дополнительные сведения о прослушивателях см. в статье [Настройка прослушивателя внутренней подсистемы балансировки нагрузки для группы доступности Always On в Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

## Дальнейшие действия
Вы можете не только использовать **мастер добавления реплики Azure** для расширения группы доступности Always On в Azure, но также полностью переместить некоторые рабочие нагрузки SQL Server в Azure. Чтобы приступить к работе, см. статью [Подготовка виртуальной машины SQL Server в Azure](virtual-machines-windows-portal-sql-server-provision.md).

Другие темы, связанные с запуском SQL Server на виртуальных машинах Azure, рассматриваются в статье [SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0713_2016-->