---
title: "Гибридные локальные и облачные приложения (.NET) | Документация Майкрософт"
description: "Узнайте, как создать локальное или облачное гибридное приложение .NET с использованием ретранслятора служебной шины Azure."
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/16/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3c9d542edf04c119f5d97f80eacdfd0521acd77d


---
# <a name="net-onpremisescloud-hybrid-application-using-azure-service-bus-wcf-relay"></a>Создание локального или облачного гибридного приложения .NET с использованием ретранслятора WCF служебной шины Azure
## <a name="introduction"></a>Введение
В этой статье описывается, как создать облачное гибридное приложение с помощью Microsoft Azure и Visual Studio. В этом учебнике предполагается, что у вас нет опыта использования платформы Azure. Менее чем за 30 минут вы получите приложение, которое использует несколько ресурсов Microsoft Azure и выполняется в облаке.

Вы узнаете:

* Как создать или адаптировать существующую веб-службу для использования веб-решением.
* Как использовать службу ретранслятора WCF служебной шины Azure для обмена данными между приложением Azure и веб-службой, которая размещается в другом месте.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-the-service-bus-relay-helps-with-hybrid-solutions"></a>Как ретрансляция служебной шины помогает работать с гибридными решениями
Бизнес-решения обычно состоят из сочетания пользовательского кода, написанного для удовлетворения новых и уникальных бизнес-требований, с существующими функциональными возможностями, предоставляемыми уже используемыми решениями и системами.

Архитекторы решений начинают использовать облако для упрощения реализации масштабирования и снижения эксплуатационных расходов. При этом они обнаруживают,что существующие активы служб, которые они хотели бы использовать в качестве стандартных блоков для своих решений, находятся за корпоративным брандмауэром, поэтому обращение к ним из облачного решения затруднено. Многие внутренние службы построены или размещены таким образом, который не позволяет легко предоставлять их на границе корпоративной сети.

Ретранслятор служебной шины предназначен для случаев, когда существующие веб-службы Windows Communication Foundation (WCF) безопасно предоставляются для решений, находящихся вне периметра корпоративной сети, без внесения существенных изменений в инфраструктуру корпоративной сети. Такие службы ретранслятора служебной шины по-прежнему размещаются внутри существующей среды, однако они делегируют функции прослушивания входящих сеансов и запросов размещенной в облаке службе Service Bus. Служебная шина также защищает эти службы от несанкционированного доступа с помощью проверки подлинности [подписанного URL-адреса](../service-bus-messaging/service-bus-sas-overview.md) (SAS).

## <a name="solution-scenario"></a>Сценарий решений
В этом учебнике вы создадите веб-сайт ASP.NET, который позволит просматривать список продуктов на странице складских запасов.

![][0]

В учебнике предполагается, что имеются сведения о продуктах в существующей локальной системе, для доступа к которой используется ретрансляция служебной шины. Это имитируется веб-службой, запущенной в простом консольном приложении и поддерживаемой набором продуктов в памяти. Можно запустить это консольное приложение на своем компьютере и развернуть веб-роль в Azure. Таким образом вы увидите, как веб-роль, выполняемая в центре обработки данных Azure, действительно вызывает ваш компьютер, даже если он (почти наверняка) будет находиться хотя бы за одним брандмауэром и слоем преобразования сетевых адресов (NAT).

Снимок экрана начальной страницы готового веб-приложения приведен ниже.

![][1]

## <a name="set-up-the-development-environment"></a>Настройка среды разработки
Прежде чем начать разработку приложения для Azure, подготовьте нужные инструменты и настройте среду разработки.

1. Установите пакет Azure SDK для .NET с [этой страницы][Получить инструменты и пакет SDK].
2. Щелкните **Install the SDK** (Установить пакет SDK) для используемой версии Visual Studio. На описанных в этом учебнике шагах используется Visual Studio 2015.
3. При появлении запроса на выполнение или сохранение файла установки щелкните **Выполнить**.
4. В **установщике веб-платформы** щелкните **Установить**, чтобы продолжить.
5. После завершения установки у вас будут все компоненты, необходимые для начала разработки приложения. В состав пакета SDK входят инструменты для эффективной разработки приложений Azure в Visual Studio. Если у вас не установлено приложение Visual Studio, будет автоматически установлена бесплатная версия Visual Studio Express.

## <a name="create-a-namespace"></a>Создание пространства имен
Чтобы начать использование функций служебной шины в Azure, необходимо сначала создать пространство имен службы. Пространство имен предоставляет контейнер для адресации ресурсов служебной шины в вашем приложении.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-an-onpremises-server"></a>Создание локального сервера
Во-первых, нужно создать (макетную) локальную систему каталогов продукции. Она будет довольно простой. Это выглядит как представление фактической локальной системы каталогов продукции в виде полнофункциональной службы, которую мы пытаемся интегрировать.

Этот проект запускается как консольное приложение Visual Studio, используя [пакет NuGet служебной шины Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) для включения библиотек и параметров конфигурации служебной шины.

### <a name="create-the-project"></a>Создание проекта
1. Запустите Microsoft Visual Studio, используя привилегии администратора. Чтобы запустить Visual Studio, используя привилегии администратора, щелкните правой кнопкой мыши значок программы **Visual Studio** и выберите пункт **Запустить от имени администратора**.
2. В меню **Файл** Visual Studio выберите **Создать**, а затем — **Проект**.
3. В разделе **Visual C#** области **Установленные шаблоны** щелкните **Консольное приложение**. В поле **Имя** введите **ProductsServer**.
   
   ![][11]
4. Нажмите кнопку **ОК**, чтобы создать проект **ProductsServer**.
5. Если диспетчер пакетов NuGet для Visual Studio уже установлен, пропустите следующий шаг. В противном случае посетите сайт [NuGet][NuGet] и щелкните [Установить NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Следуйте инструкциям на экране для установки диспетчера пакетов NuGet, а затем перезапустите Visual Studio.
6. В обозревателе решений щелкните правой кнопкой мыши проект **ProductsServer** и выберите пункт **Управление пакетами NuGet**.
7. Щелкните вкладку **Обзор** и выполните поиск `Microsoft Azure Service Bus`. Щелкните **Установить**и примите условия использования.
   
   ![][13]
   
   Обратите внимание, что на необходимые клиентские сборки теперь имеется ссылка.
8. Добавьте новый класс для контракта на свою продукцию. В обозревателе решений щелкните правой кнопкой проект **ProductsServer**, а затем выберите **Добавить** и **Класс**.
9. В поле **Имя** введите **ProductsContract.cs**. Нажмите кнопку **Добавить**.
10. В **ProductsContract.cs** замените определение пространства имен на приведенный ниже код, определяющий контракт для службы.
    
    ```
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;
    
        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }
    
        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();
    
        }
    
        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```
11. В Program.cs замените определение пространства имен на следующий код, добавляющий службу профилей и узел для нее.
    
    ```
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;
    
        // Implement the IProducts interface.
        class ProductsService : IProducts
        {
    
            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };
    
            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }
    
        }
    
        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();
    
                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();
    
                sh.Close();
            }
        }
    }
    ```
12. В обозревателе решений дважды щелкните файл **App.config**, чтобы открыть его в редакторе Visual Studio. В нижней части элемента **&lt;system.ServiceModel&gt;** (но в пределах &lt;system.ServiceModel&gt;) добавьте приведенный ниже код XML. Не забудьте заменить *yourServiceNamespace* именем пространства имен, а *yourKey* — ключом SAS, полученным ранее на портале.
    
    ```
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
13. В файле App.config в элементе **&lt;appSettings&gt;** замените значение строки подключения значением, полученным ранее на портале. 
    
    ```
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. Нажмите клавиши **CTRL+SHIFT+B** или в меню **Сборка** щелкните **Собрать решение**, чтобы создать приложение и проверить результат.

## <a name="create-an-aspnet-application"></a>Создание приложения ASP.NET
В этом разделе будет выполнено построение простого приложения ASP.NET, которое будет отображать данные, полученные из службы продукции.

### <a name="create-the-project"></a>Создание проекта
1. Убедитесь, что система Visual Studio запущена с правами администратора.
2. В меню **Файл** Visual Studio выберите **Создать**, а затем — **Проект**.
3. В разделе **Visual C#** области **Установленные шаблоны** щелкните **Веб-приложение ASP.NET**. Присвойте проекту имя **ProductsPortal**. Нажмите кнопку **ОК**.
   
   ![][15]
4. В списке **Выбор шаблона** выберите **MVC**. 
5. Установите флажок **Разместить в облаке**.
   
   ![][16]
6. Нажмите кнопку **Изменить способ проверки подлинности**. В диалоговом окне **Изменить способ проверки подлинности** установите переключатель **Без проверки подлинности** и нажмите кнопку **ОК**. В этом учебнике выполняется развертывание приложения, для которого не требуется имя пользователя.
   
    ![][18]
7. В диалоговом окне **Новый проект ASP.NET** в разделе **Microsoft Azure** установите флажок **Разместить в облаке**, а в раскрывающемся списке выберите пункт **Служба приложений**.
   
   ![][19]
8. Нажмите кнопку **ОК**. 
9. Теперь необходимо настроить ресурсы Azure для нового веб-приложения. Выполните действия, описанные в разделе [Настройка ресурсов Azure для нового веб-приложения](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). Затем вернитесь к этому учебнику и перейдите к следующему шагу.
10. В обозревателе решений щелкните правой кнопкой мыши **Модели**, а затем выберите **Добавить** и **Класс**. В поле **Имя** введите **Product.cs**. Нажмите кнопку **Добавить**.
    
    ![][17]

### <a name="modify-the-web-application"></a>Изменение веб-приложения
1. В Visual Studio замените существующее определение пространства имен в файле на следующий код.
   
   ```
   // Declare properties for the products inventory.
    namespace ProductsWeb.Models
   {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
   }
   ```
2. В обозревателе решений разверните папку **Контроллеры** и дважды щелкните файл **HomeController.cs**, чтобы открыть его в Visual Studio.
3. В файле **HomeController.cs** замените имеющееся определение пространства имен следующим кодом.
   
    ```
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;
   
        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```
4. В обозревателе решений разверните папку Views\Shared, а затем дважды щелкните файл **_Layout.cshtml**, чтобы открыть его в редакторе Visual Studio.
5. Замените все вхождения элемента **Мое приложение MVC ASP.NET** текстом **Продукты LITWARE**.
6. Удалите ссылки **Главная**, **О программе** и **Контакт**. В следующем примере удалите выделенный код.
   
    ![][41]
7. В обозревателе решений разверните папку Views\Home, а затем дважды щелкните файл **Index.cshtml**, чтобы открыть его в редакторе Visual Studio.
   Замените все содержимое файла следующим кодом.
   
   ```
   @model IEnumerable<ProductsWeb.Models.Product>
   
   @{
            ViewBag.Title = "Index";
   }
   
   <h2>Prod Inventory</h2>
   
   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>
   
   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }
   
   </table>
   ```
8. Чтобы проверить текущий результат, нажмите клавиши **CTRL+SHIFT+B** для сборки проекта.

### <a name="run-the-app-locally"></a>Локальный запуск приложения
Запустите приложение, чтобы убедиться, что оно работает.

1. **ProductsPortal** должен быть активным проектом. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Назначить запускаемым проектом**.
2. В Visual Studio нажмите клавишу F5.
3. Приложение должно начать выполняться в браузере.
   
   ![][21]

## <a name="put-the-pieces-together"></a>Объединение элементов
Следующим шагом является подключение локального сервера продуктов к приложению ASP.NET.

1. В Visual Studio повторно откройте проект **ProductsPortal**, созданный в разделе [Создание приложения ASP.NET](#create-an-aspnet-application).
2. Аналогично шагу в разделе "Создание локального сервера" добавьте пакет NuGet в ссылки проекта. В обозревателе решений щелкните правой кнопкой мыши проект **ProductsPortal** и выберите пункт **Управление пакетами NuGet**.
3. Выполните поиск по фразе «служебная шина» и выберите элемент **Служебная шина Microsoft Azure** . Завершите установку и закройте это диалоговое окно.
4. В обозревателе решений щелкните правой кнопкой мыши проект **ProductsPortal** и выберите пункт **Добавить**, а затем — **Существующий элемент**.
5. Перейдите к файлу **ProductsContract.cs** в консольном проекте **ProductsServer**. Щелкните, чтобы выделить ProductsContract.cs. Щелкните стрелку вниз рядом с пунктом **Добавить**, затем нажмите кнопку **Добавить как связь**.
   
   ![][24]
6. Теперь откройте файл **HomeController.cs** в редакторе Visual Studio и замените существующее определение пространства имен приведенным ниже кодом. Замените *yourServiceNamespace* на имя пространства имен вашей службы, а *yourKey* — на ключ SAS. Это позволит клиенту вызывать локальную службу, возвращая результат в вызов.
   
   ```
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;
   
       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;
   
           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }
   
           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```
7. В обозревателе решений щелкните правой кнопкой мыши решение **ProductsPortal** (не проект). Щелкните **Добавить**, а затем — **Существующий проект**.
8. Перейдите к проекту **ProductsServer**, а затем дважды щелкните файл решения **ProductsServer.csproj**, чтобы добавить его.
9. Чтобы данные отобразились в решении **ProductsPortal**, нужно запустить проект **ProductsServer**. В обозревателе решений щелкните правой кнопкой мыши решение **ProductsPortal** и выберите пункт **Свойства**. Откроется диалоговое окно **Страницы свойств**.
10. В левой части окна выберите **Запускаемый проект**. В правой части окна выберите **Несколько запускаемых проектов**. **ProductsServer** и **ProductsPortal** должны отображаться в указанном порядке с назначенным действием **Запустить**.
    
      ![][25]
11. В левой части диалогового окна **Свойства** щелкните **Зависимости проектов**.
12. В списке **Проекты** выберите **ProductsServer**. **Снимите** флажок **ProductsPortal**.
13. В списке **Проекты** выберите **ProductsPortal**. Установите флажок **ProductsServer**. 
    
    ![][26]
14. В диалоговом окне **Страницы свойств** нажмите кнопку **ОК**.

## <a name="run-the-project-locally"></a>Локальный запуск проекта
Чтобы проверить приложение локально, в окне Visual Studio нажмите клавишу **F5**. Первым должен запуститься локальный сервер (**ProductsServer**), а затем в окне браузера должно запуститься приложение **ProductsPortal**. На этот раз вы сможете проследить получение данных списка складских запасов продукции из локальной системы службы продукции.

![][10]

На странице **ProductsPortal** нажмите кнопку **Обновить**. При каждом обновлении страницы после вызова `GetProducts()` из **ProductsServer** на сервере приложения будет отображаться сообщение.

Прежде чем перейти к следующему шагу, закройте оба приложения.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Развертывание проекта ProductsPortal в веб-приложении Azure
Следующий шаг — преобразование внешнего интерфейса **ProductsPortal** в веб-приложение Azure. Сначала разверните проект **ProductsPortal**, выполнив все инструкции из раздела [Развертывание веб-проекта в веб-приложении Azure](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app). По завершении развертывания вернитесь к этому руководству и перейдите к следующему шагу.

> [!NOTE]
> Если веб-проект **ProductsPortal** запустится после развертывания автоматически, в окне браузера может появиться сообщение об ошибке. Это ожидаемое поведение, и означает, что приложение **ProductsServer** еще не запущено.
> 
> 

Скопируйте URL-адрес развернутого веб-приложения, так как он понадобится для выполнения следующего шага. Кроме того, этот URL-адрес можно найти в окне "Действие службы приложений Azure" в Visual Studio.

![][9] 

### <a name="set-productsportal-as-web-app"></a>Настройка ProductsPortal в качестве веб-приложения
Перед запуском приложения в облаке необходимо, чтобы проект **ProductsPortal** в Visual Studio запускался как веб-приложение.

1. В Visual Studio щелкните правой кнопкой мыши проект **ProjectsPortal** и выберите пункт **Свойства**.
2. В столбце слева щелкните **Веб**.
3. В разделе **Действие при запуске** нажмите кнопку **Начальный URL-адрес** и введите в текстовое поле URL-адрес развернутого ранее веб-приложения, например `http://productsportal1234567890.azurewebsites.net/`.
   
    ![][27]
4. В меню **Файл** Visual Studio выберите **Сохранить все**.
5. В Visual Studio в меню "Сборка" выберите **Пересобрать решение**.

## <a name="run-the-application"></a>Выполнение приложения
1. Нажмите клавишу F5, чтобы создать и запустить приложение. Первым должен запуститься локальный сервер (консольное приложение **ProductsServer**), затем в окне браузера должно запуститься приложение **ProductsPortal**, как показано на снимке экрана ниже. Обратите внимание, что в списке складских запасов продукции содержатся данные, полученные из локальной системы службы продукции, и они отображаются в веб-приложении. Проверьте URL-адрес. Приложение **ProductsPortal** должно запускаться в облаке как веб-приложение Azure. 
   
   ![][1]
   
   > [!IMPORTANT]
   > Консольное приложение **ProductsServer** должно запускаться с возможностью передачи данных в приложение **ProductsPortal**. Если в браузере появляется сообщение об ошибке, подождите несколько секунд, пока **ProductsServer** загрузит и отобразит следующее сообщение. Затем нажмите кнопку **Обновить** в браузере.
   > 
   > 
   
   ![][37]
2. В браузере на странице **ProductsPortal** нажмите кнопку **Обновить**. При каждом обновлении страницы после вызова `GetProducts()` из **ProductsServer** на сервере приложения будет отображаться сообщение.
   
    ![][38]

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию о Service Bus см. на следующих ресурсах.  

* [Служебная шина Azure][sbwacom]  
* [Использование очередей служебной шины][sbwacomqhowto]  

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[Получить инструменты и SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
[NuGet]: http://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


[sbwacom]: /documentation/services/service-bus/  
[sbwacomqhowto]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md




<!--HONumber=Nov16_HO2-->


