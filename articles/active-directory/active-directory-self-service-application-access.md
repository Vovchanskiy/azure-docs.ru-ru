---
title: Самостоятельный доступ к приложениям и делегированное управление приложениями с помощью Azure Active Directory | Microsoft Docs
description: В этой статье описывается самостоятельный доступ к приложениям и делегированное управление приложениями с помощью Azure Active Directory.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: asmalser

---
# <a name="self-service-application-access-and-delegated-management-with-azure-active-directory"></a>Самостоятельный доступ к приложениям и делегированное управление приложениями с помощью Azure Active Directory
Обеспечение возможности самообслуживания для пользователей — это распространенный сценарий в корпоративных ИТ-решениях. При наличии большого количества пользователей и приложений может оказаться, что у администратора каталога нет достаточного количества данных для принятия решений о предоставлении доступа. Часто случается так, что руководитель группы или другой делегированный администратор может лучше других справиться с решением о предоставлении доступа к приложению. Но в конечном счете именно пользователи работают в приложении, и поэтому им лучше всех известно, какие возможности нужны для выполнения работы.

Самостоятельный доступ к приложению — это функция платформы [Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/) , дающая администраторам каталогов следующие возможности:

* разрешать пользователям запрашивать доступ к приложениям с помощью элемента «Дополнительные приложения» на [панели доступа Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)
* задавать приложения, к которым пользователи могут запрашивать доступ;
* задавать в настройках, требуется ли пользователям утверждение для самостоятельного назначения доступа к приложению;
* устанавливать, кто будет утверждать запросы и управлять доступом к каждому приложению.

Сейчас эту возможность поддерживают все предварительно интегрированные и настраиваемые приложения в [коллекции приложений Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/), которые позволяют выполнять федеративный единый вход или единый вход на основе пароля, в частности Salesforce, Dropbox, Google Apps и многие другие.
В этой статье описывается, как:

* Настроить самостоятельный доступ к приложениям для пользователей, в том числе дополнительный рабочий процесс утверждения 
* Делегировать управление доступом для определенных приложений наиболее подходящим людям в организации, а также разрешать им использовать панель доступа Azure AD для утверждения запросов на доступ, непосредственно предоставлять доступ отдельным пользователям или (дополнительно) задавать учетные данные для доступа к приложениям при настройке единого входа на основе пароля.

## <a name="configuring-self-service-application-access"></a>Настройка самостоятельного доступа к приложениям
Чтобы разрешить самостоятельный доступ к приложениям и задать в настройках, какие приложения могут добавлять или запрашивать пользователи, следуйте инструкциям ниже.

**1.** Перейдите на [классический портал Azure](https://manage.windowsazure.com/).

**2.** В разделе **Active Directory** выберите необходимый каталог, а затем — вкладку **Приложения**. 

**3.** Нажмите кнопку **Добавить** и выберите или добавьте приложение, используя коллекцию.

**4.** После добавления приложения откроется страница быстрого запуска приложения. Щелкните **Настройка единого входа**, выберите необходимый режим единого входа и сохраните настройки. 

**5.** Далее перейдите на вкладку **Настройка**. Чтобы разрешить пользователям запрашивать доступ к этому приложению с помощью панели доступа Azure AD, для параметра **Разрешить самостоятельный доступ к приложению** установите значение **Да**.

![][1]

**6.** Чтобы настроить рабочий процесс утверждения запросов на доступ (необязательно), для параметра **Перед предоставлением доступа требуется утверждение** установите значение **Да**. Затем с помощью кнопки **Утверждающие лица** выберите одно или несколько утверждающих лиц.

Утверждающим лицом может быть любой пользователь в организации с учетной записью Azure AD, который занимается подготовкой учетных записей, лицензированием или другими бизнес-процессами, которые требуются в организации перед предоставлением доступа к приложению. Утверждающим лицом может быть даже владелец одной или нескольких групп общей учетной записи. Он может назначать пользователей одной из этих групп для предоставления им доступа через общую учетную запись. 

Если утверждение не требуется, приложение будет сразу же добавлено на панель доступа Azure AD. Такой вариант является подходящим, если в приложении настроена [автоматическая подготовка учетных записей пользователей](active-directory-saas-app-provisioning.md) или установлен [режим "управляемого пользователем" единого входа по паролю](active-directory-appssoaccess-whatis.md#password-based-single-sign-on), при которых у пользователя уже есть учетная запись и пароль, который ему известен.

**7.** Если приложение настроено для использования единого входа по паролю, также будет доступен параметр, который предоставляет утверждающему лицу право устанавливать учетные данные единого входа от имени каждого пользователя. Дополнительные сведения см. в следующем разделе об управлении делегированным доступом.

**8.** Наконец, в разделе **Группа для самостоятельно назначенных пользователей** отображается имя группы, которая будет использоваться для размещения пользователей, которым предоставлен или назначен доступ к приложению. Лицо, подтверждающее доступ, становится владельцем этой группы. Если показанное имя группы не существует, оно будет создано автоматически. При необходимости в качестве имени группы можно задать имя существующей группы.

**9.** Нажмите кнопку **Сохранить** внизу экрана, чтобы сохранить конфигурацию. Теперь пользователи смогут запрашивать доступ к этому приложению из панели доступа.

**10.** Чтобы протестировать работу пользователей, войдите на панель доступа Azure AD организации по адресу https://myapps.microsoft.com, при этом предпочтительно войти через учетную запись, которая не используется для утверждения доступа к приложению. 

**11.** На вкладке **Приложения** щелкните элемент **Дополнительные приложения**. В результате отобразится коллекция всех приложений, для которых включен самостоятельный доступ к приложению в каталоге, с возможностью поиска и фильтрации по категориям приложений в левой части окна. 

**12.** Щелкните приложение, чтобы запустить процесс запроса. Если утверждение не требуется, приложение будет сразу же добавлено на вкладку **Приложения** после быстрого подтверждения. Если утверждение требуется, появится соответствующее диалоговое окно и на электронную почту утверждающих лиц будут отправлены письма. (Краткое примечание. Чтобы увидеть процесс запроса, следует войти на панель доступа, не используя учетную запись утверждающего лица.)

**13.** В электронном письме утверждающему лицу предлагается войти на панель доступа Azure AD и подтвердить запрос. После утверждения запроса (и выполнения утверждающим лицом всех особых процессов, которые были заранее определены) приложение станет доступным для пользователей на вкладке **Приложения** , где они смогут войти в него.

## <a name="delegated-application-access-management"></a>Управление делегированным доступом к приложениям
Лицом, утверждающим доступ к приложению, может быть любой пользователь в организации, который лучше остальных подходит для предоставления или запрета доступа к этому приложению. Этот пользователь может заниматься подготовкой учетных записей, лицензированием или другими бизнес-процессами, которые требуются в организации перед предоставлением доступа к приложению.

При настройке описанного выше самостоятельного доступа к приложениям назначенным утверждающим лицам будет доступен дополнительный элемент **Управление приложениями** на панели доступа Azure AD, который показывает, к каким приложениям у них есть доступ администратора. Если щелкнуть приложение, отобразится экран с несколькими параметрами.

![][2]

### <a name="approve-requests"></a>Утверждение запросов
Элемент **Утверждение запросов** позволяет утверждающим лицам увидеть ожидающие утверждения запросы на доступ к определенному приложению, а также перейти на вкладку «Утверждения», на которой можно подтвердить или отклонить запросы. Обратите внимание, что при создании запросов утверждающее лицо также получает автоматические сообщения электронной почты, в которых содержатся указания о дальнейших действиях.

### <a name="add-users"></a>Добавление пользователей
Элемент **Добавление пользователей** позволяет утверждающим лицам напрямую предоставлять доступ к приложению выбранным пользователям. Если щелкнуть этот элемент, откроется диалоговое окно, в котором утверждающие лица могут искать и просматривать пользователей в каталоге. В результате добавления пользователей соответствующее приложение отображается на панелях доступа Azure AD этих пользователей или в Office 365. Если перед входом пользователя в приложении требуется вручную выполнить процесс подготовки учетных записей, утверждающее лицо должно выполнить этот процесс до предоставления доступа.  

### <a name="manage-users"></a>Управление пользователями
Элемент **Управление пользователями** позволяет утверждающим лицам непосредственно обновлять список пользователей, которые имеют доступ к приложению, или удалять их. 

### <a name="configure-password-sso-credentials-(if-applicable)"></a>Настройка учетных данных единого входа на основе пароля (если применимо)
Элемент **Настройка** доступен, если ИТ-администратор настроил приложение для использования единого входа на основе пароля и предоставил утверждающему лицу возможность устанавливать учетные данные единого входа на основе пароля, как описано выше. Если щелкнуть эту плитку, отобразится несколько вариантов передачи учетных данных назначенным пользователям на выбор утверждающего лица:

![][3]

* **Вход пользователей с помощью своих паролей** — в этом режиме назначенные пользователи знают свои имена пользователей и пароли для приложения, которые им будет предложено ввести во время первого входа в приложение. Этот вариант соответствует использованию единого входа на основе пароля, при котором [пользователи управляют своими учетными данными](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).
* **Пользователи автоматически входят с помощью отдельных учетных записей, которыми я управляю** — в этом режиме при входе в приложение назначенным пользователям не нужно вводить или знать свои учетные данные для конкретного приложения. Вместо этого утверждающее лицо устанавливает учетные данные для каждого пользователя после предоставления им доступа при помощи элемента **Добавление пользователей** . Когда пользователь щелкает приложение на панели доступа или в Office 365, он автоматически входит с помощью учетных данных, установленных утверждающим лицом. Этот вариант соответствует использованию единого входа на основе пароля, при котором [администраторы управляют учетными данными](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).
* **Пользователи автоматически входят с помощью единой учетной записи, которой я управляю**. Это особый случай, который можно использовать, когда требуется предоставить доступ всем назначенным пользователям с помощью единой общей учетной записи. Обычно этот вариант используется для приложений социальных сетей, когда у организации есть единая учетная запись, в которую вносят обновления многие пользователи. Этот вариант также соответствует использованию единого входа на основе пароля, при котором [администраторы управляют учетными данными](active-directory-appssoaccess-whatis.md#password-based-single-sign-on). Тем не менее после выбора этого параметра утверждающему лицу будет предложено ввести имя пользователя и пароль для единой общей учетной записи. После этого, щелкнув приложение на панелях доступа Azure AD или в Office 365, все назначенные пользователи будут входить с помощью этой учетной записи.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)

<!--Image references-->
[1]: ./media/active-directory-self-service-application-access/ssaa_admin.PNG
[2]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app.PNG
[3]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app_config.PNG



<!--HONumber=Oct16_HO2-->


