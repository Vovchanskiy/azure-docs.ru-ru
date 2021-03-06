---
title: Руководство. Интеграция Azure Active Directory с Adaptive Suite | Microsoft Docs
description: Узнайте, как использовать Adaptive Suite вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач.
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
# Руководство. Интеграция Azure Active Directory с Adaptive Suite
Цель данного учебника — показать интеграцию Azure и Adaptive Suite. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Adaptive Suite

После выполнения инструкций, приведенных в этом руководстве, пользователи Azure AD, добавленные в Adaptive Suite, смогут выполнять единый вход в приложение на корпоративном веб-сайте Adaptive Suite (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Adaptive Suite
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Сценарий")

## Включение интеграции приложений для Adaptive Suite
В этом разделе показано, как включить интеграцию приложений для Adaptive Suite.

### Чтобы включить интеграцию приложений для Adaptive Suite, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить**.
   
   ![Добавление приложения](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Добавить приложение из коллекции")
6. В **поле поиска** введите **Adaptive Suite**.
   
   ![Коллекция приложений](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Коллекция приложений")
7. В области результатов выберите **Adaptive Suite** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
   
   ## Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Adaptive Suite со своей учетной записью Azure AD, используя федерацию на основе протокола SAML. Чтобы настроить единый вход для Adaptive Suite, необходимо извлечь значение отпечатка из сертификата. Если вы не знакомы с этой процедурой, просмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI).

### Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **Adaptive Suite** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Настройка единого входа")
2. На странице **Как пользователи должны входить в Adaptive Suite?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Настройка единого входа")
3. На странице **Настроить параметры приложения** введите в текстовое поле **URL-адрес ответа** URL-адрес в формате "*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*" и нажмите кнопку **Далее**.
   
   > [!NOTE]
   > Этот адрес можно узнать на странице **Параметры единого входа SAML** клиента Adaptive Suite.
   > 
   > 
   
   ![Настройка параметров приложения](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Настройка параметров приложения")
4. На странице **Настройка единого входа в Adaptive Suite** щелкните **Загрузить сертификат** и сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Настройка единого входа")
5. В другом окне веб-браузера войдите в свой корпоративный веб-сайт Adaptive Suite в качестве администратора.
6. Перейдите на страницу **Admin** (Администратор).
   
   ![Администратор](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Администратор")
7. В разделе **Users and Roles** (Пользователи и роли) щелкните **Manage SAML SSO Settings** (Управление параметрами единого входа SAML).
   
   ![Управление параметрами единого входа SAML](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Управление параметрами единого входа SAML")
8. На странице **SAML SSO Settings** (Параметры единого входа SAML) выполните следующие действия.
   
   ![Параметры единого входа SAML](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "Параметры единого входа SAML")
   
   1. Введите имя конфигурации в текстовое поле **Identity provider name** (Имя поставщика удостоверений).
   2. На классическом портале Azure на диалоговой странице **Настройка единого входа в Adaptive Suite** скопируйте значение поля **Идентификатор сущности** и вставьте его в текстовое поле **Identity provider Entity ID** (Идентификатор сущности поставщика удостоверений).
   3. На классическом портале Azure на диалоговой странице **Настройка единого входа в Adaptive Suite** скопируйте значение поля **URL-адрес единого входа SAML** и вставьте его в текстовое поле **Identity provider SSO URL** (URL-адрес единого входа поставщика удостоверений).
   4. На классическом портале Azure на диалоговой странице **Настройка единого входа в Adaptive Suite** скопируйте значение поля **URL-адрес единого входа SAML** и вставьте его в текстовое поле **Custom logout URL** (Настраиваемый URL-адрес выхода).
   5. Чтобы отправить загруженный сертификат, нажмите кнопку **Выбрать файл**.
   6. Для параметра **Идентификатор пользователя SAML** выберите значение **Имя пользователя Adaptive Insights**.
   7. Для параметра **Расположение идентификатора пользователя SAML** выберите значение **Идентификатор пользователя в элементе NameID субъекта**.
   8. Для параметра **Формат элемента NameID SAML** выберите значение **Адрес электронной почты**.
   9. Для параметра **Включить SAML** выберите значение **Разрешить единый вход SAML и прямой вход через Adaptive Insights**.
   10. Щелкните **Сохранить**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Настройка единого входа")
   
   ## Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Adaptive Suite, они должны быть подготовлены для Adaptive Suite. В случае Adaptive Suite подготовка выполняется вручную.

### Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на корпоративный веб-сайт **Adaptive Suite** в качестве администратора.
2. Перейдите на страницу **Admin** (Администратор).
   
   ![Администратор](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Администратор")
3. В разделе **Users and Roles** (Пользователи и роли) щелкните **Add User** (Добавить пользователя).
   
   ![Добавить пользователя](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Добавить пользователя")
4. В разделе **New User** (Новый пользователь) выполните следующие действия.
   
   ![Отправить](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Отправить")
   
   1. Введите в текстовые поля **Name** (Имя), **Login** (Имя для входа), **Email** (Адрес электронной почты) и **Password** (Пароль) соответствующие данные действующего пользователя Azure Active Directory, для которого выполняется подготовка.
   2. Выберите **Роль**.
   3. Нажмите кнопку **Submit** (Отправить).

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Adaptive Suite или API, предоставляемые Adaptive Suite для подготовки учетных записей пользователя AAD.
> 
> 

## Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### Чтобы назначить пользователей Adaptive Suite, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Adaptive Suite** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Назначить пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->