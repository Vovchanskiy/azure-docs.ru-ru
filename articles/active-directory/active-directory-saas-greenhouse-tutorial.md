---
title: Руководство. Интеграция Azure Active Directory с Greenhouse | Microsoft Docs
description: Узнайте, как использовать Greenhouse вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2016
ms.author: jeedes

---
# Руководство. Интеграция Azure Active Directory с Greenhouse
Цель данного учебника — показать интеграцию Azure и Greenhouse. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Greenhouse

После завершения этого руководства пользователи Azure AD, назначенные Greenhouse, будут иметь возможность единого входа в приложение на корпоративном веб-сайте Greenhouse (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Greenhouse
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Сценарий")

## Включение интеграции приложений для Greenhouse
В этом разделе показано, как включить интеграцию приложений для Greenhouse.

### Чтобы включить интеграцию приложений для Greenhouse, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить**.
   
   ![Добавление приложения](./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Добавить приложение из коллекции")
6. В **поле поиска** введите **greenhouse**.
   
   ![Коллекция приложений](./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Коллекция приложений")
7. В области результатов выберите **Greenhouse** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Greenhouse](./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Greenhouse")
   
   ## Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Greenhouse со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Greenhouse** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Настройка единого входа")
2. На странице **Как пользователи должны входить в Greenhouse?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Настройка единого входа")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес для входа** введите свой URL-адрес в формате "*https://company.greenhouse.io*", а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Настройка URL-адреса приложения")
4. На странице **Настройка единого входа в Greenhouse** нажмите кнопку **Загрузить метаданные**, а затем сохраните файл метаданных на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Настройка единого входа")
5. Передайте этот файл метаданных в службу поддержки Greenhouse.
   
   > [!NOTE]
   > Единый вход должна включить служба поддержки Greenhouse.
   > 
   > 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Настройка единого входа")
   
   ## Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Greenhouse, они должны быть подготовлены для Greenhouse. В случае с Greenhouse подготовка выполняется вручную.

### Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Выполните вход на веб-сайт компании **Greenhouse** в качестве администратора.
2. На панели инструментов в верхней части экрана нажмите элемент **Настройка**, а затем элемент **Пользователи**.
   
   ![Пользователи](./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Пользователи")
3. Нажмите кнопку **Новые пользователи**.
   
   ![Новый пользователь](./media/active-directory-saas-greenhouse-tutorial/IC790792.png "Новый пользователь")
4. В разделе **Добавить нового пользователя** выполните следующие действия.
   
   ![Добавить нового пользователя](./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Добавить нового пользователя")
   
   1. В текстовом поле **Введите адреса электронной почты пользователей** укажите адрес электронной почты действующей учетной записи Azure Active Directory, которую вы хотите подготовить.
   2. Щелкните **Сохранить**.
      
      > [!NOTE]
      > Владельцы учетных записей Azure Active Directory получат электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.
      > 
      > 

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Greenhouse или API, предоставляемые Greenhouse для подготовки учетных записей пользователя AAD.
> 
> 

## Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### Чтобы назначить пользователей Greenhouse, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Greenhouse** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Назначить пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->