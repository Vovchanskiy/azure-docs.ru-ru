---
title: Общие сведения о панели доступа | Microsoft Docs
description: Сведения об использовании различных видов панели доступа (веб-браузер, приложение Android, приложения iPhone и iPad) для доступа к приложениям SaaS, назначенных вам.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: markusvi

---
# <a name="introduction-to-the-access-panel"></a>Общие сведения о панели доступа
Панель доступа представляет собой веб-портал, который позволяет пользователям с учетной записью организации в Azure Active Directory просматривать и запускать облачные приложения, к которым администратор Azure AD предоставил доступ. Если вы являетесь пользователем одного из выпусков Azure Active Directory, вы также можете использовать возможности самообслуживания для управления группами с помощью панели доступа. <br>
 Панель доступа отделена от портала управления Azure, для нее не требуется подписка Azure. 

![Панель доступа][1] 

Панель доступа позволяет пользователям редактировать некоторые настройки профиля, включая следующие возможности:

* изменение пароля, связанного с учетной записью организации;
* изменение параметров сброса пароля;
* изменение настроек контактов и предпочтений, связанных с многофакторной проверкой подлинности (для учетных записей, для которых ее использование настроено администратором);
* просмотр сведений учетной записи, например идентификатора пользователя, дополнительного адреса электронной почты, номеров мобильного и рабочего телефонов;
* просмотр и запуск облачных приложений, к которым вам предоставлен доступ администратором Azure AD. Дополнительные сведения о панели доступа для конечных пользователей см. в разделе [Использование панели доступа](https://msdn.microsoft.com/library/azure/dn756411.aspx).
* Самостоятельное управление группами. В частности, можно создавать группы безопасности и управлять ими, а также запрашивать членство в группах безопасности в Azure AD. Дополнительные сведения можно найти в разделах [Самостоятельное управление группами для пользователей в Azure AD](active-directory-accessmanagement-self-service-group-management.md) и [Управление группами](active-directory-manage-groups.md). 

## <a name="accessing-the-access-panel"></a>Вход на панель доступа
Чтобы открыть панель доступа, введите следующий URL-адрес в веб-браузере:  <br> 
**http://myapps.microsoft.com**

Если для страницы входа настроена пользовательская фирменная символика, ее можно загружать по умолчанию, добавив домен своей организации в конец URL-адреса:  <br> 
**http://myapps.microsoft.com/contosobuild.com**

В этом случае может использоваться любое активное или проверенное доменное имя, настроенное на вкладке «Домены» вашего каталога на портале управления Azure, как показано на снимке экрана ниже.

![Wingtip toys][2]  

Этот URL-адрес должен быть распространен среди всех пользователей, которые будут входить в приложения, интегрированные с Azure AD.

## <a name="authentication"></a>Аутентификация
Чтобы открыть панель доступа, пользователь должен пройти проверку подлинности с помощью учетной записи организации Azure AD. <br>
 Пользователь может пройти проверку подлинности в Azure AD напрямую. <br>
 Если в организации настроена служба федерации, использующая ADFS или другие технологии, проверку подлинности пользователей можно выполнять с помощью Windows Server Active Directory.

Если у пользователя есть подписка на Azure или Office 365 и он использует портал управления Azure или приложение Office 365, ему будет предоставлен список приложений, не требующих повторного входа. Пользователям, которые не прошли проверку подлинности, будет предложено войти с помощью имени пользователя и пароля их учетной записи в Azure AD. Если в организации настроена служба федерации, достаточно ввести имя пользователя.

После проверки подлинности пользователи смогут взаимодействовать с приложениями, интегрированными в каталог администратором. Чтобы узнать, как интегрировать приложения с Azure AD, ознакомьтесь со статьей [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Требования к веб-браузеру
Как минимум для работы с панелью доступа требуется браузер с включенной поддержкой JavaScript и CSS. Чтобы пользователь мог входить в приложения с использованием единого входа с помощью пароля, в браузере необходимо установить расширение «Панель доступа». Это расширение автоматически загружается при выборе пользователем приложения, настроенного на работу с единым входом с помощью пароля.

Сейчас есть расширение «Панель доступа» для браузеров Internet Explorer 8 и более поздних версий, а также Chrome и Firefox.

## <a name="mobile-app-support"></a>Поддержка мобильных приложений
Чтобы входить в приложения, использующие единый вход с помощью пароля, на устройствах iOS и Android, пользователям нужно установить мобильное приложение My Apps, опубликованное группой Azure Active Directory.

### <a name="my-apps-for-android"></a>My Apps для Android
My Apps для Android поддерживается на всех устройствах Android под управлением Android версии 4.1 и выше. Сейчас это приложение доступно в [магазине Google Play](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![My apps][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>My Apps для iPhone и iPad
My Apps для iOS поддерживается на всех устройствах iPhone и iPad под управлением iOS версии 7 и выше. Сейчас это приложение доступно в Apple App Store.

![Профиль приложений][4]    

> [!NOTE]
> В приложения, поддерживающие федерацию с Azure AD (включая Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 и более 70 других), можно входить практически в любом веб-браузере на любом устройстве без подключаемого модуля или мобильного приложения. Для остальных компонентов панели доступа на веб-сайте [https://myapps.microsoft.com](https://myapps.microsoft.com/) также не требуется мобильное приложение My Apps на мобильном устройстве.
> 
> 

## <a name="tips-for-testing-the-end-user-experience"></a>Советы по тестированию работы пользователей
Если вы являетесь администратором Azure и вошли на портал управления Azure, используя учетную запись каталога, вы автоматически войдете в панель доступа со своей текущей учетной записью администратора. В этом случае вы увидите все приложения, назначенные этой учетной записи.

**Чтобы протестировать работу с другой учетной записью пользователя, выполните следующие действия.**

1. Щелкните меню пользователя в правом верхнем углу портала Azure или на панели доступа и выберите**Выход**. Вы выйдете из Azure AD.
2. Перейдите на панель доступа по адресу **http://myapps.microsoft.com**.
3. На странице входа введите имя пользователя и пароль учетной записи в вашем каталоге, которую требуется протестировать.

## <a name="launching-applications"></a>Запуск приложений
На панели доступа может находиться несколько типов приложений.

### <a name="office-365-applications"></a>Приложения Office 365
Если в организации используются приложения Office 365, и у пользователя есть лицензия на их использование, эти приложения будут отображаться на панели доступа пользователя.

Когда пользователь щелкает плитку приложения Office 365, он перенаправляется к этому приложению и автоматически входит в систему.

### <a name="microsoft-and-thirdparty-applications-configured-with-federationbased-sso"></a>Приложения Майкрософт и сторонние приложения с единым входом на основе федерации
Это приложения, добавленные администратором в раздел Active Directory на портале управления Azure, с режимом единого входа*Единый вход Azure AD*. Пользователь будет видеть эти приложения только в случае, если администратор явно предоставил доступ к ним.

Когда пользователь щелкает плитку одного из приложений, он перенаправляется к этому приложению и автоматически входит в систему.

### <a name="passwordbased-sso-without-identity-provisioning"></a>Единый вход по паролю без предоставления идентификатора
Это приложения, добавленные администратором в раздел Active Directory на портале управления Azure, с режимом единого входа*Единый вход с помощью пароля*. <br>  Все пользователи в каталоге будут видеть все приложения, настроенные в этом режиме.

Когда пользователь впервые щелкнет плитку одного из этих приложений, отобразится запрос на установку подключаемого модуля единого входа с помощью пароля для Internet Explorer или Chrome. После этого может потребоваться перезапустить веб-браузер. Когда пользователь вернется на панель доступа и щелкнет плитку приложения снова, появится запрос имени пользователя и пароля для приложения. После ввода имени пользователя и пароля эти учетные данные будут надежно сохранены в Azure AD и связаны с соответствующей учетной записью в Azure AD, и панель доступа будет автоматически выполнять вход пользователя в приложение с использованием этих учетных данных.

После следующего щелчка плитки приложения пользователь автоматически войдет в приложение без необходимости повторного ввода учетных данных и повторной установки подключаемого модуля единого входа с помощью пароля.

Если учетные данные пользователя изменились в целевом стороннем приложении, пользователь также должен обновить свои учетные данные, которые хранятся в Azure AD. Для обновления учетных данных пользователю нужно щелкнуть значок справа внизу на плитке приложения и выбрать «обновить учетные данные», чтобы повторно ввести имя пользователя и пароль для этого приложения.

### <a name="passwordbased-sso-with-identity-provisioning"></a>Единый вход с помощью пароля с предоставлением идентификатора
Это приложения, добавленные администратором в раздел Active Directory на портале управления Azure, с режимом единого входа*Единый вход с помощью пароля*, а также предоставлением идентификатора.

Когда пользователь впервые щелкнет плитку одного из этих приложений, отобразится запрос на установку подключаемого модуля единого входа с помощью пароля для Internet Explorer или Chrome. После этого может потребоваться перезапустить веб-браузер. Когда пользователь вернется на панель доступа и щелкнет плитку приложения снова, вход в приложение выполнится автоматически.

При первом входе пользователя в некоторые приложения может потребоваться изменить пароль. Если учетные данные пользователя изменились в целевом стороннем приложении, пользователь также должен обновить свои учетные данные, которые хранятся в Azure AD. Для обновления учетных данных пользователю нужно щелкнуть значок справа внизу на плитке приложения и выбрать «обновить учетные данные», чтобы повторно ввести имя пользователя и пароль для этого приложения.

### <a name="application-with-existing-sso-solutions"></a>Приложения с существующими решениями единого входа
При настройке единого входа для приложения на портале управления Azure доступен третий параметр: «Существующий единый вход». Этот параметр позволяет администраторам создавать ссылку на приложение и помещать ее на панель доступа для выбранных пользователей. Например, если существует приложение, которое выполняет проверку подлинности пользователей с помощью служб федерации Active Directory 2.0, администратор может использовать параметр «Существующий единый вход» для создания ссылки на него на панели доступа. Когда пользователи переходят по ссылке, проверка подлинности выполняется с помощью служб федерации Active Directory 2.0 или другого решения существующего единого входа, предоставляемого приложением.

## <a name="related-articles"></a>Связанные статьи
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
* [Список учебников по интеграции приложений SaaS](active-directory-saas-tutorial-list.md)
* [Введение в единый вход и управление доступом к приложениям с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS](active-directory-saas-app-provisioning.md)

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png



<!--HONumber=Oct16_HO2-->


