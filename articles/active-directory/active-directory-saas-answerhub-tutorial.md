---
title: Руководство. Интеграция Azure Active Directory с AnswerHub | Microsoft Docs
description: Узнайте, как использовать AnswerHub вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-answerhub"></a>Руководство. Интеграция Azure Active Directory с AnswerHub
Цель данного руководства — показать интеграцию Azure и [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software).  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) .

После выполнения инструкций, приведенных в этом руководстве, пользователи Azure AD, добавленные к AnswerHub, смогут выполнять единый вход в приложение на корпоративном веб-сайте AnswerHub (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для AnswerHub
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-answerhub-tutorial/IC785165.png "Scenario")

## <a name="enabling-the-application-integration-for-answerhub"></a>Включение интеграции приложений для AnswerHub
В этом разделе показано, как включить интеграцию приложений для AnswerHub.

### <a name="to-enable-the-application-integration-for-answerhub,-perform-the-following-steps:"></a>Чтобы включить интеграцию приложений для AnswerHub, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-answerhub-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-answerhub-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-answerhub-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **AnswerHub**.
   
   ![Коллекция приложений](./media/active-directory-saas-answerhub-tutorial/IC785166.png "Application Gallery")
7. В области результатов выберите **AnswerHub** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в AnswerHub со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **AnswerHub** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в AnswerHub?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configure single sign-on")
3. На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес для входа в AnswerHub** введите URL-адрес в формате "*https://company.answerhub.com*" и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configure App URL")
4. На странице **Настройка единого входа в AnswerHub** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configure single sign-on")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт AnswerHub в качестве администратора.
   
   > [!NOTE]
   > Если вам нужна помощь в настройке AnswerHub, обратитесь в [службу поддержки AnswerHub](mailto:success@answerhub.com.).
   > 
   > 
6. Перейдите в раздел **Administration**(Администрирование).
7. Откройте вкладку **User and Group** (Пользователь и группа).
8. В разделе **Social Settings** (Параметры социальных сетей) в области навигации слева выберите пункт **SAML Setup** (Настройка SAML).
9. Откройте вкладку **IDP Config** (Настройка IDP).
10. На вкладке **IDP Config** (Настройка IDP) выполните следующие действия.
    
    ![Настройка SAML](./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML Setup")
    
    1. На классическом портале Azure на странице диалогового окна **Настройка единого входа в AnswerHub** скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **IDP Login URL** (URL-адрес входа для IDP).
    2. На классическом портале Azure на странице диалогового окна **Настройка единого входа в AnswerHub** скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **IDP Logout URL** (URL-адрес выхода для IDP).
    3. На классическом портале Azure на странице диалогового окна **Настройка единого входа в AnswerHub** скопируйте значение поля **Формат идентификатора имени** и вставьте его в текстовое поле **IDP Name Identifier Format** (Формат идентификатора имени IDP).
    4. Щелкните **Ключи и сертификаты**.
11. На вкладке «Ключи и сертификаты» выполните следующие действия.
    
    ![Ключи и сертификаты](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Keys and Certificates")
    
    1. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
       
       > [!TIP]
       > Дополнительные сведения можно узнать в видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
       > 
       > 
    2. Откройте сертификат в кодировке Base-64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Открытый ключ IDP (формат x509)** .
    3. Щелкните **Сохранить**.
12. На вкладке **IDP Config** (Настройка IDP) нажмите кнопку **Сохранить**.
13. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнять вход в AnswerHub, они должны быть подготовлены для AnswerHub.  
В случае с AnswerHub подготовка выполняется вручную.

### <a name="to-configure-user-provisioning,-perform-the-following-steps:"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на корпоративном веб-сайте **AnswerHub** в качестве администратора.
2. Перейдите в раздел **Administration**(Администрирование).
3. Откройте вкладку **Users & Groups** (Пользователи и группы).
4. В разделе **Manage Users** (Управление пользователями) в области навигации слева выберите пункт **Create or import users** (Создать или импортировать пользователей).
   
   ![Users & Groups](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Users & Groups")
5. Введите в текстовые поля **Email address** (Адрес электронной почты), **Username** (Имя пользователя) и **Password** (Пароль) соответствующие данные действующей учетной записи Azure Active Directory, которую нужно подготовить, и нажмите кнопку **Save** (Сохранить).

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя AnswerHub или API, предоставляемые AnswerHub для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-answerhub,-perform-the-following-steps:"></a>Чтобы назначить пользователей AnswerHub, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **AnswerHub** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-answerhub-tutorial/IC785176.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


