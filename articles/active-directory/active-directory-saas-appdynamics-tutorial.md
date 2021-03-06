---
title: Руководство. Интеграция Azure Active Directory с AppDynamics | Microsoft Docs
description: Узнайте, как использовать AppDynamics вместе с Azure Active Directory для настройки единого входа, автоматической подготовки пользователей и выполнения других задач.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2016
ms.author: jeedes

---
# Руководство. Интеграция Azure Active Directory с AppDynamics
Цель данного учебника — показать интеграцию Azure и AppDynamics. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа AppDynamics

После выполнения инструкций, приведенных в этом руководстве, пользователи Azure AD, добавленные к AppDynamics, смогут выполнять единый вход в приложение на корпоративном веб-сайте AppDynamics (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для AppDynamics
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Сценарий")

## Включение интеграции приложений для AppDynamics
В этом разделе показано, как включить интеграцию приложений для AppDynamics.

### Чтобы включить интеграцию приложений для AppDynamics, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить**.
   
   ![Добавление приложения](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Добавить приложение из коллекции")
6. В **поле поиска** введите **AppDynamics**.
   
   ![Коллекция приложений](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Коллекция приложений")
7. В области результатов выберите **AppDynamics** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
   
   ## Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в AppDynamics со своей учетной записью Azure AD, используя федерацию на основе протокола SAML. В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **AppDynamics** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Настройка единого входа")
2. На странице **Как пользователи должны входить в AppDynamics?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Настройка единого входа")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в AppDynamics** введите URL-адрес, используемый для входа в AppDynamics (например, "*https://companyname.saas.appdynamics.com*"), и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Настройка URL-адреса приложения")
4. Чтобы загрузить сертификат, на странице **Настройка единого входа в AppDynamics** нажмите кнопку **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.
   
   ![Настройка единого входа](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Настройка единого входа")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт AppDynamics в качестве администратора.
6. Щелкните **Settings** (Параметры) на панели инструментов в верхней части экрана и выберите **Administration** (Администрирование).
   
   ![Администрирование](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Администрирование")
7. Откройте вкладку **Authentication Provider** (Поставщик проверки подлинности).
   
   ![Поставщик проверки подлинности](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Поставщик проверки подлинности")
8. В разделе **Authentication Provider** (Поставщик проверки подлинности) выполните следующие действия.
   
   ![Настройка SAML](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "Настройка SAML")
   
   1. Для параметра **Authentication Provider** (Поставщик проверки подлинности) выберите значение **SAML**.
   2. На диалоговой странице **Настройка единого входа в AppDynamics** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес для входа**.
   3. На диалоговой странице **Настройка единого входа в AppDynamics** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Logout URL** (URL-адрес для выхода).
   4. Создайте файл в кодировке **Base-64** из загруженного сертификата.
      
      > [!TIP]
      > Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).
      > 
      > 
   5. Откройте сертификат в кодировке Base-64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат**.
   6. Щелкните **Сохранить**. ![Сохранить](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Сохранить")
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Настройка единого входа")
   
   ## Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в AppDynamics, они должны быть подготовлены для AppDynamics. В случае с AppDynamics подготовка выполняется вручную.

### Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Войдите на свой корпоративный веб-сайт AppDynamics в качестве администратора.
2. Откройте раздел **Users** (Пользователи) и щелкните значок **+**, чтобы открыть диалоговое окно **Create User** (Создание пользователя).
   
   ![Пользователи](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Пользователи")
3. В разделе **Создание пользователя** выполните следующие действия.
   
   ![Создание пользователя](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Создать пользователя")
   
   1. Введите в текстовые поля **Username** (Имя пользователя), **Name** (Имя), **Email** (Адрес электронной почты), **New Password** (Новый пароль) и **Repeat New Password** (Повторите новый пароль) соответствующие данные действующей учетной записи AAD, которую вы хотите подготовить.
   2. Щелкните **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя AppDynamics или API, предоставляемые AppDynamics для подготовки учетных записей пользователя AAD.
> 
> 

## Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### Чтобы назначить пользователей AppDynamics, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **AppDynamics** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Назначить пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->