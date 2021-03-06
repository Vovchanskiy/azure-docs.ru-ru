---
title: Рассылка push-уведомлений проверенным пользователям (серверная часть .NET)
description: Узнайте, как отправлять push-уведомления для конкретного адресата
services: mobile-services,notification-hubs
documentationcenter: ios
author: krisragh
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/21/2016
ms.author: krisragh

---
# Рассылка push-уведомлений проверенным пользователям
[!INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Аналогичные сведения для мобильных приложений см. в статье [Практическое руководство. Отправка push-уведомлений пользователю, прошедшему проверку подлинности](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#push-user).
> 
> 

В этом разделе показано, как отправлять push-уведомления пользователю, прошедшему проверку подлинности, на iOS-устройстве. Перед этим учебником сперва выполните задания учебников [Приступая к работе с проверкой подлинности] и [Приступая к работе с push-уведомления].

В этом учебнике вы узнаете, как требовать от пользователей проверку подлинности, регистрироваться в концентраторе уведомлений для рассылки push-уведомлений и обновлять серверные скрипты для отправки этих уведомлений только пользователям, прошедшим проверку подлинности.

## <a name="register"></a>Обновление службы с учетом требования аутентификации для регистрации
[!INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

## <a name="update-app"></a>Обновление приложения для выполнения входа перед регистрацией
[!INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

## <a name="test"></a>Тестирование приложения
[!INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Приступая к работе с проверкой подлинности]: mobile-services-dotnet-backend-ios-get-started-users.md
[Приступая к работе с push-уведомления]: mobile-services-dotnet-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!---HONumber=AcomDC_0727_2016-->