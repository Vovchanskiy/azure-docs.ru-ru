---
title: Работа с приложениями, поддерживающими утверждения, в прокси приложения
description: Описывается, как настроить и запустить прокси приложения Azure AD.
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
# Работа с приложениями, поддерживающими утверждения, в прокси приложения
Приложения, поддерживающие утверждения, выполняют перенаправление в службу маркеров безопасности (STS), которая, в свою очередь, запрашивает учетные данные пользователя в обмен на маркер, прежде чем перенаправить пользователя в приложение. Чтобы включить прокси приложения для работы с этими перенаправлениями, необходимо выполнить следующие действия.

## Предварительные требования
Перед выполнением этой процедуры убедитесь, что служба маркеров безопасности, в которую перенаправляет пользователя приложение, поддерживающее утверждения, доступна за пределами вашей локальной сети.

## Настройка на классическом портале Azure
1. Опубликуйте приложение в соответствии с инструкциями, описанными в статье [Публикация приложений с помощью прокси приложения](active-directory-application-proxy-publish.md).
2. В списке приложений выберите приложение, поддерживающее утверждения, и щелкните **Настроить**.
3. Если для параметра **Метод предварительной проверки подлинности** выбрано значение **Сквозной режим**, не забудьте выбрать значение **HTTPS** для схемы **Внешний URL-адрес**.
4. Если в поле **Метод предварительной проверки подлинности** выбрано значение **Azure Active Directory**, выберите для параметра **Метод внутренней проверки подлинности** значение **Нет**.

## Настройка в службах AD FS
1. Откройте оснастку управления AD FS.
2. Перейдите в папку **Relying Party Trusts** (Отношения доверия проверяющей стороны), щелкните правой кнопкой мыши приложение, которое публикуется с помощью прокси приложения, и выберите **Свойства**. ![Щелкните правой кнопкой мыши имя приложения в папке «Отношения доверия проверяющей стороны» (снимок экрана)](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. На вкладке **Конечные точки** в поле **Тип конечной точки** выберите **WS-Federation**.
4. В поле **Trusted URL** (Доверенный URL-адрес) введите URL-адрес, который вы указали в прокси приложении в поле **Внешний URL-адрес**, и нажмите кнопку **ОК**. ![Добавление конечной точки: задайте значение в поле «Доверенный URL-адрес» (снимок экрана)](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## См. также
* [Опубликуйте приложения с помощью прокси-сервера приложений.](active-directory-application-proxy-publish.md)
* [Включение единого входа](active-directory-application-proxy-sso-using-kcd.md)
* [Устранение неполадок с прокси приложения](active-directory-application-proxy-troubleshoot.md)
* [Включение собственных клиентских приложений для взаимодействия с приложениями прокси](active-directory-application-proxy-native-client.md)

Последние новости и обновления см. в [блоге о прокси приложения](http://blogs.technet.com/b/applicationproxyblog/).

<!---HONumber=AcomDC_0622_2016-->