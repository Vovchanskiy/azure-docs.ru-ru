---
title: Развертывание существующего исполняемого файла в Azure Service Fabric | Microsoft Docs
description: Пошаговое руководство по упаковке имеющегося приложения в качестве гостевого исполняемого файла для его развертывания в кластере Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/22/2016
ms.author: msfussell;mikhegn

---
# <a name="deploy-a-guest-executable-to-service-fabric"></a>Развертывание гостевого исполняемого файла в Service Fabric
В Azure Service Fabric можно запустить приложение любого типа, в том числе приложения Node.js или Java, а также собственные приложения. В Service Fabric такие типы приложений называются гостевыми исполняемыми файлами.
Гостевые исполняемые файлы обрабатываются в Service Fabric как службы без отслеживания состояния. Это значит, что они будут размещены на узлах кластера на основе доступности и других метрик. В этой статье описывается, как упаковать и развернуть гостевой исполняемый файл в кластере Service Fabric, используя Visual Studio или служебную программу командной строки.

Здесь мы рассмотрим процедуру упаковки гостевого исполняемого файла и его развертывания в Service Fabric.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Преимущества запуска гостевого исполняемого файла в Service Fabric
Запуск гостевого исполняемого файла в кластере Service Fabric отличается некоторыми преимуществами.

* обеспечение высокой доступности; Приложения, работающие в среде Service Fabric, отличаются высокой доступностью. Service Fabric обеспечивает выполнение экземпляров приложения.
* Наблюдение за работоспособностью системы. Service Fabric наблюдает за работоспособностью приложений и в случае сбоя предоставляет диагностические данные.   
* Управление жизненным циклом приложений. Service Fabric не только обновляет приложения без ущерба для работы пользователей, но и автоматически откатывает приложение к предыдущей версии, если во время обновления возникло событие ухудшения работоспособности.    
* Плотность. В одном кластере могут работать несколько приложений, что позволяет отказаться от использования отдельного оборудования для каждого приложения.

## <a name="overview-of-application-and-service-manifest-files"></a>Обзор файлов манифестов приложений и служб
При развертывании гостевого исполняемого файла рекомендуем ознакомиться с моделью развертывания и упаковки Service Fabric, как описано в [модели приложения](service-fabric-application-model.md). Модель упаковки Service Fabric основана на двух XML-файлах — манифестах приложения и службы. Определение схемы для файла ApplicationManifest.xml и ServiceManifest.xml устанавливается с пакетом SDK в расположении *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Манифест приложения.** Этот манифест используется для описания приложения. В файле указаны службы, из которых состоит приложение, и другие параметры, которые определяют развертывание одной или нескольких служб (например, количество экземпляров).
  
  В Service Fabric приложение — это единица развертывания и обновления. Приложение может обновляться как одна единица, для которой выполняется управление потенциальными сбоями и откатами. Service Fabric гарантирует, что процесс обновления будет выполнен успешно, а в случае сбоя приложение не останется в неизвестном или нестабильном состоянии.
* **Манифест служб.** Манифест служб описывает компоненты службы. В этом файле содержится такая информация, как имя и тип службы, ее код, конфигурация и данные. Манифест службы также включает некоторые дополнительные параметры, которые могут использоваться для настройки службы после ее развертывания.

## <a name="application-package-file-structure"></a>Структура файла пакета приложения
Для развертывания приложения в Service Fabric оно должно следовать предопределенной структуре каталогов. Ниже приведен пример этой структуры.

```
|-- ApplicationPackageRoot
      |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
      |-- ApplicationManifest.xml
```

Каталог ApplicationPackageRoot содержит файл ApplicationManifest.xml, определяющий приложение. В подкаталогах служб, включенных в приложение, хранятся все необходимые службам артефакты: файл ServiceManifest.xml и, как правило, три дополнительных каталога.

* *Code*. В этом каталоге содержится код службы.
* *Config*. В этом каталоге хранится файл Settings.xml (а также другие файлы в случае необходимости), который служба может использовать во время работы для получения определенных параметров конфигурации.
* *Data*. Здесь хранятся дополнительные локальные данные, которые могут потребоваться службе. Обратите внимание, что в этом каталоге должны храниться только временные данные. Service Fabric не копирует и не реплицирует изменения в каталог Data при перемещении службы (например, во время отработки отказа).

Примечание. Если каталоги `config` и `data` вам не нужны, не создавайте их.

## <a name="packaging-an-existing-executable"></a>Упаковка имеющегося исполняемого файла
При упаковке гостевого исполняемого файла у вас есть выбор — использовать шаблон проекта Visual Studio или [создать пакет приложения вручную](#manually). При использовании Visual Studio мастер проекта создает структуру пакета приложения и файлы манифеста за вас.

> [!NOTE]
> Упаковать имеющийся исполняемый файл Windows в службу проще всего с помощью Visual Studio.
> 
> 

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Упаковка существующего исполняемого приложения с использованием Visual Studio
Visual Studio предоставляет шаблон службы Service Fabric для развертывания гостевого исполняемого файла в кластере Service Fabric.

Чтобы завершить публикацию, сделайте следующее:

1. Чтобы создать приложение Service Fabric, выберите "Файл > Создать проект".
2. Выберите гостевой исполняемый файл в качестве шаблона службы.
3. Щелкните "Обзор", чтобы выбрать папку с исполняемым файлом, и задайте остальные параметры, необходимые для создания службы.
   * Для параметра *Поведение пакета кода* можно настроить копирование всего содержимого папки проекта Visual Studio. Это удобно, если исполняемый файл не будет изменяться. Если исполняемый файл будет изменяться и требуется возможность выбора новых сборок в динамическом режиме, можно задать привязку к папке. Обратите внимание, что при создании проекта приложения в Visual Studio можно использовать связанные папки. С их помощью можно ссылаться на исходное расположение в проекте, что позволяет обновлять гостевой исполняемый файл в источнике назначения. Такие обновления становятся частью пакета приложения при создании.
   * *Program* позволяет выбрать исполняемый файл, который следует выполнить для запуска службы.
   * *Arguments* позволяет указать аргументы, которые нужно передать в исполняемый файл. Здесь можно указать список параметров с аргументами.
   * *WorkingFolder* позволяет задать рабочий каталог процесса, который будет запущен. Можно задать три значения:
     * `CodeBase`. Рабочим каталогом будет каталог Code в пакете приложения (каталог `Code` в показанной выше структуре файлов).
     * `CodePackage`. Рабочим каталогом будет корневой каталог в пакете приложения (каталог `GuestService1Pkg` в показанной выше структуре файлов).
     * `Work`. Файлы помещаются в подкаталог, который называется рабочим (work).
4. Задайте имя службы и нажмите кнопку "ОК".
5. Если службе необходима конечная точка для обмена данными, можно добавить значения протокола, порта и типа в файл ServiceManifest.xml. Например, `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Теперь можно выполнить упаковку и публикацию на локальном кластере, выполнив отладку решения в Visual Studio. Когда все будет готово, можно опубликовать приложение на удаленном кластере или выполнить возврат решения в систему управления версиями.
7. Перейдите к концу этой статьи, чтобы узнать, как просмотреть данные о работе гостевого исполняемого файла в Service Fabric Explorer.

<a id="manually"></a>

## <a name="manually-packaging-and-deploying-an-existing-executable"></a>Упаковка и развертывание имеющегося исполняемого файла вручную
Процесс упаковки гостевого исполняемого файла вручную включает в себя следующие этапы:

1. Создание структуры каталогов пакета.
2. Добавление файлов конфигурации и кода приложения.
3. Редактирование файла манифеста службы.
4. Редактирование файла манифеста приложения.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Создание структуры каталогов пакета
Создайте структуру каталогов, как описано выше.

### <a name="add-the-application's-code-and-configuration-files"></a>Добавление файлов конфигурации и кода приложения
Создав структуру каталогов, добавьте файлы кода и конфигурации приложения в каталоги Code и Config. При необходимости в этих каталогах можно создать дополнительные папки (подкаталоги).

Service Fabric создает расширенную копию содержимого корневого каталога приложения (выполняется операция xcopy), поэтому вам нужно создать только два каталога верхнего уровня: для кода и для параметров. (Вы можете выбрать любые имена. Дополнительные сведения см. в следующем разделе.)

> [!NOTE]
> Обязательно добавьте все файлы и зависимости, необходимые приложению. Service Fabric скопирует содержимое пакета приложения на все узлы кластера, где будут развертываться службы приложения. Пакет должен содержать весь код, необходимый для работы приложения. Мы не рекомендуем рассчитывать на то, что зависимости уже установлены.
> 
> 

### <a name="edit-the-service-manifest-file"></a>Редактирование файла манифеста службы
Далее нужно добавить в манифест службы следующие сведения:

* Имя типа службы. Это идентификатор, по которому Service Fabric определяет службу.
* Команда для запуска приложения (ExeHost).
* Любой сценарий, который нужно выполнить для установки и настройки приложения (SetupEntrypoint).

Ниже приведен пример файла `ServiceManifest.xml` .

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Давайте рассмотрим те элементы файла, которые вам нужно изменить.

#### <a name="updating-the-servicetypes"></a>Обновление ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Выберите для `ServiceTypeName`любое имя. Значение используется в файле `ApplicationManifest.xml` для идентификации службы.
* Необходимо указать `UseImplicitHost="true"`. Этот атрибут сообщает платформе Service Fabric, что служба использует автономное приложение, поэтому платформе нужно только запустить службу как процесс и следить за ее работоспособностью.

#### <a name="updating-the-codepackage"></a>Обновление CodePackage
Элемент CodePackage указывает на расположение и версию кода службы.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Атрибут `Name` определяет имя каталога в пакете приложения, где хранится код службы. `CodePackage` также имеет атрибут `version`. Он определяет версию кода, а также потенциально может использоваться для обновления кода службы с помощью инфраструктуры Service Fabric для управления жизненным циклом приложения.

#### <a name="optional:-updating-the-setupentrypoint"></a>Обновление SetupEntrypoint (необязательно)
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Элемент SetupEntrypoint позволяет задать исполняемый или пакетный файл, который следует запустить перед выполнением кода службы. Это необязательный шаг, поэтому этот элемент можно не указывать, если отсутствует необходимость в инициализации или установке. Файл, указанный в SetupEntryPoint, выполняется при каждом перезапуске службы.

Существует только один элемент SetupEntrypoint, поэтому, если для установки или настройки приложения требуется несколько сценариев, эти сценарии необходимо сгруппировать в один пакетный файл. SetupEntrypoint может запускать файлы любого типа, включая исполняемые и пакетные, а также командлеты PowerShell. Подробнее см. в статье [Настройка SetupEntryPoint](service-fabric-application-runas-security.md).

В приведенном выше примере в разделе SetupEntrypoint запускается пакетный файл `LaunchConfig.cmd`, расположенный в подкаталоге `scripts` каталога Code (при условии, что в элементе WorkingFolder указано имя каталога CodeBase).

#### <a name="updating-the-entrypoint"></a>Обновление Entrypoint
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Элемент `Entrypoint` в файле манифеста службы указывает, как нужно запускать службу. В элементе `ExeHost` указывается исполняемый файл (вместе с аргументами), который будет использоваться для запуска службы.

* `Program` — имя исполняемого файла, который нужно выполнить для запуска службы.
* `Arguments` — аргументы, которые нужно передать в исполняемый файл. Здесь можно указать список параметров с аргументами.
* `WorkingFolder` — рабочий каталог запускаемого процесса. Можно задать три значения:
  * `CodeBase`. Рабочим каталогом будет каталог Code в пакете приложения (каталог `Code` в показанной выше структуре файлов).
  * `CodePackage`. Рабочим каталогом будет корневой каталог в пакете приложения (каталог `GuestService1Pkg` в показанной выше структуре файлов).
    * `Work`. Файлы помещаются в подкаталог, который называется рабочим (work).

Элемент WorkingFolder позволяет задать правильный рабочий каталог, чтобы приложение или сценарии инициализации могли использовать относительные пути.

#### <a name="updating-the-endpoints-and-registering-with-naming-service-for-communication"></a>Обновление конечных точек и регистрация в службе именования для обмена данными
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
В приведенном выше примере в элементе `Endpoint` указываются конечные точки, в которых приложение может ожидать передачи данных. В этом примере приложение Node.js прослушивает HTTP-порт 3000.

Кроме того, можно настроить так, чтобы служба Service Fabric опубликовала конечную точку в службе именования. Таким образом, другие службы смогут обнаружить адрес конечной точки для этой службы.
Опубликованный адрес конечной точки имеет вид `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` и `PathSuffix` являются необязательными атрибутами. `IPAddressOrFQDN` — IP-адрес или полное доменное имя узла, где размещен исполняемый файл, которое вычисляется автоматически.

В примере ниже после развертывания службы в Service Fabric Explorer появится конечная точка в формате `http://10.1.4.92:3000/myapp/`, опубликованная для экземпляра службы. Если используется локальный компьютер, она будет в формате `http://localhost:3000/myapp/`. 

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Эти адреса можно использовать для обмена данными между службами через [обратный прокси-сервер](service-fabric-reverseproxy.md) .

### <a name="edit-the-application-manifest-file"></a>Редактирование файла манифеста приложения
Настроив файл `Servicemanifest.xml`, вам нужно внести в файл `ApplicationManifest.xml` изменения, которые позволят использовать правильные тип и имя службы.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
В элементе `ServiceManifestImport` можно указать одну или несколько служб, которые нужно добавить в приложение. Службы указываются в атрибуте `ServiceManifestName` в виде имени каталога, в котором хранится файл `ServiceManifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Настройка ведения журнала
В журналах консоли можно отслеживать работоспособность гостевых исполняемых файлов. В частности, из этих журналов можно узнать, не возвращают ли сценарии приложения и конфигурации какие-либо ошибки.
Перенаправление консоли можно настроить в файле `ServiceManifest.xml` в элементе `ConsoleRedirection`.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* Элемент `ConsoleRedirection` позволяет перенаправлять выходные данные консоли (stdout и stderr) в рабочий каталог для проверки на ошибки во время установки или запуска приложения в кластере Service Fabric.
  
  * `FileRetentionCount` определяет, сколько файлов будет сохраняться в рабочем каталоге. Например, значение 5 означает, что в рабочем каталоге будут храниться файлы журналов для пяти предыдущих запусков.
  * `FileMaxSizeInKb` определяет максимальный размер файла журнала.

Файлы журналов сохраняются в один из рабочих каталогов службы. Чтобы определить, где находятся файлы, откройте Service Fabric Explorer и посмотрите, на каком узле запущена служба и какой рабочий каталог используется. Эта процедура рассмотрена далее в этой статье.

## <a name="deployment"></a>Развертывание
Последним шагом является развертывание приложения. В приведенном ниже сценарии PowerShell показано развертывание приложения в локальный кластер разработки и запуск новой службы Service Fabric.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Службу Service Fabric можно развертывать в разных конфигурациях. Например, службу можно развернуть с одним или несколькими экземплярами. Либо развернуть ее так, чтобы на каждом узле кластера Service Fabric находилось по одному экземпляру службы.

Параметр `InstanceCount` командлета `New-ServiceFabricService` позволяет указать, сколько экземпляров службы следует запускать в кластере Service Fabric. Значение `InstanceCount` следует задавать в зависимости от типа развертываемого приложения. Ниже приводятся два самых распространенных сценария.

* `InstanceCount = "1"`. В этом случае в кластере развертывается только один экземпляр службы. На каком именно узле будет развернута служба, определяет планировщик Service Fabric.
* `InstanceCount ="-1"`. В этом случае на каждом узле кластера Service Fabric будет развернуто по одному экземпляру службы. В результате на каждом узле кластера будет по одному (и только одному) экземпляру службы.

Эта конфигурация подходит для клиентских приложений (например, конечной точки REST), ведь для работы с конечной точкой таким приложениям достаточно подключиться к любому узлу кластера. Эту конфигурацию также можно использовать, когда, к примеру, все узлы кластера Service Fabric подключены к балансировщику нагрузки. Таким образом, трафик клиента будет распределяться между всеми экземплярами службы на всех узлах кластера.

## <a name="check-your-running-application"></a>Проверка работающего приложения
В обозревателе Service Fabric определите узел, где запущена служба. В этом примере она выполняется на узле Node1.

![Узел, на котором запущена служба](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Если вы перейдете к узлу, а затем к приложению, вы увидите основные сведения об узле, включая его расположение на диске.

![Расположение на диске](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Если перейти в этот каталог в обозревателе сервера, вы увидите рабочий каталог и папку журналов службы, как показано на рисунке ниже.

![Расположение журнала](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Дальнейшие действия
Из этой статьи вы узнали об основной процедуре упаковки гостевого исполняемого файла и его развертывания в Service Fabric. Далее можно ознакомиться с дополнительными материалами по этой теме.

* [Пример для упаковки и развертывания гостевого исполняемого файла на GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), включая ссылку на предварительную версию средства упаковки
* [Развертывание нескольких пользовательских приложений](service-fabric-deploy-multiple-apps.md)
* [Создание первого приложения Service Fabric в Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)

<!--HONumber=Oct16_HO2-->


