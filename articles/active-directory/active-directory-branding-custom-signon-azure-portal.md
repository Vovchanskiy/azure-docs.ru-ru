---
title: Настройка страницы входа в предварительной версии Azure Active Directory | Microsoft Docs
description: Узнайте, как добавить фирменную символику организации на страницу входа в Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# Добавление фирменной символики организации на страницу входа в предварительной версии Azure Active Directory
Чтобы избежать путаницы, многие компании используют единый стиль оформления всех веб-сайтов и служб, которыми они управляют. Предварительная версия Azure Active Directory предоставляет такую возможность, позволяя настроить внешний вид страницы входа, добавив на нее логотип организации и цветовые схемы. [Что есть в предварительной версии?](active-directory-preview-explainer.md) Страница входа: эта страница отображается при входе в Office 365 или другие веб-приложения, использующие Azure AD в качестве поставщика удостоверений. На этой странице вводятся учетные данные.

Если вы хотите показать фирменную символику, цвета и другие настраиваемые элементы на этой странице, изучите следующие изображения, чтобы понять разницу между двумя интерфейсами.

На следующем снимке экрана приведен пример страницы входа в Office 365 на настольном компьютере **перед** настройкой.

![Страница входа в службу Office 365 перед настройкой](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

На следующем снимке экрана приведен пример страницы входа в Office 365 на настольном компьютере **после** настройки.

![Страница входа в службу Office 365 после настройки](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)

## Настройка страницы входа
Как правило, страница входа используется для доступа через веб-браузер к облачным приложениям и службам, на которые у вашей организации есть подписка.

Когда вы что-то меняете на странице входа, может потребоваться до одного часа, чтобы эти изменения вступили в силу.

Страница входа с фирменной символикой отображается только при посещении службы с помощью URL-адреса конкретного клиента, например https://outlook.com/**contoso**.com или https://mail.**contoso**.com.

При посещении службы без URL-адреса клиента (например, https://mail.office365.com) страница входа отображается без фирменной символики. Символика появится, когда вы введете идентификатор пользователя или выберете элемент пользователя.

> [!NOTE]
> * Для доменного имени в части портала Azure **Домены**, в которой была настроена фирменная символика, должно отображаться состояние "Активно". Дополнительные сведения см. в статье о [добавлении имен личных доменов](active-directory-domains-add-azure-portal.md).
> * Фирменная символика страницы входа не применяется к странице входа пользователя корпорации Майкрософт. Если вы входите с использованием учетной записи Майкрософт, то вы можете увидеть список элементов пользователей с фирменной символикой, отображаемый в Azure AD. Но к странице входа в учетную запись Майкрософт это оформление применяться не будет.
> 
> 

**Добавление фирменной символики компании в ваш каталог:**

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.
   
   ![Открытие страницы "Управление пользователями"](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. В колонке **Пользователи и группы** выберите **Company branding** (Фирменная символика компании).
4. В колонке **Company branding** (Фирменная символика компании) щелкните **Изменить**.
   
    ![Изменение пользовательской фирменной символики](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. Измените элементы, которые нужно настроить. Все элементы необязательные.
6. Щелкните **Сохранить**.

Применение каких-либо изменений фирменной символики страницы входа может занять до одного часа.

## Дальнейшие действия
[Добавление фирменной символики компании для конкретного языка](active-directory-branding-localize-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->