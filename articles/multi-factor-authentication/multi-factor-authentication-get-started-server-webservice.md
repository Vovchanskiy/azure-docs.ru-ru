---
title: "Приступая к работе с веб-службой мобильного приложения сервера Azure Multi-Factor Authentication"
description: "Приложение Azure Multi-Factor Authentication предлагает возможность дополнительной проверки подлинности по внештатному каналу.  Это позволяет серверу Azure Multi-Factor Authentication отправлять push-уведомления пользователям."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 883b3a27b4b06785068d04a143bae6c33fbed99b


---
# <a name="getting-started-the-mfa-server-mobile-app-web-service"></a>Приступая к работе с веб-службой мобильного приложения сервера Azure Multi-Factor Authentication
Приложение Azure Multi-Factor Authentication предлагает возможность дополнительной проверки подлинности по внештатному каналу. Вместо автоматического телефонного вызова или отправки SMS пользователю при входе в систему служба Azure Multi-Factor Authentication отправляет push-уведомление приложению Azure Multi-Factor Authentication, которое находится на смартфоне или планшете пользователя. Чтобы войти, пользователю в приложении нужно просто коснуться элемента «Проверить подлинность» (или ввести ПИН-код и коснуться элемента «Проверить подлинность»).

Чтобы использовать приложение Azure Multi-Factor Authentication и чтобы оно успешно взаимодействовало с веб-службой мобильного приложения, необходимо выполнить такие условия:

* Требования к оборудованию и программному обеспечению см. в разделе «Требования к оборудованию и программному обеспечению».
* Необходимо использовать версию 6.0 или более позднюю версию сервера Azure Multi-Factor Authentication.
* Необходимо установить веб-службу мобильного приложения на веб-сервере с выходом в Интернет под управлением Microsoft® Internet Information Services (IIS) 7.x или выше.  Дополнительные сведения см. в документации по [IIS.NET](http://www.iis.net/).
* Убедитесь, что версия ASP.NET 4.0.30319 установлена и зарегистрирована и для нее установлено значение "Разрешено".
* К обязательным службам роли относятся ASP.NET и совместимость с метабазой IIS 6.
* Веб-служба мобильного приложения должна быть доступна через общедоступный URL-адрес.
* Веб-служба мобильного приложения должна быть защищена сертификатом SSL.
* Требуется установить пакет SDK веб-службы Azure Multi-Factor Authentication для версии IIS 7.x или выше на сервере Azure Multi-Factor Authentication.
* Безопасность пакета SDK веб-службы Azure Multi-Factor Authentication должен обеспечивать сертификат SSL.
* Веб-служба мобильного приложения должна иметь возможность подключения к пакету SDK веб-службы Azure Multi-Factor Authentication по протоколу SSL.
* Веб-служба мобильного приложения должна иметь возможность выполнить проверку подлинности для пакета SDK веб-службы Azure Multi-Factor Authentication с использованием учетных данных учетной записи службы, которая входит в группу безопасности PhoneFactor Admins. Эта учетная запись службы и группа существуют в Active Directory, если сервер Azure Multi-Factor Authentication установлен на сервере, присоединенном к домену. Эта учетная запись службы и группа существуют локально на сервере Azure Multi-Factor Authentication, если он не присоединен к домену.

Чтобы установить пользовательский портал на сервере, отличном от сервера Azure Multi-Factor Authentication, нужно выполнить такие три действия:

1. Установка пакета SDK веб-службы
2. Установить веб-службу мобильного приложения.
3. Настроить параметры мобильного приложения на сервере Azure Multi-Factor Authentication.
4. Активировать приложение Azure Multi-Factor Authentication для конечных пользователей.

## <a name="install-the-web-service-sdk"></a>Установка пакета SDK веб-службы
Если пакет SDK веб-службы Azure Multi-Factor Authentication еще не установлен на сервере Azure Multi-Factor Authentication, перейдите на этот сервер и откройте сервер Azure Multi-Factor Authentication. Щелкните значок "SDK веб-службы", нажмите кнопку "Установить SDK веб-службы" и следуйте инструкциям на экране. Пакет SDK веб-службы должен быть защищен сертификатом SSL. Для этой цели подходит самозаверяющий сертификат, но он должен быть импортирован в хранилище «Доверенные корневые центры сертификации» учетной записи локального компьютера на веб-сервере пользовательского портала, чтобы этот сертификат был доверенным при запуске SSL-подключения.

<center>![Настройка](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## <a name="install-the-mobile-app-web-service"></a>Установить веб-службу мобильного приложения.
Перед установкой веб-службы мобильного приложения необходимо учитывать такие факторы:

* Если пользовательский портал Azure Multi-Factor Authentication уже установлен на сервере с выходом в Интернет, то имя пользователя, пароль и URL-адрес пакета SDK веб-службы можно скопировать из файла web.config на пользовательском портале.
* Рекомендуется открыть веб-браузер на веб-сервере с выходом в Интернет и перейти по URL-адресу пакета SDK веб-службы, указанному в файле web.config. Если веб-служба успешно откроется в браузере, у вас будут запрошены учетные данные для входа. Введите имя пользователя и пароль, которые были введены в файл web.config, в точности так, как они отображены в файле. Убедитесь, что не отображаются предупреждения или ошибки сертификата.
* Если обратный прокси-сервер или брандмауэр размещен перед веб-сервером веб-службы мобильного приложения и выполняет разгрузку SSL, вы можете изменить файл web.config веб-сервиса мобильного приложения и добавить следующий ключ в раздел <appSettings>, чтобы веб-служба мобильного приложения могла использовать протокол http вместо https. Это позволит пользовательскому порталу использовать протокол http вместо https. <add key="SSL_REQUIRED" value="false"/>

### <a name="to-install-the-mobile-app-web-service"></a>Установка веб-службы мобильного приложения
<ol>

<li>Откройте проводник на сервере, на котором установлен сервер Azure Multi-Factor Authentication, и перейдите в папку установки сервера Azure Multi-Factor Authentication (например, C:\Program Files\Azure Multi-Factor Authentication). Выберите соответствующий файл установки AuthenticationPhoneAppWebServiceSetup службы Azure Multi-Factor Authentication для 32- или 64-разрядной версии сервера, на который будет установлена веб-служба мобильного приложения. Скопируйте файл установки на сервер с выходом в Интернет.</li>

<li>На веб-сервере с выходом в Интернет файл установки необходимо запускать с правами администратора. Самый простой способ сделать это — открыть командную строку от имени администратора и перейти в расположение, в которое был скопирован файл установки.</li>  

<li>Запустите файл установки Multi-Factor AuthenticationMobileAppWebServiceSetup и при необходимости измените сайт и замените имя виртуального каталога коротким именем, например «PA». Рекомендуется использовать короткое имя виртуального каталога, так как пользователи должны вводить URL-адрес веб-службы мобильного приложения в мобильное устройство во время активации.</li>

<li>После завершения установки файла Azure Multi-Factor AuthenticationMobileAppWebServiceSetup перейдите в расположение C:\inetpub\wwwroot\PA (или в соответствующий каталог на основе имени виртуального каталога) и измените файл web.config.</li>  

<li>Найдите ключи WEB_SERVICE_SDK_AUTHENTICATION_USERNAME и WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD и задайте значения имени пользователя и пароля для учетной записи службы, которая входит в группу безопасности PhoneFactor Admins (см. раздел требований выше). Это может быть та же учетная запись, которая используется как удостоверение на пользовательском портале Azure Multi-Factor Authentication (если этот портал был установлен заранее). Обязательно поместите имя пользователя и пароль в кавычки в конце строки (значение = "" / >). Рекомендуется использовать полное имя пользователя (например, домен\имя_пользователя или компьютер\имя_пользователя).</li>  

<li>Найдите параметр pfMobile App Web Service_pfwssdk_PfWsSdk и измените его значение, указав вместо ссылки http://localhost:4898/PfWsSdk.asmx URL-адрес пакета SDK веб-службы, выполняющегося на том же сервере, на котором установлен сервер Azure Multi-Factor Authentication (например, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Так как для этого подключения используется протокол SSL, необходимо ссылаться на пакет SDK веб-службы по имени сервера, а не по IP-адресу, потому что SSL-сертификат выдан для имени сервера и URL-адрес должен соответствовать имени в сертификате. Если имя сервера не связано с IP-адресом на сервере с выходом в Интернет, добавьте запись в файл hosts на этом сервере, чтобы сопоставить имя сервера Azure Multi-Factor Authentication и его IP-адрес. Сохраните файл web.config после внесения изменений.</li>  

<li>Если веб-сайт, с которого выполнялась установка пользовательского портала (например, веб-сайт по умолчанию), еще не связан с публично подписанным сертификатом, установите сертификат на сервере, если он на нем еще не установлен, затем откройте диспетчер IIS и привяжите сертификат к веб-сайту.</li>  

<li>Откройте веб-браузер на любом компьютере и перейдите по URL-адресу на страницу, с которой была установлена веб-служба мобильного приложения (например, https://www.publicwebsite.com/PA). Убедитесь, что не отображаются предупреждения или ошибки сертификата.</li>

### <a name="configure-the-mobile-app-settings-in-the-azure-multifactor-authentication-server"></a>Настроить параметры мобильного приложения на сервере Azure Multi-Factor Authentication.
Теперь, когда веб-служба мобильного приложения установлена, необходимо настроить сервер Azure Multi-Factor Authentication для работы с порталом.

#### <a name="to-configure-the-mobile-app-settings-in-the-azure-multifactor-authentication-server"></a>Настройка параметров мобильного приложения на сервере Azure Multi-Factor Authentication
1. На сервере Azure Multi-Factor Authentication щелкните значок пользовательского портала. Если пользователям разрешено управлять своими методами проверки подлинности, на вкладке «Параметры» в группе «Разрешить пользователям выбирать метод» установите флажок «Мобильное приложение». Если не включить эту функцию, конечным пользователям придется обращаться в вашу службу поддержки для завершения активации мобильного приложения.
2. Установите флажок «Разрешить пользователям активировать мобильное приложение».
3. Установите флажок «Разрешить регистрацию пользователей».
4. Щелкните значок мобильного приложения.
5. Введите URL-адрес, который используется с виртуальным каталогом, созданным при установке файла Azure Multi-Factor AuthenticationMobileAppWebServiceSetup. В соответствующем поле можно ввести имя учетной записи. Это название организации будет отображаться в мобильном приложении. Если оставить это поле пустым, в нем будет отображаться имя поставщика Multi-Factor Authentication, созданного на портале управления Azure.

<center>![Настройка](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>



<!--HONumber=Nov16_HO2-->


