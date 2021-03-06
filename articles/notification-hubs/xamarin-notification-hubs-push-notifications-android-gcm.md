---
title: "Приступая к работе с Центрами уведомлений для приложений Xamarin.Android | Документация Майкрософт"
description: "Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android."
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5137e0f33497dfe7ee815bb4bc30929364f6df72


---
# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>Приступая к работе с Центрами уведомлений для приложений Xamarin в Android
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Обзор
В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android.
Вы создаете пустое приложение Xamarin.Android, которое получает push-уведомления с помощью Google Cloud Messaging (GCM). По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений. Готовый код доступен в примере [приложения NotificationHubs][GitHub].

В этом учебнике описывается простой сценарий вещания с использованием Центров уведомлений.

## <a name="before-you-begin"></a>Перед началом работы
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Полный код для этого учебника можно найти на портале [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid).

## <a name="prerequisites"></a>Предварительные требования
Для работы с данным учебником требуется следующее:

* Visual Studio с расширением Xamarin для Windows или Xamarin Studio для Mac OS X. Инструкции по установке см. в [руководстве по установке и настройке Visual Studio и Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* Активная учетная запись Google
* [Компонент обмена сообщениями в Azure]
* [Компонент клиента Google Cloud Messaging]

Для прохождения других учебников, посвященных Центрам уведомлений для приложений Xamarin.Android, необходимо сначала выполнить действия, описанные в этом учебнике.

> [!IMPORTANT]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-google-cloud-messaging"></a>Включение Google Cloud Messaging
[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Настройка концентратора уведомлений
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="7">

<li><p>Перейдите на расположенную сверху вкладку <b>Настройка</b>, введите значение <b>Ключ API</b>, полученное в предыдущем разделе, а затем нажмите кнопку <b>Сохранить</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)

Концентратор уведомлений теперь настроен для работы с GCM. Также у вас есть строки подключения, с помощью которых вы можете зарегистрировать приложение для получения уведомлений и отправки push-уведомлений.

## <a name="connect-your-app-to-the-notification-hub"></a>Подключение приложения к центру уведомлений
### <a name="create-a-new-project"></a>Создание нового проекта
1. В Xamarin Studio щелкните **New Solution** (Создать решение), **Android App** (Приложение Android) и нажмите кнопку **Next** (Далее).
   
       ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)
2. Введите значения в поля **App Name** (Имя приложения) и **Identifier** (Идентификатор). В разделе **Target Plaforms** (Целевые платформы) выберите поддерживаемые платформы и нажмите кнопки **Next** (Далее) и **Create** (Создать).
   
       ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)

    Будет создан проект Android.

1. Откройте свойства проекта, щелкнув правой кнопкой мыши новый проект в представлении Solution (Решение) и выбрав пункт **Options**(Параметры). В разделе **Build** (Сборка) выберите **Android Application** (Приложение Android).
   
    Убедитесь, что **Имя пакета** начинается со строчной буквы.
   
   > [!IMPORTANT]
   > Первая буква имени пакета должна быть строчной. В противном случае возникнут ошибки манифеста приложения при регистрации **BroadcastReceiver** и **IntentFilter** для push-уведомлений ниже.
   > 
   > 
   
       ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)
2. Или выберите другой уровень API для параметра **Minimum Android version** (Минимальная версия Android).
3. Или для параметра **Target Android version** (Целевая версия Android) выберите другую версию API, которую нужно использовать в качестве целевой (используйте API уровня 8 или выше).

Нажмите кнопку **ОК** и закройте диалоговое окно параметров проекта.

### <a name="add-the-required-components-to-your-project"></a>Добавление в проект необходимых компонентов
Клиент Google Cloud Messaging, доступный в магазине компонентов Xamarin, упрощает процесс поддержки push-уведомлений в Xamarin.Android.

1. Щелкните правой кнопкой мыши папку компонентов в приложении Xamarin.Android и выберите пункт **Get More Components**(Получить дополнительные компоненты).
2. Найдите компонент **Обмен сообщениями Azure** и добавьте его в проект.
3. Найдите компонент **Клиент Google Cloud Messaging** и добавьте его в проект.

### <a name="set-up-notification-hubs-in-your-project"></a>Настройка центров уведомлений в проекте
1. Подготовьте следующую информацию для вашего приложения Android и концентратора уведомлений.
   
   * **GoogleProjectNumber**. Найдите это значение номера проекта на странице своего приложения на портале разработчиков Google. Вы записали это значение ранее, когда создавали приложение на портале.
   * **Прослушивание строки подключения**. На [классическом портале Azure] на панели мониторинга щелкните **Просмотреть строки подключения**. Для этого значения скопируйте строку подключения *DefaultListenSharedAccessSignature*.
   * **Имя концентратора**— это имя вашего концентратора на [классическом портале Azure]. Например, *mynotificationhub2*.
     
     Создайте класс **Constants.cs** для вашего проекта Xamarin и определите в классе следующие постоянные значения. Замените значения заполнителей на собственные значения.
     
       public static class Constants   {
     
           public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
       }
2. Добавьте следующие элементы, используя операторы **MainActivity.cs**:
   
        using Android.Util;
        using Gcm.Client;
3. Добавьте переменную экземпляра в класс `MainActivity` , чтобы отобразить диалоговое окно оповещения при выполнении приложения.
   
        public static MainActivity instance;
4. Создайте следующий метод в классе **MainActivity** :
   
        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);
   
            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }
5. В методе `OnCreate` файла **MainActivity.cs** инициализируйте переменную `instance` и добавьте вызов в `RegisterWithGCM`:
   
        protected override void OnCreate (Bundle bundle)
        {
            instance = this;
   
            base.OnCreate (bundle);
   
            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);
   
            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);
   
            RegisterWithGCM();
        }
6. Создайте класс **MyBroadcastReceiver**.
   
   > [!NOTE]
   > Здесь мы рассмотрим создание класса **BroadcastReceiver** с нуля. Быстрой альтернативой созданию файла **MyBroadcastReceiver.cs** вручную является использование ссылки на файл **GcmService.cs**, находящийся в образце проекта Xamarin.Android вместе с примерами [NotificationHubs][GitHub]. Вы также можете скопировать файл **GcmService.cs** и изменить имена классов.
   > 
   > 
7. Добавьте следующие инструкции using в файл **MyBroadcastReceiver.cs** (ссылки на компонент и сборку, которые были добавлены ранее):
   
        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;
8. В файле **MyBroadcastReceiver.cs** добавьте следующие разрешения запросов между операторами **using** и объявлением **namespace**:
   
        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
   
        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
9. В файле **MyBroadcastReceiver.cs** измените класс **MyBroadcastReceiver** таким образом, чтобы он соответствовал следующему коду:
   
        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };
   
            public const string TAG = "MyBroadcastReceiver-GCM";
        }
10. Добавьте в файл **MyBroadcastReceiver.cs** еще один класс с именем **PushHandlerService**, сделав его производным от **GcmServiceBase**. Примените для класса атрибут **Service** :
    
         [Service] // Must use the service tag
         public class PushHandlerService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }
             private NotificationHub Hub { get; set; }
    
             public PushHandlerService() : base(Constants.SenderID)
                {
                 Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
             }
         }
11. **GcmServiceBase** реализует методы **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** и **OnError()**. Наш класс реализации **PushHandlerService** должен переопределить эти методы, чтобы они срабатывали в ответ на взаимодействие с центром уведомлений.
12. Переопределите метод **OnRegistered()** в классе **PushHandlerService** с помощью следующего кода:
    
         protected override void OnRegistered(Context context, string registrationId)
         {
             Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
             RegistrationID = registrationId;
    
             createNotification("PushHandlerService-GCM Registered...",
                                 "The device has been Registered!");
    
             Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                         context);
             try
             {
                 Hub.UnregisterAll(registrationId);
             }
             catch (Exception ex)
             {
                 Log.Error(MyBroadcastReceiver.TAG, ex.Message);
             }
    
             //var tags = new List<string>() { "falcons" }; // create tags if you want
             var tags = new List<string>() {};
    
             try
             {
                 var hubRegistration = Hub.Register(registrationId, tags.ToArray());
             }
             catch (Exception ex)
             {
                 Log.Error(MyBroadcastReceiver.TAG, ex.Message);
             }
         }
    
    > [!NOTE]
    > В коде **OnRegistered()** выше обратите внимание на возможность задать теги для регистрации на конкретные каналы обмена сообщениями.
    > 
    > 
13. Переопределите метод **OnMessage** в классе **PushHandlerService** с помощью следующего кода:
    
        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");
    
            var msg = new StringBuilder();
    
            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }
    
            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }
14. Добавьте методы **createNotification** и **dialogNotify** к службе **PushHandlerService** для уведомления пользователей при получении уведомления.
    
    > [!NOTE]
    > Структура уведомлений в версии платформы Android 5.0 и более поздних версиях существенно отличается от структуры в предыдущих версиях. Если вы проверяете это на платформе Android версии 5.0 или более поздней версии, необходимо запустить приложение, чтобы получить уведомление. Дополнительные сведения см. в статье об [уведомлениях Android](http://go.microsoft.com/fwlink/?LinkId=615880).
    > 
    > 
    
        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;
    
            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));
    
            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);
    
            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;
    
            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));
    
            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }
    
        protected void dialogNotify(String title, String message)
        {
    
            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }
15. Переопределите абстрактные члены **OnUnRegistered()**, **OnRecoverableError()** и **OnError()** для компиляции кода:
    
        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);
    
            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }
    
        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);
    
            return base.OnRecoverableError (context, errorId);
        }
    
        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

## <a name="run-your-app-in-the-emulator"></a>Запустите ваше приложение в эмуляторе
При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

> [!IMPORTANT]
> Чтобы получать push-уведомления, необходимо настроить учетную запись Google на виртуальном устройстве Google Android (в эмуляторе выберите пункт (В эмуляторе перейдите к пункту **Settings** (Параметры) и нажмите кнопку **Add Account** (Добавить учетную запись).) Кроме того, убедитесь, что эмулятор подключен к Интернету.
> 
> [!NOTE]
> Структура уведомлений в версии платформы Android 5.0 и более поздних версиях существенно отличается от структуры в предыдущих версиях. Дополнительные сведения см. в статье об [уведомлениях Android](http://go.microsoft.com/fwlink/?LinkId=615880).
> 
> 

1. В меню **Tools** (Средства) щелкните **Open Android Emulator Manager** (Открыть диспетчер эмулятора Android), выберите свое устройство и нажмите кнопку **Edit** (Изменить).
   
       ![][18]
2. В разделе **Target** (Цель) выберите **Google APIs** (API-интерфейсы Google), а затем нажмите кнопку **ОК**.
   
       ![][19]
3. На верхней панели инструментов нажмите кнопку **Запуск**и выберите приложение. При этом запускается эмулятор и выполняется приложение.
   
   Приложение получает значение *registrationId* из GCM и регистрируется в концентраторе уведомлений.

## <a name="send-notifications-from-your-backend"></a>Отправка уведомлений из серверной части
Получение уведомлений в приложении можно проверить, отправив уведомления на [классическом портале Azure] на вкладке отладки центра уведомлений, как показано на следующем рисунке.

![][30]

Push-уведомления обычно отправляются в серверной службе, например мобильными службами или ASP.NET, с помощью совместимой библиотеки. Также можно напрямую использовать API REST для отправки уведомлений, если библиотека недоступна для серверной части.

Ниже приведен список других учебников, касающихся отправки уведомлений.

* ASP.NET: см. статью [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET].
* Пакет SDK для Центров уведомлений Azure для Java: сведения об отправке уведомлений Java см. в статье [Использование концентраторов уведомлений из Java](notification-hubs-java-push-notification-tutorial.md). Было протестировано в Eclipse для разработки для Android.
* PHP: см. статью [Использование центров уведомлений в PHP](notification-hubs-php-push-notification-tutorial.md).

В следующих подразделах этого учебника вы отправляете уведомления с помощью консольного приложения .NET, а также с помощью мобильных служб с использованием скрипта узла.

#### <a name="optional-send-notifications-by-using-a-net-app"></a>(Необязательно) Отправка уведомлений с помощью приложения .NET
В этом разделе мы будем отправлять уведомления с помощью консольного приложения .NET.

1. Создайте новое консольное приложение Visual C#.
   
       ![][20]
2. В Visual Studio последовательно выберите элементы **Сервис**, **Диспетчер пакетов NuGet** и **Консоль диспетчера пакетов**.
   
    В результате в Visual Studio откроется консоль диспетчера пакетов.
3. В окне консоли диспетчера пакетов задайте свойство **Проект по умолчанию** для нового проекта консольного приложения, а затем в окне консоли выполните следующую команду:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    После этого будет добавлена ссылка на пакет SDK для Центров уведомлений Azure с помощью <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">пакета NuGet Microsoft.Azure.Notification Hubs</a>.
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
4. Откройте файл Program.cs и добавьте следующий оператор `using` :
   
        using Microsoft.Azure.NotificationHubs;
5. Добавьте в класс `Program` следующий метод: Замените текст заполнителя значением строки подключения *DefaultFullSharedAccessSignature* и именем центра на [классическом портале Azure].
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }
6. Добавьте в метод **Main** следующие строки:
   
         SendNotificationAsync();
         Console.ReadLine();
7. Нажмите клавишу F5, чтобы запустить приложение. Вы получите уведомление в приложении.
   
       ![][21]

#### <a name="optional-send-notifications-by-using-a-mobile-service"></a>(Необязательно) Отправка уведомлений с помощью мобильной службы
1. Выполните действия, описанные в статье [Приступая к работе с мобильными службами].
2. Войдите на [классический портал Azure]и выберите мобильную службу.
3. Перейдите на расположенную вверху вкладку **Планировщик** .
   
       ![][22]
4. Создайте новое запланированное задание, вставьте имя и выберите **По запросу**.
   
       ![][23]
5. Создав задание, щелкните его имя. Откройте вкладку **Скрипт** в верхней панели.
6. Вставьте следующий скрипт внутрь функции планировщика. Обязательно замените заполнители именем концентратора уведомлений и строкой подключения для элемента *DefaultFullSharedAccessSignature* , полученными ранее. Щелкните **Сохранить**.
   
        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );
7. Нажмите кнопку **Запустить один раз** на нижней панели. Вы получите всплывающее уведомление.

## <a name="next-steps"></a>Дальнейшие действия
В этом простом примере осуществляется широковещательная рассылка уведомлений на все устройства Android. Инструкции по их отправке определенным целевым пользователям, см. в руководстве [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании центров уведомлений см. в статьях [Общие сведения о концентраторах уведомлений] и [Руководство по использованию центров уведомлений Azure с приложениями Android].

<!-- Anchors. -->
[Включение Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Подключение приложения к центру уведомлений]: #connecting-app
[Запуск приложения с помощью эмулятора]: #run-app
[Отправка уведомлений из серверной части]: #send
[Дальнейшие действия]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Пакет Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript и HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Классический портал Azure]: https://manage.windowsazure.com/
[Объект wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Общие сведения о концентраторах уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Инструкции по использованию концентраторов уведомлений для Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]: /manage/services/notification-hubs/notify-users-aspnet
[Использование концентраторов уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-dotnet
[Страница компонента GCMClient]: http://components.xamarin.com/view/GCMClient
[Страница Xamarin.NotificationHub на Github]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Компонент клиента Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Компонент обмена сообщениями в Azure]: http://components.xamarin.com/view/azure-messaging



<!--HONumber=Nov16_HO2-->


