---
title: Описание кластера для балансировщика ресурсов | Microsoft Docs
description: Описание кластера Service Fabric посредством указания доменов сбоя, доменов обновления, свойств узлов и емкости узлов для диспетчера кластерных ресурсов.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# Описание кластера Service Fabric
Диспетчер Resource Manager кластера Service Fabric предоставляет несколько механизмов для описания кластера. Во время выполнения диспетчер кластерных ресурсов использует эту информацию, чтобы обеспечить высокую доступность служб, функционирующих в кластере, и в то же время гарантировать, что ресурсы в кластере используются должным образом.

## Основные понятия
Диспетчер кластерных ресурсов поддерживает некоторые функции, описывающие кластер:

* Домены сбоя
* Домены обновления
* Свойства узла
* Емкость узла

## Домены сбоя
Домен сбоя — это любая область координированного сбоя. Доменом сбоя является отдельный компьютер (так как его работа может остановиться по многим причинам — от сбоев питания и сбоев дисков до проблем встроенного ПО сетевой карты). Несколько компьютеров, подключенных к одному коммутатору Ethernet, находятся в одном домене сбоя, как если бы они были подключены к одному источнику питания. Так как они естественным образом пересекаются, домены сбоя по своей сути являются иерархическими и представлены в Service Fabric в виде кодов URI.

Если бы вы настраивали собственный кластер, вам нужно было бы подумать об этих различных областях сбоя и убедиться, что домены сбоя настроены правильно и Service Fabric знает, где размещение служб будет безопасным. Под "безопасным" размещением мы имеем в виду продуманное размещение, при котором отключение домена сбоя не приводит к выходу служб из строя (например, при неполадках в работе одного из выше перечисленных компонентов). В среде Azure мы используем данные домена сбоя, предоставляемые средой, для правильной настройки узлов в кластере от вашего имени. На следующем рисунке (рис. 7) мы для примера выделили цветом все сущности, которые объективно могут привести к появлению домена сбоя, и привели список всех доменов сбоя, которые появились в результате. В этом примере у нас есть центры обработки данных (DC), стойки (R) и колонки (B). Предположительно, если каждая колонка содержит несколько виртуальных машин, в иерархии домена сбоя может быть еще один уровень.

![Узлы, организованные через домены сбоя][Image1]

 Во время выполнения диспетчер Resource Manager кластера Service Fabric анализирует домены сбоя в кластере и пытается распределить реплики для данной службы таким образом, чтобы все они находились в разных доменах сбоя. Этот процесс помогает сохранить доступность службы в случае отказа какого-либо домена сбоя (на любом уровне иерархии).

 Для диспетчера кластерных ресурсов Service Fabric на самом деле не имеет значения количество уровней в иерархии. Однако так как ему нужно, чтобы потеря какой-либо части иерархии не влияла на кластер или службы, работающие в нем, то обычно рекомендуется, чтобы на каждом уровне глубины в домене сбоя имелось одинаковое количество компьютеров. Вследствие этого в конце дня один сегмент иерархии будет содержать не больше служб, чем другие.

 Из-за настройки кластера, в результате которого "дерево" доменов сбоя остается несбалансированным, диспетчеру кластерных ресурсов сложнее выяснить, где лучше расположить реплики. Это происходит в частности потому, что отказ отдельного домена может повлиять на доступность кластера, и поэтому диспетчер кластерных ресурсов лавирует между эффективным использованием компьютеров (размещая на них службы) в этом "сложном" домене и размещением служб таким образом, чтобы отказ домена не вызывал проблем.

 На схеме ниже показаны примеры двух различных структур кластера: в одной узлы равномерно распределены между доменами сбоя, а во второй с одним доменом сбоя связано гораздо больше узлов, чем с другими доменами. Обратите внимание, что в Azure выбор узлов, которые будут связаны с доменом сбоя или доменом обновления, осуществляется автоматически, поэтому вы никогда не заметите такого дисбаланса. Однако если вам когда-нибудь придется настраивать собственный кластер локально или в другой среде, то это нужно будет учесть.

 ![Две различные структуры кластеров][Image2]

## Домены обновления
Домены обновления — это еще одна функция, помогающая диспетчеру Resource Manager Service Fabric понять структуру кластера, чтобы заранее прогнозировать сбои. Домены обновления определяют области (наборы узлов), которые одновременно выйдут из строя во время обновления.

Домены обновления очень похожи на домены сбоя, но имеют ряд ключевых отличий. Во-первых, домены обновления обычно определяются политикой, тогда как домены сбоя тщательно определяются областями координированных сбоев (и, следовательно, структурой оборудования среды). В случае доменов обновления вы все-таки сможете решить, сколько их требуется. Еще одно различие заключается в том, что домены обновления (по крайней мере сегодня) не являются иерархическими: они больше похожи на простой тег, чем на иерархию.

На рисунке ниже показана вымышленная структура, где три домена обновления чередуются с тремя доменами сбоя. На нем также представлено одно возможное размещение трех разных реплик службы с отслеживанием состояния. Обратите внимание, что они находятся в разных доменах сбоя и обновления. Это означает, что если во время обновления службы произойдет отказ домена сбоя, то одна копия кода и данных в кластере будет выполняться и далее. В зависимости от потребностей этого может быть достаточно, однако вы, вероятно, заметите, что эта копия является старой (так как Service Fabric использует репликацию, основанную на кворуме). Чтобы действительно избежать простоев в случае двух сбоев, необходимо несколько реплик (как минимум пять).

![Размещение с доменами сбоя и обновления][Image3]

Наличие множества доменов обновления имеет свои преимущества и недостатки. Одно из преимуществ — то, что каждый этап обновления является фрагментированным и поэтому затрагивает меньшее количество узлов или служб. В результате за определенный момент времени необходимо перемещение меньшего количества служб, что обеспечивает меньшую текучесть системы и в целом повышает уровень надежности (так как любая неисправность затрагивает меньше служб). Недостатком при наличии большого количества доменов обновления является то, что Service Fabric проверяет работоспособность каждого домена обновления по мере его обновления и обеспечивает работоспособность соответствующего домена перед переходом к следующему. Цель этой проверки — убедиться, что у служб есть возможность стабилизации работы, а также проверить их работоспособность и выявить любые ошибки перед обновлением. Возникающие при этом негативные последствия допустимы, так как если выбран этот вариант, то неверные изменения не влияют слишком сильно на слишком большую часть службы в определенный момент времени.

Слишком малое количество доменов обновления имеет свои побочные эффекты: во время остановки в работе и обновления каждого отдельного домена большинство общих возможностей недоступны. Например, при наличии только трех доменов обновления вы отключаете примерно треть возможностей службы или кластера в определенный момент времени. Это нежелательно, так как в кластере должно быть достаточно других ресурсов, чтобы они могли справиться с рабочей нагрузкой. Это значит, что в обычных условиях эти узлы загружены меньше, чем должны быть, что увеличивает расходы.

Фактических ограничений на общее количество доменов сбоя или обновления в среде либо же на то, как они перекрываются, не существует. Стандартными структурами являются соотношение 1:1 (каждый отдельный домен сбоя сопоставляется со своим собственным доменом обновления), домен обновления на узел (экземпляр физической или виртуальной ОС) и модель с "чередованием", или "матричная" модель, в которой домены сбоя и домены обновления образуют матрицу с компьютерами, расположенными по диагонали.

![Структуры доменов сбоя и обновления][Image4]

Ответа на вопрос о том, какая структура лучше, не существует, так как каждая из них имеет свои преимущества и недостатки. Например, модель "1 домен сбоя — 1 домен обновления" отличается простотой настройки, а модель "1 домен сбоя на узел" больше всего понравится тем, кто привык управлять небольшими наборами компьютеров, когда каждый из них отключается отдельно от остальных.

Наиболее распространенной моделью (и именно ее мы используем для размещенных кластеров Azure Service Fabric) является матричная модель, в которой домены сбоя и домены обновления формируют таблицу и узлы располагаются по диагонали. То, как располагаются узлы в такой модели — плотно или разреженно, зависит от общего количества узлов по сравнению с количеством доменов сбоя и доменов обновления (иными словами, если кластер достаточно большой, все будет похоже на шаблон плотной матрицы, показанный в правом нижнем углу рис. 10).

## Ограничения доменов сбоя и обновления и соответствующее поведение
Диспетчер кластерных ресурсов воспринимает попытку сбалансированного распределения службы между доменами сбоя и обновления как ограничение. Дополнительные сведения об ограничениях см. в [этой статье](service-fabric-cluster-resource-manager-management-integration.md). Ограничения доменов сбоя и обновления определяются следующим образом: "Для определенного раздела службы разница в количестве реплик между двумя доменами никогда не должна составлять *больше 1*". На практике это означает, что для определенной службы отдельные перемещения или изменения могут быть восприняты кластером как недопустимые, так как они нарушают ограничение доменов сбоя или обновления.

Давайте рассмотрим один пример. Предположим, что у нас есть кластер с 6 узлами (У), на котором настроено 5 доменов сбоя (ДС) и 5 доменов обновления (ДО).

|  | ДС0 | ДС1 | ДС2 | ДС3 | ДС4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| ДО0 |У1 | | | | |
| ДО1 |У6 |У2 | | | |
| ДО2 | | |У3 | | |
| ДО3 | | | |У4 | |
| ДО4 | | | | |У5 |

Теперь предположим, что мы создаем службу с параметром TargetReplicaSetSize равным 5. Реплики размещаются на узлах У1-У5. Узел У6 фактически никогда не будет использован. Но почему? Давайте рассмотрим разницу между текущей структурой и тем, что произошло бы, если бы мы выбрали У6. Подумайте, как это относится к нашему определению ограничения доменов сбоя и обновления.

Ниже показана наша текущая структура и общее число реплик (Р) на домен сбоя и обновления.

|  | ДС0 | ДС1 | ДС2 | ДС3 | ДС4 | Всего ДО |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| ДО0 |Р1 | | | | |1 |
| ДО1 | |Р2 | | | |1 |
| ДО2 | | |Р3 | | |1 |
| ДО3 | | | |Р4 | |1 |
| ДО4 | | | | |Р5 |1 |
| Всего ДС |1 |1 |1 |1 |1 |- |

Обратите внимание, что такая структура является сбалансированной в плане распределения узлов на домен сбоя и домен обновления, а также в плане количества реплик на каждый из этих доменов. На каждый домен приходится одинаковое количество узлов и реплик.

Теперь давайте посмотрим, что произошло бы, если бы вместо У2, мы использовали У6. Как бы тогда распределялись реплики? Это выглядело бы примерно так:

|  | ДС0 | ДС1 | ДС2 | ДС3 | ДС4 | Всего ДО |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| ДО0 |Р1 | | | | |1 |
| ДО1 |Р5 | | | | |1 |
| ДО2 | | |Р2 | | |1 |
| ДО3 | | | |Р3 | |1 |
| ДО4 | | | | |Р4 |1 |
| Всего ДС |2 |0 |1 |1 |1 |- |

При этом нарушается наше определение ограничения для доменов сбоя, так как на ДС0 приходится 2 реплики, а на ДС1 — 0. То есть разница составляет 2, и из-за этого диспетчер кластерных ресурсов не разрешит использовать такую расстановку. Аналогично, если бы мы выбрали узлы У2-6, то получили бы следующее:

|  | ДС0 | ДС1 | ДС2 | ДС3 | ДС4 | Всего ДО |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| ДО0 | | | | | |0 |
| ДО1 |Р5 |Р1 | | | |2 |
| ДО2 | | |Р2 | | |1 |
| ДО3 | | | |Р3 | |1 |
| ДО4 | | | | |Р4 |1 |
| Всего ДС |1 |1 |1 |1 |1 |- |

Такая структура является сбалансированной в плане распределения на домен сбоя, но нарушает ограничение для доменов обновления (так как на ДО0 приходится 0 реплик, а на ДО1 — 2), и поэтому такая расстановка является недопустимой.

## Настройка доменов сбоя и обновления
Определение доменов сбоя и обновления выполняется автоматически в размещенных в Azure развертываниях Service Fabric. Service Fabric просто извлекает сведения о среде из Azure. В Azure сведения о домене сбоя и домене обновления выглядят как "один уровень", но на самом деле это инкапсулированная информация из нижних слоев Azure Stack и представление логических доменов сбоя и обновления с точки зрения пользователя.

Если вы настраиваете свой собственный кластер (или просто хотите попробовать запустить определенную топологию на компьютере разработки), необходимо самостоятельно предоставить сведения о доменах сбоя и обновления. В этом примере мы определяем кластер локальной разработки из 9 узлов, охватывающий три "центра обработки данных" (каждый с тремя стойками) и три домена обновления, чередующиеся с этими тремя центрами обработки данных. В шаблоне манифеста кластера это выглядит примерно так:

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```
> [!NOTE]
> В развернутых службах Azure домены сбоя и обновления назначаются автоматически. Поэтому определение ваших узлов и ролей в параметре infrastructure для Azure не содержит данные о доменах сбоя и обновления.
> 
> 

## Ограничения на размещение и свойства узлов
Иногда (фактически большую часть времени) необходимо убеждаться, что некоторые рабочие нагрузки выполняются только на некоторых узлах или некоторых наборах узлов в кластере. Например, для одних рабочих нагрузок могут требоваться графические процессоры или накопители SSD, а для других — нет. Хорошим примером этого является практически каждая n-уровневая архитектура, где одни машины используются в качестве фронтальной или интерфейсной части приложения (и поэтому, очевидно, доступны через Интернет), тогда как остальные (часто с другими аппаратными ресурсами) служат для вычислений и хранения (как правило, они не доступны через Интернет). Service Fabric ожидает, что даже в мире микрослужб существуют случаи, когда определенные рабочие нагрузки должны выполняться на конкретных конфигурациях оборудования, например:

* существующее n-уровневое приложение быстро перемещено в среду Service Fabric;
* рабочая нагрузка должна выполняться на конкретном оборудовании для повышения производительности, масштабирования или изоляции;
* рабочую нагрузку следует изолировать от других рабочих нагрузок по соображениям политики или потребления ресурсов.

Для поддержки таких типов конфигурации Service Fabric имеет первоклассное понятие того, что называется "ограничениями на размещение". С помощью ограничений на размещение можно указать, где должны выполняться определенные службы. Набор ограничений расширяется пользователями, то есть можно добавлять теги к узлам с пользовательскими свойствами, а затем выбирать их.

![Разные рабочие нагрузки структуры кластера][Image5]

Различные теги "ключ-значение" на узлах называются *свойствами* размещения узла (или просто свойствами узла), а выражение в службе называется *ограничением* на размещение. В свойстве узла могут быть указаны значения string, bool или signed long. Ограничением может быть любой логический оператор, который работает с различными свойствами узла в кластере. Ниже приведены допустимые селекторы в этих логических операторах (которые являются строками).

* Условные проверки для создания определенных операторов:
  * "равно" ==
  * "больше" >
  * "меньше" <
  * "не равно" !=
  * "больше или равно" >=
  * "меньше или равно" <=
* Логические операторы для группирования и отрицания:
  * "и" &&
  * "или" ||
  * "не" !
* Скобки для группирования операций:
  
  * ()
  
  Ниже приведено несколько примеров основных операторов ограничения, использующих некоторые из выше перечисленных символов. Обратите внимание, что свойствами узла могут быть строки, логические или числовые значения.
  
  * "Foo >= 5"
  * "NodeColor != green"
  * "((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"

Служба может быть размещена только на тех узлах, где оператор принимает общее значение True. Узлы без определенного свойства не совпадают с какими-либо ограничениями на размещение, содержащими это свойство.

Кроме того, Service Fabric определяет некоторые свойства по умолчанию, которые могут использоваться автоматически, так что их не нужно задавать. На момент написания этой статьи свойствами по умолчанию, определенными в каждом узле, являются NodeType и NodeName. Например, ограничение на размещение можно записать в таком виде: "(NodeType == NodeType03)". В целом мы пришли к выводу, что NodeType — одно из самых распространенных свойств, так как обычно оно имеет соотношение 1:1 к типу компьютера, который, в свою очередь, соответствуют типу рабочей нагрузки в традиционной архитектуре n-уровневого приложения.

![Ограничения на размещение и свойства узлов][Image6]

Предположим, что для данного типа узла были определены следующие свойства узла: ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Вы можете создать следующие ограничения на размещение службы:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Если вы уверены, что все узлы NodeType01 являются допустимыми, то можно просто выбрать этот тип узла с помощью ограничения на размещение, как показано на рисунках выше.

Одной замечательной особенностью ограничений на размещение службы является то, что они могут обновляться динамически во время выполнения. Поэтому если необходимо, то можно перемещать службу в кластере, добавлять и удалять требования и т. д. Service Fabric обеспечивает работу и доступность службы даже в том случае, если вносятся такие типы изменений.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Ограничения на размещение (а также много других элементов управления оркестратора, о которых мы еще будем говорить) задаются для каждого именованного экземпляра службы. Обновления всегда заменяют (перезаписывают) свойства, заданные ранее.

Также стоит отметить, что сейчас свойства узла определяются вместе с кластером, следовательно, и обновить их можно только при обновлении кластера. При этом для обновления свойств каждый узел должен быть выведен из строя, а затем восстановлен.

## Емкость
Одна из важнейших задач любого оркестратора — помощь в управлении потреблением ресурсов в кластере. Если вам нужна эффективная работа служб, самый нежелательный сценарий — это когда есть несколько узлов с высокой нагрузкой (что приводит к конфликтам ресурсов и снижению производительности), а на других узлах нагрузка небольшая (лишние ресурсы). Но давайте подумаем о кое-чем более простом, чем балансировка (которой мы коснемся чуть позже). Как обеспечить достаточное количество ресурсов для узлов?

В Service Fabric ресурсы представлены в виде "метрик". Метрики — это любые логические или физические ресурсы, которые нужно описать в Service Fabric. Метриками, например, являются атрибуты WorkQueueDepth или MemoryInMb. Метрики отличаются от ограничений на размещение и свойств узлов тем, что свойства узла — это обычно статические дескрипторы самих узлов, а метрики касаются ресурсов, находящихся на узлах и потребляемых службами в момент, когда они выполняются на узле. Поэтому свойством может быть, например, HasSSD со значением true или false, однако объем дискового пространства, доступного на SSD (и используемого службами), будет метрикой, например DriveSpaceInMb. В зависимости от емкости узла для атрибута DriveSpaceInMb задается объем общего места, свободного на диске, и службы будут сообщать, какая часть этой метрики используется во время выполнения.

При отключении *балансировки* всех ресурсов диспетчер кластерных ресурсов Service Fabric по-прежнему может гарантировать, что не превышена емкость ни одного узла (если только кластер в целом не слишком заполнен). Емкость — это другое *ограничение*, используемое диспетчером кластерных ресурсов, чтобы понять, какая часть ресурса используется на узле. На уровне службы и емкость, и потребление выражаются в виде метрик. Например, метрика может называться MemoryInMb. Определенный узел может иметь емкость (MemoryInMb) в размере 2048 МБ, в то время как определенная служба может сообщать, что в данный момент она потребляет 64 МБ от этой емкости.

Во время выполнения диспетчер кластерных ресурсов отслеживает, какая часть каждого ресурса используется на каждом узле (определяется по объему), а какая — остается (определяется путем вычитания объявленного количества используемых ресурсов из общего количества ресурсов каждой службы). С помощью этой информации диспетчер Resource Manager платформы Service Fabric может определить, где следует разместить или куда переместить реплики так, чтобы не была превышена емкость узлов.

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "MemoryInMb";
metric.PrimaryDefaultLoad = 64;
metric.SecondaryDefaultLoad = 64;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,64,64)
```

![Узлы кластера и емкость][Image7]

Это можно увидеть в манифесте кластера:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="Disk" Value="10000"/>
      </Capacities>
    </NodeType>
```

Нагрузка службы может изменяться также динамически. Предположим, что нагрузка реплики изменилась с 64 на 1024, но оставшаяся емкость узла, на котором она выполнялась, на тот момент составляла только 512 (от метрики MemoryInMb). Поэтому место, где в данный момент расположена реплика или экземпляр, станет недопустимым, так как использование всех реплик и экземпляров на этом узле начнет превышать его емкость. Позже мы подробно поговорим о сценарии, в котором нагрузка может изменяться динамически, но, что касается емкости, обработка выполняется всегда одинаково: диспетчер кластерных ресурсов включается автоматически и уменьшает загруженность узла, перемещая реплики или экземпляры из этого узла на другие. При этом диспетчер кластерных ресурсов пытается свести к минимуму стоимость всех перемещений (мы вернемся к вопросу стоимости позже).

## Емкость кластера
Так как же предотвратить переполнение кластера в целом? Если используется динамическая нагрузка, сделать мы можем очень мало (так как у служб могут быть всплески нагрузки независимо от действий, предпринятых диспетчером кластерных ресурсов: например, кластер с большим резервом сегодня может оказаться довольно маломощным, если вы станете знамениты завтра). При этом существуют некоторые встроенные элементы управления, которые помогут избежать основных проблем. Первое, что можно сделать, — это предотвратить создание новых рабочих нагрузок, которые могут привести к переполнению кластера.

Предположим, что вы собираетесь создать простую службу без отслеживания состояния и что некоторые ее нагрузки связаны с кластером (больше о передаче данных о динамической нагрузке и нагрузке по умолчанию поговорим позже). Предположим, что этой службе нужны определенные ресурсы (например, место на диске) и что по умолчанию она будет использовать 5 единиц места на диске для каждого экземпляра службы. Вы хотите создать 3 экземпляра службы. Отлично! Это значит, что в кластере должно быть 15 единиц места на диске только для того, чтобы можно было создать эти экземпляры службы. Service Fabric постоянно подсчитывает общую емкость и потребление для каждой метрики, поэтому мы легко можем определить и отклонить вызов создания службы, если места недостаточно.

Обратите внимание, что, так как единственным требованием является наличие 15 доступных единиц, пространство может быть распределено несколькими различными путями. Например, это может быть одна оставшаяся единица емкости на 15 различных узлах или три оставшиеся единицы емкости на 5 разных узлах и т. д. Если емкости недостаточно на трех разных узлах, Service Fabric реорганизует службы уже в кластере, чтобы освободить место на трех нужных узлах. Такие изменения почти всегда возможны, если только кластер не заполнен окончательно.

## Емкость буфера
Другая особенность, которая помогает пользователям управлять общей емкостью кластера, — добавление к емкости, выделенной на каждом узле, понятия зарезервированного буфера. Этот параметр является необязательным, но позволяет зарезервировать часть общей емкости узла только для размещения служб во время обновлений и отказов — тех случаев, где при иных обстоятельствах емкость кластера сокращается. Сегодня буфер глобально указан для каждой метрики для всех узлов в ClusterManifest. Значение, выбранное для зарезервированной емкости, будет в большей мере влиять на то, насколько ваши службы ограничены в ресурсах, а также на количество доменов сбоя и обновления, имеющихся в кластере. Обычно большее количество доменов сбоя и обновления означает, что можно выбрать меньшее значение буферизованной емкости, так как ожидается, что во время обновлений и сбоев может стать недоступной меньшая часть кластера. Обратите внимание, что указание процента свободного места имеет смысл, только если указана емкость узла для метрики.

Пример указания емкости буфера:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

При недостаточной емкости буфера попытки создания новых служб завершаются сбоем. Поэтому в кластере сохраняется достаточно запасного дополнительного места, а обновления и сбои не приводят к тому, что превышается емкость узлов. Диспетчер кластерных ресурсов предоставляет массу сведений с помощью PowerShell и интерфейсов API запросов, позволяя видеть параметры емкости буфера, общую емкость и текущее потребление для каждой метрики, используемой кластером. Здесь мы видим пример таких сведений:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2015 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2015 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## Дальнейшие действия
* Сведения об архитектуре и потоке информации в диспетчере кластерных ресурсов см. в [этой статье](service-fabric-cluster-resource-manager-architecture.md).
* Определение метрик дефрагментации — один из способов объединения нагрузки на узлах вместо ее рассредоточения. Сведения о настройке дефрагментации см. в [этой статье](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Начните с самого начала, [изучив общие сведения о диспетчере кластерных ресурсов Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Чтобы узнать, как диспетчер кластерных ресурсов управляет нагрузкой кластера и балансирует ее, ознакомьтесь со статьей о [балансировке нагрузки](service-fabric-cluster-resource-manager-balancing.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]: ./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

<!---HONumber=AcomDC_0824_2016-->