---
title: Устранение неполадок с помощью отчетов о работоспособности системы | Microsoft Docs
description: Содержит описание отчетов о работоспособности, отправляемых компонентами Azure Service Fabric, и их использовании для устранения неполадок с кластерами и приложениями.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2016
ms.author: oanapl

---
# <a name="use-system-health-reports-to-troubleshoot"></a>Устранение неполадок с помощью отчетов о работоспособности системы
Компоненты Azure Service Fabric поддерживают создание отчетов по всем сущностям в кластере без дополнительной настройки. В [хранилище данных о работоспособности](service-fabric-health-introduction.md#health-store) сущности создаются и удаляются на основании отчетов системы. Кроме того, эти сущности упорядочиваются в иерархию с учетом взаимодействия между ними.

> [!NOTE]
> Чтобы лучше разобраться в основных понятиях, связанных с работоспособностью, см. статью [Общие сведения о наблюдении за работоспособностью системы в Service Fabric](service-fabric-health-introduction.md).
> 
> 

В отчетах о работоспособности системы отражаются показатели функциональности кластеров и приложений, а также отмечаются проблемы с работоспособностью. Что касается приложений и служб, отчеты о работоспособности системы проверяют правильность реализации и поведения сущностей на уровне платформы Service Fabric. Отчеты не обеспечивают отслеживание работоспособности бизнес-логики службы или обнаружение зависших процессов. Пользовательские службы могут расширить данные о работоспособности сведениями, характерными для их логики.

> [!NOTE]
> Отчеты о работоспособности устройств наблюдения отображаются только *после* того, как компоненты системы создают сущность. При удалении сущности хранилище данных о работоспособности автоматически удаляет все связанные с ней отчеты о работоспособности. Это же происходит при создании экземпляра сущности (например, экземпляра реплики службы). Все отчеты, связанные со старым экземпляром, полностью удаляются из хранилища.
> 
> 

На отчеты о компонентах системы указывает их источник, который начинается с префикса **System** . Устройства наблюдения не могут использовать такой же префикс для своих источников, так как из-за недопустимых параметров отчеты отклоняются.
Рассмотрим некоторые системные отчеты, чтобы понять, как они активируются и как устранить возможные проблемы, связанные с ними.

> [!NOTE]
> Платформа Service Fabric по-прежнему поддерживает отчеты, которые улучшают видимость процессов, выполняемых в кластере и приложении.
> 
> 

## <a name="cluster-system-health-reports"></a>Системные отчеты о работоспособности кластеров
В хранилище данных о работоспособности автоматически создается сущность работоспособности кластера. Если все работает надлежащим образом, в этом хранилище нет системного отчета.

### <a name="neighborhood-loss"></a>Потеря окружения
**System.Federation** сообщает об ошибке при обнаружении потери окружения. Отчеты приходят с отдельных узлов. В имя свойства включается идентификатор узла. Если во всей кольцевой сети Service Fabric теряется одно окружение, могут произойти два события (отчеты создаются по обе стороны разрыва). Если теряется несколько окружений, происходит больше событий.

В отчете в качестве срока жизни указывается время ожидания глобальной аренды. Отчет повторно отправляется дважды в течение срока жизни, пока условие актуально. Событие автоматически удаляется по истечении срока его действия. Такое поведение обеспечивает правильное удаление отчетов из хранилища данных о работоспособности даже в том случае, если узел, с которого отправляется отчет, не работает.

* **SourceId**: System.Federation.
* **Свойство**: начинается с **Neighborhood** и включает сведения об узле.
* **Дальнейшие действия**: выясните, почему теряется окружение (например, проверьте связь между узлами кластера).

## <a name="node-system-health-reports"></a>Системные отчеты о работоспособности узлов
**System.FM** представляет собой службу диспетчера отработки отказов. Это система, которая управляет сведениями об узлах кластера. Каждый узел должен содержать один отчет системы System.FM о его состоянии. Сущности узла удаляются при удалении состояния узла (см. [RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx)).

### <a name="node-up/down"></a>Включение и отключение узла
Система System.FM сообщает о нормальном состоянии, если узел присоединяется к кольцевой сети (запускается и работает). Система выдает сообщение об ошибке, когда узел отключается от кольцевой сети (не работает из-за обновления или сбоя). Иерархия работоспособности, созданная в хранилище данных о работоспособности, выполняет действия в отношении развернутых сущностей в соответствии с отчетами об узлах System.FM. Узел считается виртуальным родительским элементом всех развернутых сущностей. Развернутые сущности на этом узле предоставляются посредством запросов, если в отчетах System.FM указано, что узел работает, и на узле имеется экземпляр, аналогичный экземпляру, связанному с сущностями. Когда System.FM сообщает о прекращении работы или перезапуске узла (новый экземпляр), хранилище данных о работоспособности автоматически удаляет развернутые сущности, которые могут существовать только на отключенном узле или предыдущем экземпляре узла.

* **SourceId**: System.FM.
* **Свойство**: State.
* **Дальнейшие действия**: если узел не работает вследствие обновления, он запустится после обновления. В этом случае для состояния работоспособности снова будет установлено значение ОК. Если узел не возобновляет работу или вышел из строя, необходимо дополнительно проанализировать проблему.

В следующем примере показано событие System.FM с нормальным состоянием работоспособности для работающего узла.

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### <a name="certificate-expiration"></a>Истечение срока действия сертификата
**System.FabricNode** выдает предупреждение, когда приближается истечение срока действия сертификатов, используемых узлом. У каждого узла есть три сертификата: **Certificate_cluster**, **Certificate_server** и **Certificate_default_client**. Если сертификат остается действительным по крайней мере еще в течение двух недель, в отчете сообщается об отсутствии проблем. Если срок действия истекает в течение двух недель, отображается отчет с предупреждением. Срок жизни этих событий не ограничен. Они удаляются при выходе узла из кластера.

* **SourceId**: System.FabricNode.
* **Свойство**: начинается с **Certificate** и содержит дополнительные сведения о типе сертификата.
* **Дальнейшие действия**: обновите сертификаты, если срок их действия вскоре истечет.

### <a name="load-capacity-violation"></a>Нарушение емкости для загрузки
Балансировщик нагрузки платформы Service Fabric выдает предупреждение, если обнаруживает нарушение емкости узла.

* **SourceId**: System.PLB.
* **Свойство**: начинается с **Capacity**.
* **Дальнейшие действия**: проверьте предоставленные метрики и просмотрите данные о текущей емкости на узле.

## <a name="application-system-health-reports"></a>Системные отчеты о работоспособности приложений
**System.CM** представляет собой службу диспетчера кластеров. Это система, которая управляет сведениями о приложении.

### <a name="state"></a>Состояние
Система System.CM сообщает об отсутствии проблем во время создания или обновления приложения. Она сообщает хранилищу данных о работоспособности об удалении приложения, чтобы обеспечить его удаление из хранилища.

* **SourceId**: System.CM.
* **Свойство**: State.
* **Дальнейшие действия**: если приложение создано, оно должно включать отчет о работоспособности диспетчера кластера. Или проверьте состояние приложения, выполнив запрос (например, командлет PowerShell **Get-ServiceFabricApplication -ApplicationName *имя_приложения***).

В следующем примере показано событие состояния в приложении **fabric:/WordCount** .

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## <a name="service-system-health-reports"></a>Системные отчеты о работоспособности служб
**System.FM** представляет собой службу диспетчера отработки отказов. Это система, которая управляет сведениями о службах.

### <a name="state"></a>Состояние
Система System.FM сообщает об отсутствии проблем, когда служба создана. Она удаляет сущность из хранилища данных о работоспособности, когда служба удалена.

* **SourceId**: System.FM.
* **Свойство**: State.

В следующем примере показано событие состояния в службе **fabric:/WordCount/WordCountService**.

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### <a name="unplaced-replicas-violation"></a>Нарушение: неразмещенные реплики
**System.PLB** выдает предупреждение, если ей не удалось найти расположение для одной или нескольких реплик службы. Отчет удаляется по истечении срока действия.

* **SourceId**: System.FM.
* **Свойство**: State.
* **Дальнейшие действия**: проверьте ограничения службы и текущее состояние расположения.

Ниже приведен пример нарушения для службы, для которой настроено 7 целевых реплик в кластере с 5 узлами.

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="partition-system-health-reports"></a>Системные отчеты о работоспособности разделов
**System.FM** представляет собой службу диспетчера отработки отказов. Это система, которая управляет сведениями о разделах служб.

### <a name="state"></a>Состояние
Система System.FM сообщает об отсутствии проблем, если создан работоспособный раздел. Она удаляет сущность из хранилища данных о работоспособности при удалении раздела.

Если в разделе нет минимального требуемого количества реплик, она сообщает об ошибке. Если в разделе есть минимальное требуемое количество реплик, но оно меньше целевого, она показывает предупреждение. Если в разделе наблюдается потеря кворума, система System.FM сообщает об ошибке.

Другие важные события: предупреждение о том, что перенастройка или сборка занимают больше времени, чем ожидалось. Ожидаемое время, необходимое для сборки или перенастройки, настраивается на основе сценариев службы. Если служба, например база данных SQL, содержит терабайт данных о состоянии, сборка занимает больше времени, чем для службы с меньшим объемом данных о состоянии.

* **SourceId**: System.FM.
* **Свойство**: State.
* **Дальнейшие действия**: если с состоянием работоспособности наблюдаются проблемы, возможно, некоторые реплики не созданы, не открыты либо их уровень не повышен до первичной или вторичной реплики должным образом. Во многих случаях основной причиной является ошибка службы при открытии или изменении реализации роли.

Ниже приведен пример работоспособного раздела.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

В следующем примере показаны данные о работоспособности раздела, количество реплик в котором меньше целевого значения. Дальнейшие действия: получите описание раздела со сведениями о его конфигурации: **MinReplicaSetSize** равно 2, а **TargetReplicaSetSize** — 7. Затем получите данные о количестве узлов в кластере: пять. Поэтому в данном случае невозможно разместить две реплики.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>Нарушение ограничений в отношении реплик
**System.PLB** выдает предупреждение, если обнаруживает нарушение ограничений реплик и не может разместить реплики раздела.

* **SourceId**: System.PLB.
* **Свойство**: начинается с **ReplicaConstraintViolation**.

## <a name="replica-system-health-reports"></a>Системные отчеты о работоспособности реплик
**System.RA** представляет собой компонент агента перенастройки. Это система для отслеживания состояния реплики.

### <a name="state"></a>Состояние
**System.RA** сообщает об отсутствии проблем при создании реплики.

* **SourceId**: System.RA.
* **Свойство**: State.

Ниже приведен пример работоспособной реплики.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### <a name="replica-open-status"></a>Состояние открытия реплики
Описание этого отчета о работоспособности содержит время начала (в формате UTC) при вызове API.

**System.RA** выдает предупреждение, если реплика открывается дольше заданного периода (по умолчанию 30 минут). Если API влияет на доступность службы, отчет выполняется гораздо быстрее (с заданным интервалом, который по умолчанию равен 30 секундам). Измеряемое время включает в себя время, необходимое для открытия репликатора и службы. Свойство меняется на ОК при открытии.

* **SourceId**: System.RA.
* **Свойство**. **ReplicaOpenStatus**
* **Дальнейшие действия**: если с состоянием работоспособности наблюдаются проблемы, проверьте, почему реплика открывается дольше, чем ожидалось.

### <a name="slow-service-api-call"></a>Медленный вызов API службы
Системы **System.RAP** и **System.Replicator** выдают предупреждение, если вызов пользовательского кода службы длится больше заданного периода. Предупреждение удаляется по завершении вызова.

* **SourceId**: System.RAP или System.Replicator.
* **Свойство**: имя медленного API. В описании приводятся дополнительные сведения о времени, в течение которого в работе API наблюдалась задержка.
* **Дальнейшие действия**: выясните причину, по которой вызов занимает больше времени, чем ожидалось.

В следующем примере показана секция с потерей кворума и приведены действия, позволяющие определить причину проблемы. Для одной из реплик получено предупреждение о состоянии работоспособности, поэтому мы получаем данные о ее работоспособности. Они свидетельствуют, что выполнение операции службы занимает больше времени, чем ожидалось (об этом событии сообщает система System.RAP). После получения этих сведений необходимо рассмотреть и изучить код службы. В этом случае реализация **RunAsync** службы с отслеживанием состояния порождает необработанное исключение. Так как реплики перезапускаются, не исключено, что ни одна реплика не будет отображаться с состоянием предупреждения. Вы можете повторить попытку получения состояния работоспособности и проверить, есть ли различия в идентификаторе реплики. В некоторых случаях помогают повторные попытки.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

При запуске неисправного приложения с помощью отладчика исключение, выданное RunAsync, отображается в окне «Диагностические события»:

![События диагностики Visual Studio 2015: сбой RunAsync в fabric:/HelloWorldStatefulApplication.][1]

События диагностики Visual Studio 2015: сбой RunAsync в **fabric:/HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>Переполнение очереди репликации
**System.Replicator** выдает предупреждение при переполнении очереди репликации. На сервере-источнике это обычно происходит, так как одна или несколько вторичных реплик выполняются слишком медленно для подтверждения операций. На сервере-получателе это обычно происходит, если служба медленно применяет операции. Предупреждение удаляется при очистке очереди.

* **SourceId**: System.Replicator.
* **Свойство**: **PrimaryReplicationQueueStatus** или **SecondaryReplicationQueueStatus**, в зависимости от роли реплики.

### <a name="slow-naming-operations"></a>Медленные операции именования
**System.NamingService** сообщает о состоянии работоспособности первичной реплики, когда операция именования длится больше допустимого. Примеры операций именования — [CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) и [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx). Дополнительные методы можно найти в статьях, посвященных FabricClient, например в статье о [методах управления службами](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx) или [методах управления свойствами](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx).

> [!NOTE]
> Служба именования присваивает имена служб определенному расположению в кластере, позволяя пользователям управлять именами и свойствами. Это секционированная материализованная служба Service Fabric. Одна из секций представляет имя Authority Owner, содержащее метаданные обо всех именах и службах Service Fabric. Имена Service Fabric сопоставляются с разными секциями, называемыми секциями владельца имени (NO). Таким образом, служба является расширяемой. Узнайте больше о [службе именования](service-fabric-architecture.md).
> 
> 

Когда операция именования длится дольше, чем ожидалось, для нее задается состояние Warning в *первичной реплике секции службы именования, выполняющей операцию*. Если операция завершается успешно, это состояние удаляется. Если операция завершается с ошибкой, отчет о работоспособности будет содержать сведения об ошибке.

* **SourceId**: System.NamingService.
* **Property**: начинается с префикса **Duration_** и определяет медленную операцию и имя экземпляра Service Fabric, к которому применяется операция. Например, если создание службы с именем fabric:/MyApp/MyService занимает слишком много времени, то имя свойства — Duration_AOCreateService.fabric:/MyApp/MyService. Владелец центра указывает на роль секции именования для этого имени и операции.
* **Дальнейшие действия**: проверьте, почему происходит сбой операции именования. Каждая операция может завершаться сбоем по различным причинам. Например, операция удаления службы может "зависнуть" на узле, так как узел приложения постоянно аварийно завершается на узле из-за ошибки пользователя в коде службы.

В следующем примере показана операция создания службы. Длительность операции превышает заданное время. Владелец центра выполняет повторную попытку и отправляет работу к владельцу именования. Владелец именования завершает последнюю операцию с истечением времени ожидания. В этом случае одна и та же реплика является первичной для ролей владельца центра и владельца именования.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Системные отчеты о работоспособности DeployedApplication
**System.Hosting** — это центр развернутых сущностей.

### <a name="activation"></a>Активация
Система System.Hosting сообщает об отсутствии проблем, если приложение успешно активировано на узле. В противном случае отображается сообщение об ошибке.

* **SourceId**: System.Hosting.
* **Property**: Activation, включающее версию выпуска.
* **Дальнейшие действия**: если в приложении есть проблемы с работоспособностью, выясните причину ошибки при активации.

Ниже приведен пример успешной активации.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Загрузить
**System.Hosting** сообщает об ошибке, если скачивание пакета приложения завершается сбоем.

* **SourceId**: System.Hosting.
* **Property**: **Download:*версия_развертывания***
* **Дальнейшие действия**: выясните причину ошибки при скачивании на узле.

## <a name="deployedservicepackage-system-health-reports"></a>Системные отчеты о работоспособности DeployedServicePackage
**System.Hosting** — это центр развернутых сущностей.

### <a name="service-package-activation"></a>Активация пакета службы
Система System.Hosting сообщает об отсутствии проблем, если пакет службы на узле успешно активирован. В противном случае отображается сообщение об ошибке.

* **SourceId**: System.Hosting.
* **Property**: Activation.
* **Дальнейшие действия**: выясните причину ошибки при активации.

### <a name="code-package-activation"></a>Активация пакета кода
**System.Hosting** сообщает об отсутствии проблем, если каждый пакет кода успешно активирован. Если активация завершилась ошибкой, выводится заданное предупреждение. Если не удалось активировать **CodePackage** или этот элемент завершил работу с ошибкой и превышением значения настроенного параметра **CodePackageHealthErrorThreshold**, система выдает сообщение об ошибке. Если пакет службы содержит несколько пакетов кода, для каждого из них создается отчет об активации.

* **SourceId**: System.Hosting.
* **Property**: использует префикс **CodePackageActivation** и содержит имя пакета кода и точку входа вида **CodePackageActivation:*имя_пакета_кода*:*SetupEntryPoint/EntryPoint*** (например, **CodePackageActivation:Code:SetupEntryPoint**).

### <a name="service-type-registration"></a>Регистрация типа службы
**System.Hosting** сообщает об отсутствии проблем, если тип службы успешно зарегистрирован. Система выдает сообщение об ошибке, если регистрация не выполнена вовремя (настраивается с помощью параметра **ServiceTypeRegistrationTimeout**). Если тип службы не регистрируется на узле из-за закрытия среды выполнения, система Hosting выдает предупреждение.

* **SourceId**: System.Hosting.
* **Свойство**: использует префикс **ServiceTypeRegistration** и содержит имя типа службы (например, **ServiceTypeRegistration:FileStoreServiceType**).

Ниже приведен пример работоспособного развернутого пакета службы.

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Загрузить
**System.Hosting** сообщает об ошибке при скачивании пакета службы.

* **SourceId**: System.Hosting.
* **Property**: **Download:*версия_развертывания***
* **Дальнейшие действия**: выясните причину ошибки при скачивании на узле.

### <a name="upgrade-validation"></a>Проверка обновления
**System.Hosting** сообщает об ошибке проверки во время обновления или об ошибке обновления на узле.

* **SourceId**: System.Hosting.
* **Property**: использует префикс **FabricUpgradeValidation** и содержит версию обновления.
* **Description**: сведения о возникшей ошибке.

## <a name="next-steps"></a>Дальнейшие действия
[Просмотр отчетов о работоспособности Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Проверка работоспособности службы и оповещение о проблемах](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Мониторинг и диагностика состояния служб в локальной среде разработки](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Обновление приложения Service Fabric](service-fabric-application-upgrade.md)

<!--HONumber=Oct16_HO2-->


