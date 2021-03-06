---
title: Решение для мониторинга VMware в Log Analytics | Microsoft Docs
description: Узнайте о том, как решение для мониторинга VMware помогает управлять журналами событий и отслеживать узлы ESXi.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: banders

---
# <a name="vmware-monitoring-(preview)-solution-in-log-analytics"></a>Решение для мониторинга VMware (предварительная версия) в Log Analytics | Microsoft Azure
Решение для мониторинга VMware в Log Analytics помогает управлять централизованным ведением больших журналов VMware, а также отслеживать их. В статье описано, как с помощью этого решения централизованно устранять неполадки, связанные с узлами ESXi, а также регистрировать их и управлять ими. В решении можно увидеть подробные данные для всех узлов ESXi в одном расположении. В журналах узла ESXi вы можете просматривать счетчики основных событий, а также состояния и тенденции ВМ и узлов ESXi. Вы также можете устранять неполадки, просматривая централизованные журналы узлов ESXi и выполняя поиск в них. И наоборот, можно создавать оповещения на основе поисковых запросов по журналам.

## <a name="installing-and-configuring-the-solution"></a>Установка и настройка решения
Для установки и настройки решений используйте указанные ниже данные.

* Включите решение для мониторинга VMware в рабочую область OMS, как описано в статье [Добавление решений Log Analytics из коллекции решений](log-analytics-add-solutions.md).

#### <a name="supported-vmware-esxi-hosts"></a>Поддерживаемые узлы VMware ESXi
vSphere ESXi Host версий 5.5 и 6.0.

#### <a name="prepare-a-linux-server"></a>Подготовка сервера под управлением Linux
Создайте ВМ с ОС Linux, чтобы получать все данные системных журналов от узлов ESXi. [Агент OMS для Linux ](log-analytics-linux-agents.md) — это точка сбора всех данных системных журналов узлов ESXi. Можно использовать несколько узлов ESXi для пересылки журналов на отдельный сервер под управлением Linux, как показано в следующем примере.  

   ![Поток данных системных журналов](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Настройка сбора системных журналов
1. Настройте пересылку системных журналов для VSphere. Подробные сведения о настройке пересылки системных журналов см. [здесь](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Последовательно выберите **ESXi Host Configuration** > **Software** > **Advanced Settings** > **Syslog** (Конфигурация узла ESXi > Программное обеспечение > Дополнительные параметры > Системный журнал).
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. В поле *Syslog.global.logHost* укажите сервер Linux и номер порта *1514*. Например, `tcp://hostname:1514` или `tcp://123.456.789.101:1514`.
3. Откройте брандмауэр узла ESXi для системного журнала. Последовательно выберите **ESXi Host Configuration** > **Software** > **Security Profile** > **Firewall** и **Properties** (Конфигурация узла ESXi > Программное обеспечение > Профиль безопасности > Брандмауэр > Свойства).  
   
    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  
   
    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. Проверьте консоль vSphere, чтобы удостовериться в правильной настройке системного журнала. Подтвердите на узле ESXI, что этот порт **1514** настроен.
5. Проверьте подключение между сервером Linux и узлом ESXi, выполнив команду `nc` на узле ESXi. Например:
   
    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```
6. Скачайте и установите агент OMS для Linux на сервере Linux. Подробные сведения см. в [документации по агенту OMS для Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
7. Установив агента OMS для Linux, перейдите в каталог /etc/opt/microsoft/omsagent/sysconf/omsagent.d и скопируйте файл vmware_esxi.conf в каталог /etc/opt/microsoft/omsagent/conf/omsagent.d. Измените владельца или группу и разрешения для файла. Например:
   
    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
8. Перезапустите агент OMS для Linux, выполнив `sudo /opt/microsoft/omsagent/bin/service_control restart`.
9. На портале OMS выполните следующий поиск по журналу: `Type=VMware_CL`. Собирая данные системного журнала, OMS сохраняет формат syslog. На портале регистрируются некоторые поля, включая *Hostname* и *ProcessName*.  
   
    ![type](./media/log-analytics-vmware/type.png)  
   
    Если представление с результатами поиска по журналу похоже на изображенное выше, это значит, что панель мониторинга для соответствующего решения OMS VMware настроена.  

## <a name="vmware-data-collection-details"></a>Сведения о сборе данных VMware
Решения для мониторинга VMware собирает разные метрики производительности и данные журналов на узлах ESXi с помощью включенных агентов OMS для Linux.

В следующей таблице содержатся методы сбора данных и другие связанные сведения.

| платформа | Агент OMS для Linux | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![Да](./media/log-analytics-vmware/oms-bullet-green.png) |![Нет](./media/log-analytics-vmware/oms-bullet-red.png) |![Нет](./media/log-analytics-vmware/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-vmware/oms-bullet-red.png) |Каждые 3 минуты |

В следующей таблице приведены примеры типов данных, собираемых решением для мониторинга VMware.

| Имя поля | Описание |
| --- | --- |
| Device_s |Запоминающие устройства VMware |
| ESXIFailure_s |Типы сбоев |
| EventTime_t |Время возникновения события |
| HostName_s |Имя узла ESXi |
| Operation_s |Создание или удаление ВМ |
| ProcessName_s |Имя события |
| ResourceId_s |Имя узла VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V. |
| SCSIStatus_s |Состояние VMware SCSI |
| SyslogMessage_s |Данные системного журнала |
| UserName_s |Пользователь, создавший или удаливший ВМ |
| VMName_s |имя виртуальной машины; |
| Компьютер |Главный компьютер |
| TimeGenerated |Время создания данных |
| DataCenter_s |Центр обработки данных VMware |
| StorageLatency_s |Задержка хранилища (мс) |

## <a name="vmware-monitoring-solution-overview"></a>Обзор решения для мониторинга VMware
Плитка VMware отображается на портале OMS. Решение отображает обобщенное представление ошибок. Щелкнув плитку, вы перейдете в представление панели мониторинга.

![Плитка](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Навигация в представлении панели мониторинга
В представлении панели мониторинга **VMware** колонки упорядочены по следующим категориям:

* число состояний сбоя;
* основной узел по счетчикам событий;
* счетчики основных событий;
* действия виртуальной машины;
* события диска узла ESXi.

![Решение 1](./media/log-analytics-vmware/solutionview1-1.png)

![Решение 2](./media/log-analytics-vmware/solutionview1-2.png)

Щелкните любую колонку, чтобы открыть область поиска Log Analytics с соответствующими подробными сведениями.

Здесь можно изменить поисковый запрос в соответствии с определенной задачей. Руководство по поиску по журналам OMS доступно [здесь](log-analytics-log-searches.md).

#### <a name="find-esxi-host-events"></a>Поиск событий узлов ESXi
Для одного узла ESXi создается несколько журналов на основе выполняющихся процессов. Решение для мониторинга VMware объединяет их, суммируя счетчики событий. Это централизованное представление помогает понять, на каком узле ESXi происходит больше всего событий и какие именно события происходят в вашей среде чаще всего.

![event](./media/log-analytics-vmware/events.png)

Чтобы просмотреть дополнительные сведения, щелкните узел ESXi или тип события.

Щелкнув имя узла ESXi, вы сможете просмотреть сведения, связанные с этим узлом ESXi. Чтобы отфильтровать результаты по типу события, добавьте `“ProcessName_s=EVENT TYPE”` в поисковом запросе. Можно выбрать **ProcessName** в фильтре поиска. Это поможет ограничить поиск.

![Детализация](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Поиск активных действий ВМ
Виртуальные машины могут создаваться и удаляться на любом узле ESXi. Администраторам часто требуется знать, сколько ВМ создается узлом ESXi. Это помогает оценить производительность и рассчитать использование ресурсов. При управлении средой очень важно отслеживать события действий ВМ.

![Детализация](./media/log-analytics-vmware/vmactivities1.png)

Чтобы просмотреть дополнительные данные о создании ВМ узлом ESXi, щелкните имя узла ESXi.

![Детализация](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Распространенные поисковые запросы
Решение включает и другие полезные запросы, которые помогают управлять узлами ESXi узлов. Сюда входят запросы к данным о переполнении дискового пространства, задержках хранилища и сбоях пути.

![Запросы](./media/log-analytics-vmware/queries.png)

#### <a name="save-queries"></a>Сохранение запросов
Сохранение запросов поиска — это стандартная функция OMS, которая помогает сохранять запросы, которые оказались полезными для вас. Создав запрос, который вы считаете полезным, сохраните его, щелкнув **Избранное**. Сохранение запроса позволяет без труда использовать его позже на странице [Моя панель мониторинга](log-analytics-dashboards.md), где вы можете создавать собственные настраиваемые панели мониторинга.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Создание оповещений из запросов
Созданные запросы можно использовать для оповещения при возникновении определенных событий. Подробные сведения о создании оповещений см. в статье [Оповещения в Log Analytics](log-analytics-alerts.md). Примеры оповещающих и других запросов см. в записи блога [Monitor VMware using OMS Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) (Мониторинг VMware с помощью OMS Log Analytics).

## <a name="next-steps"></a>Дальнейшие действия
* Используйте [поиск по журналам в Log Analytics](log-analytics-log-searches.md) для просмотра подробных данных об узле VMware.
* [Создавайте собственные панели мониторинга](log-analytics-dashboards.md), отображающие данные об узле VMware.
* [Создавайте оповещения](log-analytics-alerts.md), информирующие о возникновении определенных событий узла VMware.

<!--HONumber=Oct16_HO2-->


