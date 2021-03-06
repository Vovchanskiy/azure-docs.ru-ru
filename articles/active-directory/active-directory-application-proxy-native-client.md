---
title: Включение собственных клиентских приложений для взаимодействия с приложениями прокси | Microsoft Docs
description: В этой статье описано, как включить собственные клиентские приложения для взаимодействия с соединителем прокси приложений Azure AD, чтобы обеспечить безопасный удаленный доступ к локальным приложениям.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban

---
# Включение собственных клиентских приложений для взаимодействия с приложениями прокси
Прокси приложений Azure Active Directory часто используется для публикации приложений браузера, например SharePoint, Outlook Web Access и пользовательских бизнес-приложений. Он также может использоваться для публикации собственных клиентских приложений, отличающихся от веб-приложений тем, что они устанавливаются на устройстве. Это реализуется с помощью выданных маркеров Azure AD, отправляемых в стандартные заголовки HTTP авторизации.

![Связь между конечными пользователями, Azure Active Directory и опубликованными приложениями](./media/active-directory-application-proxy-native-client/richclientflow.png)

Для публикации таких приложений рекомендуется использовать библиотеку проверки подлинности Azure AD, которая автоматически выполняет проверку подлинности и поддерживает множество различных клиентских сред. Прокси приложения вписывается в [сценарий вызова веб-API собственным приложением](active-directory-authentication-scenarios.md#native-application-to-web-api). Чтобы этого добиться, выполните следующие действия.

## Шаг 1. Публикация приложения
Опубликуйте приложение прокси, как любое другое приложение, назначьте пользователей и предоставьте им лицензии категории «Премиум» или «Базовый». Дополнительные сведения см. в статье [Публикация приложений с помощью прокси приложения Azure AD](active-directory-application-proxy-publish.md).

## Шаг 2. Настройка приложения
Настройте собственное приложение, следуя инструкциям ниже.

1. Войдите на классический портал Azure.
2. Щелкните значок Active Directory в левом меню, а затем выберите нужный каталог.
3. В верхнем меню щелкните **Приложения**. Если в каталоге нет добавленных приложений, на этой странице отображается только ссылка **Добавить приложение**. Щелкните ссылку или нажмите кнопку **Добавить** на панели команд.
4. На странице **Что необходимо сделать?** щелкните ссылку **Добавить приложение, разрабатываемое моей организацией**.
5. На странице **Расскажите о своем приложении** укажите имя приложения и выберите **Собственное клиентское приложение**. Для продолжения щелкните значок стрелки.
6. На странице **Сведения о приложении** введите **URI перенаправления** для собственного клиентского приложения, а затем щелкните кнопку с галочкой для завершения.

Приложение добавляется в систему, и выполняется переход на страницу быстрого запуска для вашего приложения.

## Шаг 3. Предоставление доступа для других приложений
Включите собственное приложение, которое должно быть доступно для других приложений в каталоге.

1. В верхнем меню щелкните элемент **Приложения**, выберите новое собственное приложение и нажмите кнопку **Настроить**.
2. Прокрутите вниз до раздела **Разрешения для других приложений**. Нажмите кнопку **Добавить приложение**, выберите приложение прокси, к которому нужно предоставить доступ в собственном приложении, и установите флажок в нижнем правом углу. В раскрывающемся меню **Делегированные разрешения** выберите новое разрешение.

![Снимок экрана "Разрешения для других приложений" — добавление приложения](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## Шаг 4. Изменение библиотеки проверки подлинности Active Directory
Измените код собственного приложения с учетом проверки подлинности с помощью библиотеки Active Directory Authentication Library (ADAL). Для этого включите в код следующее:

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Переменные должны быть заменены следующим образом.

* **TenantId** находится в GUID URL-адреса приложения на странице **Конфигурация** сразу после элемента /Directory/.
* **Frontend URL** — это URL-адрес внешнего интерфейса, который вы ввели в приложение прокси. Он находится на странице **Конфигурация** приложения прокси.
* **Client Id** — это собственное приложение, которое находится на странице **Настройка** собственного приложения.
* **URI перенаправления собственного приложения** находится на странице **Настройка** собственного приложения.

![Снимок экрана страницы настройки нового нативного приложения](./media/active-directory-application-proxy-native-client/new_native_app.png)

Дополнительные сведения о потоке собственного приложения см. в разделе [Из нативного приложения к веб-интерфейсу API](active-directory-authentication-scenarios.md#native-application-to-web-api).

## См. также
* [Публикация приложений с помощью доменного имени](active-directory-application-proxy-custom-domains.md)
* [Включение условного доступа](active-directory-application-proxy-conditional-access.md)
* [Работа с приложениями, поддерживающими утверждения](active-directory-application-proxy-claims-aware-apps.md)
* [Включение единого входа](active-directory-application-proxy-sso-using-kcd.md)

Последние новости и обновления см. в [блоге о прокси приложения](http://blogs.technet.com/b/applicationproxyblog/).

<!---HONumber=AcomDC_0622_2016-->