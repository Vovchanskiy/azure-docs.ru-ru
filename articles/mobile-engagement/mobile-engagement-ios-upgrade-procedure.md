---
title: 'Службы Мобильного Взаимодействия Azure: процедура обновления пакета SDK для iOS | Microsoft Docs'
description: Последние обновления и указания для пакета SDK для iOS для Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 09/14/2016
ms.author: piyushjo

---
# <a name="upgrade-procedures"></a>Процедуры обновления
Если вы уже интегрировали в приложение старую версию службы Engagement, при обновлении пакета SDK необходимо учитывать следующее.

Для каждой новой версии пакета SDK необходимо сначала заменить (удалить и повторно импортировать в проект Хcode) папки EngagementSDK и EngagementReach.

## <a name="from-3.0.0-to-4.0.0"></a>С версии 3.0.0 до версии 4.0.0
### <a name="xcode-8"></a>XCode 8
Версия XCode 8 является обязательной, начиная с пакета SDK версии 4.0.0.

> [!NOTE]
> Если вам крайне важно оставить версию XCode 7, то можете воспользоваться [пакетом SDK Служб взаимодействия для iOS версии 3.2.4](https://aka.ms/r6oouh). В предыдущей версии на устройствах iOS 10 выявлена ошибка в модуле обработки рекламных кампаний: не приводятся в действие системные уведомления. Для исправления этой ошибки понадобится реализовать в делегате приложения устаревшую версию API `application:didReceiveRemoteNotification:` :
> 
> 

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **Данный способ не является рекомендуемым** , так как это поведение может измениться с любым последующим (даже незначительным) обновлением версии iOS из-за того, что этот интерфейс API для iOS устарел. Рекомендуется перейти на XCode 8 как можно скорее.
> 
> 

### <a name="usernotifications-framework"></a>Платформа UserNotifications
В этапы сборки необходимо добавить платформу `UserNotifications` .

В обозревателе проектов откройте область проектов и выберите правильную цель. Затем откройте вкладку **Build phases** (Этапы сборки) и в меню **Link Binary With Libraries** (Связать двоичные объекты с библиотеками) добавьте платформу `UserNotifications.framework`, задав значение ссылки `Optional`.

### <a name="application-push-capability"></a>Функция push-уведомлений приложения
XCode 8 может выполнить сброс настроек push-уведомлений приложения. Проверьте настройки этой функции на вкладке `capability` выбранной цели.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Добавление нового кода регистрации уведомления в iOS 10
Старый фрагмент кода для регистрации приложения для получения уведомлений по-прежнему функционирует, но он использует устаревшие интерфейсы API при работе в iOS 10.

Импортируйте платформу `User Notification` :

        #import <UserNotifications/UserNotifications.h> 

В методе `application:didFinishLaunchingWithOptions` делегата приложения замените:

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
        [application registerForRemoteNotifications];
    }
    else {

        [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
    }

на:

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Если у вас уже есть собственная реализация UNUserNotificationCenterDelegate
Пакет SDK также имеет собственную реализацию протокола UNUserNotificationCenterDelegate. Он используется пакетом SDK для отслеживания жизненного цикла уведомлений Служб Взаимодействия на устройствах, работающих под управлением iOS 10 или более поздней версии. Если пакет SDK обнаружит ваш делегат, он не будет применять собственную реализацию, так как в приложении может быть только один делегат UNUserNotificationCenter. Это означает, что вам потребуется добавить логику Служб Взаимодействия в собственный делегат.

Это достигается двумя способами.

Просто переадресуйте вызовы делегата в пакет SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Или воспользуйтесь наследованием из класса `AEUserNotificationHandler`:

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> Можно определить, поступают ли уведомления из Служб взаимодействия, передав словарь `userInfo` в метод класса `isEngagementPushPayload:` агента.
> 
> 

## <a name="from-2.0.0-to-3.0.0"></a>Из версии 2.0.0 в 3.0.0
Прекращена поддержка iOS 4.X. Начиная с этой версии, целевое устройство для развертывания приложения должно быть с версией не ниже iOS 6.

Если в приложении используется обработка рекламных кампаний, необходимо добавить значение `remote-notification` в массив `UIBackgroundModes` в файле Info.plist для получения удаленных уведомлений.

Метод `application:didReceiveRemoteNotification:` необходимо заменить на `application:didReceiveRemoteNotification:fetchCompletionHandler:` в делегате приложения.

"AEPushDelegate.h" — нерекомендуемый интерфейс, поэтому все ссылки на него необходимо удалить. Это, в частности, подразумевает удаление `[[EngagementAgent shared] setPushDelegate:self]` и методов делегата в делегате приложения:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

## <a name="from-1.16.0-to-2.0.0"></a>От версии 1.16.0 до 2.0.0
Ниже описан процесс переноса интеграции пакета SDK из службы Capptain от Capptain SAS в приложение на платформе Azure Mobile Engagement.
При миграции с более ранней версии сначала выполните перенос на версию 1.16, как указано на веб-сайте Capptain, а затем примените следующую процедуру.

> [!IMPORTANT]
> Службы Capptain и Azure Mobile Engagement отличаются между собой. В представленных ниже процедурах описывается способ переноса только для клиентского приложения. При переносе пакета SDK в приложение данные НЕ будут перенесены с серверов Capptain на серверы Mobile Engagement.
> 
> 

### <a name="agent"></a>Агент
Метод `registerApp:` заменен новым методом `init:`. Следует обновить делегат приложения соответствующим образом и использовать строку подключения:

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

Из пакета SDK было удалено средство отслеживания SmartAd, поэтому нужно просто удалить все экземпляры класса `AETrackModule` .

### <a name="class-name-changes"></a>Изменение имени класса
Как часть ребрендинга следует изменить имена нескольких классов и файлов.

Во всех классах префикс CP нужно заменить префиксом AE.

Пример:

* `CPModule.h` переименовывается в `AEModule.h`.

Во всех классах префикс Capptain нужно заменить префиксом Engagement.

Примеры:

* Класс `CapptainAgent` переименовывается в `EngagementAgent`.
* Класс `CapptainTableViewController` переименовывается в `EngagementTableViewController`.
* Класс `CapptainUtils` переименовывается в `EngagementUtils`.
* Класс `CapptainViewController` переименовывается в `EngagementViewController`.

<!--HONumber=Oct16_HO2-->


