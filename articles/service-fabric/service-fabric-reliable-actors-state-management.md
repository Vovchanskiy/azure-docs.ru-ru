---
title: Управление состоянием субъектов Reliable Actors | Microsoft Docs
description: Описание управления, сохранения и репликации субъектов Reliable Actors для обеспечения высокого уровня доступности.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2016
ms.author: vturecek

---
# Управление состоянием субъектов Reliable Actors
Субъекты Reliable Actors — это однопотоковые объекты для инкапсуляции логики и состояния. Так как субъекты выполняются в службах Reliable Services, они могут поддерживать состояние, используя те же механизмы сохранения и репликации, которые применяются службами Reliable Services. При этом субъекты не теряют свое состояние после сбоев, повторной активации после сборки мусора или перемещения между узлами в кластере из-за балансировки ресурсов или обновления.

## Сохранение состояния и репликация
Все субъекты Reliable Actors *отслеживают состояние*, так как каждый экземпляр субъекта сопоставляется с уникальным идентификатором. Это означает, что повторные вызовы одного идентификатора субъекта направляются в один и тот же экземпляр субъекта. Это отличается от системы без отслеживания состояния, в которой не гарантируется, что клиентские вызовы будут каждый раз направляться на один и тот же сервер. Поэтому службы субъектов всегда отслеживают состояние.

Несмотря на то, что субъекты отслеживают состояние, это не означает, что они надежно хранят состояние. Субъекты могут выбрать уровень сохранения и репликации состояния на основе их требований к хранению данных:

* **Сохраняемое состояние:** состояние сохраняется на диске и реплицируется на 3 или более реплик. Это самый устойчивый вариант хранения состояния, при котором оно может сохраниться даже после полного сбоя кластера.
* **Непостоянное состояние:** состояние реплицируется на 3 или более реплик и хранится только в памяти. Это обеспечивает устойчивость в случае сбоя узла, сбоя субъекта и во время обновления и балансировки ресурсов. Однако состояние не сохраняется на диск, поэтому если все реплики будут потеряны одновременно, состояние также теряется.
* **Несохраняемое состояние:** состояние не реплицируется и не записывается на диск. Для субъектов, которым просто не требуется надежно хранить состояние.

Каждый уровень сохраняемости — это просто другая конфигурация *поставщика состояний* и *репликации* службы. То, записывается ли состояние на диск, зависит от *поставщика состояний* — компонента службы Reliable Service, который хранит состояние. А репликация зависит от того, на скольких репликах развертывается служба. Как и в случае со службами Reliable Services, поставщика состояний и число реплик можно легко настроить вручную. Платформа субъектов предоставляет атрибут, который при использовании с субъектом автоматически выбирает поставщика состояний по умолчанию и создает параметры числа реплик для достижения одного из этих трех параметров сохраняемости.

### Сохраненное состояние
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```  
Этот параметр использует поставщик состояний, который хранит данные на диске, и автоматически задает 3 реплики службы.

### Непостоянное состояние
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
Этот параметр использует поставщик состояний только в памяти и настраивает 3 реплики.

### Несохраняемое состояние
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
Этот параметр использует поставщик состояний только в памяти и настраивает 1 реплику.

### Значения по умолчанию и созданные параметры
При использовании атрибута `StatePersistence` поставщик состояний автоматически выбирается во время выполнения при запуске службы субъекта. Однако число реплик задается средствами построения субъекта Visual Studio во время компиляции. Средства построения автоматически создают *службу по умолчанию* для службы субъекта в ApplicationManifest.xml. Параметры создаются для **минимального размера набора реплик** и **целевого размера набора реплик**. Конечно, вы можете изменить эти параметры вручную, однако при каждом изменении атрибута `StatePersistence` параметрам будут присвоены значения размера набора реплик по умолчанию для выбранного атрибута `StatePersistence`, а все предыдущие значения будут переопределены. Другими словами, указанные в файле ServiceManifest.xml значения будут переопределены **только** во время сборки, при изменении значения атрибута `StatePersistence`.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## Диспетчер состояний
У каждого экземпляра субъекта есть собственный диспетчер состояний: структура данных, подобная словарю, которая надежно хранит пары "ключ-значение". Диспетчер состояний — это оболочка вокруг поставщика состояний. Его можно использовать для хранения данных независимо от того, какой параметр сохраняемости применяется, но он не предоставляет никаких гарантий, что для работающей службы субъекта можно изменить параметр непостоянного состояния (только в памяти) на сохраняемое состояние с помощью последовательного обновления при сохранении данных. Тем не менее, можно изменить количество реплик для запущенной службы.

Ключи диспетчера состояний должны быть строками, хотя значения универсальные и могут быть любого типа, в том числе пользовательского. Значения, хранящиеся в диспетчере состояний, должны быть сериализуемыми контрактом данных, поскольку они могут передаваться по сети другим узлам во время репликации и быть записаны на диск в зависимости от параметра сохранения состояния субъекта.

Диспетчер состояний предоставляет общие методы словаря для управления состоянием, аналогичные используемым в надежном словаре.

### Доступ к состоянию
Доступ к состоянию можно получить через диспетчер состояний по ключу. Методы диспетчера состояний асинхронны, так как они могут потребовать дискового ввода-вывода, если у субъектов сохраняемое состояние. При первом доступе объекты состояния кэшируются в памяти. Повторные операции доступа к объектам обращаются к объектам непосредственно из памяти и возвращают данные синхронно без дискового ввода-вывода или асинхронного переключения контекста. Объект состояния удаляется из кэша в следующих случаях:

* метод субъекта вызывает необработанное исключение после извлечения объекта из диспетчера состояний;
* субъект повторно активируется после отключения или из-за сбоя;
* поставщик состояния записывает состояние на диск. Поведение зависит от реализации поставщика состояний. Поставщик состояний по умолчанию для параметра `Persisted` применяет такое поведение.

Получить состояние можно с помощью стандартной операции *Get*, которая вызывает исключение `KeyNotFoundException`, если запись не существует для указанного ключа:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```

Также получить состояние можно, используя метод *TryGet*. Он не вызывает исключение, если запись не существует для указанного ключа:

```csharp
class MyActor : Actor, IMyActor
{
    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```

### Сохранение состояния
Методы извлечения диспетчера состояний возвращают ссылку на объект в локальной памяти. Изменение этого объекта только в локальной памяти не приводит к его длительному сохранению. Когда объект извлекается из диспетчера состояний и изменяется, его необходимо снова вставить в диспетчер состояний для постоянного сохранения.

Состояние можно вставить с помощью безусловной операции *Set*, которая эквивалента синтаксису `dictionary["key"] = value`:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```

Состояние можно добавить с помощью метода *Add*, который вызывает исключение `InvalidOperationException` при попытке добавить ключ, который уже существует:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```

Состояние также можно добавить с помощью метода *TryAdd*, который вызывает исключение при попытке добавить уже существующий ключ:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```

В конце метода субъекта диспетчер состояний автоматически сохраняет все значения, которые были добавлены или изменены с помощью операции вставки или обновления. "Сохранение" может включать в себя сохранение на диск и репликацию в зависимости от используемых параметров. Значения, которые не были изменены, не сохраняются и не реплицируются. Если значения не были изменены, операция сохранения не выполняет никаких действий. При сбое сохранения измененное состояние удаляется, и загружается исходное состояние.

Состояние также можно сохранить вручную, вызвав метод `SaveStateAsync` в базовом субъекте:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```

### Удаление состояния
Состояние можно удалить из диспетчера состояний субъекта без возможности восстановления, вызвав метод *Remove*. Этот метод вызывает исключение `KeyNotFoundException` при попытке удалить ключ, который не существует:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```

Состояние также можно удалить без возможности восстановления с помощью метода *TryRemove*, который не вызывает исключение при попытке удалить несуществующий ключ:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```

## Дальнейшие действия
* [Сериализация типа субъекта](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
* [Полиморфизм субъекта и объектно-ориентированные шаблоны проектирования](service-fabric-reliable-actors-polymorphism.md)
* [Диагностика и мониторинг производительности в Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Справочная документация по API субъектов](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Пример кода](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0713_2016-->