---
title: Приступая к работе с проверкой подлинности в Android (серверная часть JavaScript) | Microsoft Docs
description: Узнайте, как использовать мобильные службы для аутентификации пользователей приложения Android с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт (серверная часть JavaScript).
services: mobile-services
documentationcenter: android
author: RickSaling
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 07/21/2016
ms.author: ricksal

---
# Добавление проверки подлинности в приложение мобильных служб для Android (серверная часть JavaScript)
[!INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Аналогичные сведения для мобильных приложений см. в разделе [Добавление проверки подлинности в приложение Android](../app-service-mobile/app-service-mobile-android-get-started-users.md).
> 
> 

## Сводка
В этом разделе показано, как выполнять проверку подлинности пользователей в мобильных службах Azure в приложении. В этом учебнике вы добавите проверку подлинности к проекту быстрого запуска, используя поставщик удостоверений, поддерживаемый мобильными службами. После выполнения успешной проверки подлинности и авторизации мобильными службами отображается значение идентификатора пользователя.

> [!VIDEO android-getting-started-with-authentication-in-windows-azure-mobile-services]
> 
> 

В этом учебнике рассматриваются следующие основные шаги для включения проверки подлинности в приложении:

## Предварительные требования
[!INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## Регистрация приложения для проверки подлинности и настройка мобильных служб
[!INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## Ограничение разрешений для пользователей, прошедших проверку подлинности
[!INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1. В Android Studio откройте проект, созданный в ходе работы с учебником [Приступая к работе с мобильными службами].
2. В меню **Запуск** щелкните **Запуск приложения**; убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния 401 ("Не санкционировано").
   
     Это происходит, потому что приложение пытается получить доступ к мобильным службам как пользователь, не прошедший проверку подлинности, а таблица *TodoItem* теперь требует выполнения проверки подлинности.

Далее приложение будет обновлено таким образом, что оно станет производить аутентификацию учетных данных пользователей, прежде чем запрашивать ресурсы из мобильной службы.

## Добавление проверки подлинности в приложение
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Кэширование маркеров проверки подлинности на клиенте
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="refresh-tokens"></a>Обновление кэша маркеров
[!INCLUDE [mobile-android-authenticate-app-refresh-token](../../includes/mobile-android-authenticate-app-refresh-token.md)]

## <a name="next-steps"></a>Дальнейшие действия
В следующем учебном курсе, который называется [Авторизация пользователей с помощью скриптов], значение ИД пользователя, предоставляемое мобильными службами на основе пользователя, прошедшего проверку подлинности, будет использоваться для фильтрации данных, возвращаемых мобильными службами.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: mobile-services-android-get-started.md
[Авторизация пользователей с помощью скриптов]: mobile-services-javascript-backend-service-side-authorization.md

<!---HONumber=AcomDC_0727_2016-->