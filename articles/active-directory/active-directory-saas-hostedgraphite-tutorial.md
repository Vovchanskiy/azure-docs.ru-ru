---
title: Учебник. Интеграция Azure Active Directory с Hosted Graphite | Microsoft Docs
description: Узнайте, как настроить единый вход между Azure Active Directory и Hosted Graphite.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2016
ms.author: jeedes

---
# Учебник. Интеграция Azure Active Directory с Hosted Graphite
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Hosted Graphite.

Интеграция Azure AD с приложением Hosted Graphite обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Hosted Graphite.
* Вы можете включить автоматический вход пользователей в Hosted Graphite (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования
Чтобы настроить интеграцию Azure AD с Hosted Graphite, вам потребуется:

* подписка Azure AD;
* подписка Hosted Graphite с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Hosted Graphite из коллекции
2. Настройка и проверка единого входа в Azure AD

## Добавление Hosted Graphite из коллекции
Чтобы настроить интеграцию Hosted Graphite с Azure AD, необходимо добавить Hosted Graphite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Hosted Graphite из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    .![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    .![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить**.
   
    .![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
   
    .![Приложения][4]
6. В поле поиска введите **Hosted Graphite**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_01.png)
7. В области результатов выберите **Hosted Graphite** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_0001.png)

## Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Hosted Graphite с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Hosted Graphite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Hosted Graphite.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Hosted Graphite.

Чтобы настроить и проверить единый вход Azure AD в Hosted Graphite, вам потребуется выполнить действия в указанных далее стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Hosted Graphite](#creating-a-hosted-graphite-test-user)** требуется для создания пользователя Britta Simon в Hosted Graphite, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Hosted Graphite.

**Чтобы настроить единый вход Azure AD в Hosted Graphite, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **Hosted Graphite** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6]
2. На странице **Как пользователи должны входить в Hosted Graphite?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    .![Настройка единого входа](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_03.png)
3. Если вы хотите настроить приложение в **режиме, инициированном IDP**, на странице с диалоговым окном **Настроить параметры приложения** выполните указанные ниже действия и нажмите кнопку **Далее**.
   
    .![Настройка единого входа](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_04.png)
   
    а. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.hostedgraphite.com/metadata/<user id>`.
   
    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://www.hostedgraphite.com/complete/saml/<user id>`.
   
    c. Нажмите кнопку **Далее**.
4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)
   
    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.hostedgraphite.com/login/saml/<user id>/`.
   
    b. Нажмите кнопку **Далее**.
   
   > [!NOTE]
   > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо обновить фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, на стороне приложения перейдите в раздел "Access" (Доступ) -> "SAML setup" (Настройка SAML) или свяжитесь с Hosted Graphite.
   > 
   > 
5. На странице **Настройка единого входа в Hosted Graphite** выполните следующие действия и нажмите кнопку **Далее**.
   
    .![Настройка единого входа](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_05.png)
   
    а. Нажмите **Загрузить сертификат** и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
6. Войдите в клиент Hosted Graphite с правами администратора.
7. На боковой панели выберите **SAML Setup page** (Страница настройки SAML) (**"Access" (Доступ) -> "SAML Setup" (Настройка SAML)**).
   
    .![Настройка единого входа на стороне приложения](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)
8. Подтвердите, что эти URL-адреса соответствуют конфигурации из шага 3.
   
    .![Настройка единого входа на стороне приложения](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)
9. Скопируйте значения полей **URL-адрес издателя** и **URL-адрес единого входа SAML** из Azure AD и вставьте их в поля **Entity or Issuer ID** (Идентификатор сущности или издателя) и **SSO Login URL** (URL-адрес единого входа) в приложении Hosted Graphite.
   
    .![Настройка единого входа на стороне приложения](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)
   
    .![Настройка единого входа на стороне приложения](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_003.png)
10. Для параметра **Default User Role** (Роль пользователя по умолчанию) выберите значение **Read-only** (Только для чтения).
    
     .![Настройка единого входа на стороне приложения](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)
11. Скопируйте содержимое скачанного файла сертификата и вставьте его в текстовое поле **X.509 Certificate** (Сертификат X.509).
    
     .![Настройка единого входа на стороне приложения](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)
12. Нажмите кнопку **Сохранить**.
13. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
    
    .![Единый вход в Azure AD][10]
14. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
    
    .![Единый вход в Azure AD][11]

### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

.![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    .![Создание тестового пользователя Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    .![Создание тестового пользователя Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_05.png)
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя**, введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   .![Создание тестового пользователя Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_06.png)
   
   а. В текстовом поле **Имя** введите **Britta**.
   
   b. В текстовое поле **Фамилия** введите **Simon**.
   
   c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.
   
   г) В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    .![Создание тестового пользователя Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_07.png)
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    .![Создание тестового пользователя Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_08.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.

### Создание тестового пользователя Hosted Graphite
Цель этого раздела — создать пользователя с именем Britta Simon в Hosted Graphite. Приложение Hosted Graphite поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к Hosted Graphite (если он еще не создан).

> [!NOTE]
> Чтобы создать пользователя вручную, необходимо обратиться в службу поддержки Hosted Graphite по адресу <mailto:help@hostedgraphite.com>.
> 
> 

### Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Hosted Graphite.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Hosted Graphite, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Hosted Graphite**.
   
    .![Настройка единого входа](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    .![Назначение пользователя][203]
4. Из списка пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Hosted Graphite на панели доступа, вы автоматически войдете в приложение Hosted Graphite.

## Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0810_2016-->
