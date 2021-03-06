---
title: Учебник. Интеграция Azure Active Directory с облачной платформой SAP HANA | Microsoft Docs
description: Узнайте, как использовать SAP HANA Cloud Platform вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>Учебник. Интеграция Azure Active Directory с облачной платформой SAP HANA
Цель данного учебника — показать интеграцию Azure и облачной платформы SAP HANA.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* действующая подписка Azure;
* Учетная запись облачной платформы SAP HANA.

По завершении работы с этим руководством пользователи Azure AD, назначенные на SAP HANA Cloud Platform, смогут выполнять единый вход в приложение следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

> [!IMPORTANT]
> Для тестирования единого входа необходимо развернуть собственное приложение или оформить подписку на приложение в учетной записи облачной платформы SAP HANA. В этом учебнике используется развертывание приложения в учетной записи.
> 
> 

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для облачной платформы SAP HANA
2. Настройка единого входа
3. Назначение роли пользователю
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scenario")

## <a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>Включение интеграции приложений для облачной платформы SAP HANA
В этом разделе показано, как включить интеграцию приложений для облачной платформы SAP HANA.

### <a name="to-enable-the-application-integration-for-sap-hana-cloud-platform,-perform-the-following-steps:"></a>Чтобы включить интеграцию приложений для облачной платформы SAP HANA, выполните следующие действия:
1. На портале управления Azure в левой области навигации нажмите **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **SAP HANA Cloud Platform**.
   
   ![Коллекция приложений](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Application Gallery")
7. В области результатов выберите **SAP HANA Cloud Platform** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить аутентификацию в облачной платформе SAP HANA со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется отправить сертификат в кодировке Base-64 в клиент облачной платформы SAP HANA.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **SAP HANA Cloud Platform** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configure single sign-on")
2. На странице **Как пользователи должны входить на SAP HANA Cloud Platform?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configure Single Sign-On")
3. В другом окне веб-браузера войдите в панель SAP HANA Cloud Platform по адресу https://account.\<узел_среды\>.ondemand.com/cockpit (например, *https://account.hanatrial.ondemand.com/cockpit*).
4. Щелкните вкладку **Доверие** .
   
   ![Доверие](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Trust")
5. В разделе управления доверием выполните следующие действия:
   
   ![Получить метаданные](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Get Metadata")
   
   1. Щелкните вкладку **Поставщик локальных служб** .
   2. Чтобы скачать файл метаданных SAP HANA Cloud Platform, щелкните **Получить метаданные**.
6. На странице **Настроить URL-адрес приложения** классического портала Azure выполните приведенные ниже действия, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configure App URL")
   
   1. В текстовом поле **URL-адрес единого входа** введите URL-адрес, который пользователи используют для входа в приложение **SAP HANA Cloud Platform**. Это URL-адрес защищенного ресурса для конкретной учетной записи в приложении облачной платформы SAP HANA. URL-адрес составлен по следующей схеме: *https://\<имя_приложения\>\<имя_учетной_записи\>.\<узел_среды\>.ondemand.com/\<путь\_к\_защищенному\_ресурсу\>* (например: *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*).
      
      > [!NOTE]
      > Это URL-адрес в приложении облачной платформы SAP HANA, запрашивающий аутентификацию пользователя.
      > 
      > 
   2. Откройте скачанный файл метаданных облачной платформы SAP HANA, а затем найдите тег **ns3:AssertionConsumerService** .
   3. Скопируйте значение атрибута **Location** и вставьте его в текстовое поле **URL-адрес ответа SAP HANA Cloud Platform**.
7. На странице **Настройка единого входа в SAP HANA Cloud Platform** щелкните **Скачать метаданные**, чтобы скачать метаданные, а затем сохраните файл метаданных на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configure Single Sign-On")
8. В разделе **Поставщик локальных служб** пульта управления SAP HANA Cloud Platform сделайте следующее:
   
   ![Управление доверием](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Trust Management")
   
   1. Нажмите кнопку **Изменить**.
   2. Для параметра **Configuration Type** (Тип конфигурации) выберите значение **Custom** (Настраиваемая).
   3. В поле **Имя поставщика локальных служб**оставьте значение по умолчанию.
   4. Чтобы создать пару ключей **Signing Key** (Ключ подписывания) и **Signing Certificate** (Сертификат подписывания), щелкните **Generate Key Pair** (Создать пару ключей).
   5. Для параметра **Principal Propagation** (Распространение субъектов) выберите значение **Disabled** (Отключено).
   6. Для параметра **Force Authentication** (Принудительная аутентификация) выберите значение **Disabled** (Отключено).
   7. Щелкните **Сохранить**.
9. Откройте вкладку **Trusted Identity Provider** (Доверенный поставщик удостоверений) и щелкните **Add Trusted Identity Provider** (Добавить доверенный поставщик удостоверений).
   
   ![Управление доверием](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Trust Management")
   
   > [!NOTE]
   > Для управления списком доверенных поставщиков удостоверений необходимо выбрать тип конфигурации «Настраиваемая» в разделе «Поставщик локальных служб». В качестве типа конфигурации по умолчанию используется неизменяемое неявное доверие к службе SAP ID Service. Для значения «Нет» параметры доверия отсутствуют.
   > 
   > 
10. Откройте вкладку **General** (Общие) и нажмите кнопку **Browse** (Обзор), чтобы передать скачанный файл метаданных.
    
    ![Управление доверием](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Trust Management")
    
    > [!NOTE]
    > После передачи файла метаданных поля **Single Sign-on URL** (URL-адрес единого входа), **Single Logout URL** (URL-адрес единого выхода) и **Signing Certificate** (Сертификат для подписи) заполняются автоматически.
    > 
    > 
11. Перейдите на вкладку **Атрибуты** .
12. На вкладке **Атрибуты** сделайте следующее:
    
    ![Атрибуты](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributes")
    
    1. Щелкните **Добавить атрибут на основе утверждений**и добавьте следующие атрибуты на основе утверждений:
       
       | Атрибут утверждения | Атрибут субъекта |
       | --- | --- |
       | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname |firstname |
       | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname |Lastname |
       | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress |email |
    
    > [!NOTE]
    > Конфигурация атрибутов зависит от способа разработки приложений в HCP, т. е. какие атрибуты предполагается использовать в ответе SAML и какое имя (атрибут субъекта) используется для доступа к этому атрибуту в коде.
    > 
    > а.  **Атрибут по умолчанию** на снимке экрана предоставляется только для примера. Он не требуется для того, чтобы сценарий работал.  
    > 
    > b.  Имена и значения для **атрибута субъекта** , показанные на снимке экрана, зависят от способа разработки приложения. Возможно, приложению требуются другие сопоставления.
    > 
    > 
13. На классическом портале Azure в диалоговом окне **Настройка единого входа в SAP HANA Cloud Platform** выберите подтверждение настройки единого входа и нажмите кнопку **Завершить**.
    
    ![Настройка единого входа](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configure Single Sign-On")

При необходимости для поставщика удостоверений Azure Active Directory можно настроить группы на основе утверждений.

> [!NOTE]
> Использование групп на облачной платформе SAP HANA позволяет динамически назначать одного или нескольких пользователей одной или нескольким ролям в приложениях облачной платформы SAP HANA, которые определяются по значениям атрибутов в утверждении SAML 2.0. Например, если утверждение содержит атрибут *contract=temporary*, возможно, вам потребуется добавить всех затронутых пользователей в группу *TEMPORARY*. Группа*TEMPORARY*может содержать одну или несколько ролей из одного или нескольких приложений, развернутых в учетной записи SAP HANA Cloud Platform.
> 
> Используйте группы на основе утверждений, чтобы массово назначить большое число пользователей для одной или нескольких ролей приложений в учетной записи облачной платформы SAP HANA. Если требуется назначить одного или небольшое число пользователей конкретным ролям, рекомендуется назначить их непосредственно на вкладке**Authorizations**(Разрешения) панели SAP HANA Cloud Platform.
> 
> 

## <a name="assigning-a-role-to-a-user"></a>Назначение роли пользователю
Чтобы разрешить пользователям Azure AD входить в облачную платформу SAP HANA, им необходимо назначить роли в облачной платформе SAP HANA.

### <a name="to-assign-a-role-to-a-user,-perform-the-following-steps:"></a>Чтобы назначить роль пользователю, выполните следующие действия:
1. Войдите в пульт управления **SAP HANA Cloud Platform** .
2. Выполните следующие действия:
   
   ![Authorizations](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Authorizations")
   
   1. Щелкните **Авторизация**.
   2. Откройте вкладку **Пользователи** .
   3. В тестовом поле **Пользователь** введите электронный адрес пользователя.
   4. Щелкните **Назначить** , чтобы назначить роль пользователю.
   5. Щелкните **Сохранить**.

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-sap-hana-cloud-platform,-perform-the-following-steps:"></a>Чтобы назначить пользователей облачной платформы SAP HANA, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **SAP HANA Cloud Platform** щелкните **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


