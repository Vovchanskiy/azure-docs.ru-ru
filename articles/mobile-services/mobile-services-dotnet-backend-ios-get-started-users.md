---
title: Добавление проверки подлинности в существующее приложение мобильных служб Azure (iOS) | Microsoft Docs
description: Узнайте, как использовать мобильные службы для аутентификации пользователей приложения iOS с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт.
services: mobile-services
documentationcenter: ios
author: krisragh
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/21/2016
ms.author: krisragh

---
# Добавление проверки подлинности в существующее приложение мобильных служб Azure
[!INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Аналогичные сведения для мобильных приложений см. в разделе [Добавление проверки подлинности в приложение iOS](../app-service-mobile/app-service-mobile-ios-get-started-users.md).
> 
> 

В этом учебнике вы добавляете проверку подлинности в проект быстрого запуска с помощью поддерживаемого поставщика удостоверений. Этот учебник создан на основе [учебника по быстрому запуску мобильных служб], который необходимо изучить в первую очередь.

## <a name="register"></a>Регистрация приложения для проверки подлинности и настройка мобильных служб
[!INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[!INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности
[!INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

В Xcode откройте проект. Нажмите кнопку **Выполнить**, чтобы открыть приложение. Убедитесь, что после запуска приложения возникло исключение с кодом состояния 401 (неавторизованный доступ). Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения проверки подлинности.

## <a name="add-authentication"></a>Добавление проверки подлинности в приложение
[!INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

## <a name="store-authentication"></a>Сохранение маркеров проверки подлинности в приложении
[!INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Дальнейшие действия
В следующем учебнике, который называется [Авторизация пользователей мобильных служб на стороне службы], вы воспользуетесь идентификатором пользователя для фильтрации возвращаемых данных.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- URLs. -->
[Авторизация пользователей мобильных служб на стороне службы]: mobile-services-dotnet-backend-service-side-authorization.md
[учебника по быстрому запуску мобильных служб]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-service-side-authorization.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0727_2016-->