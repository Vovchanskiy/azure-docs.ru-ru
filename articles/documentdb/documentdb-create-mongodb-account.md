---
title: Создание учетной записи DocumentDB с поддержкой протокола MongoDB | Microsoft Docs
description: Узнайте, как создать учетную запись DocumentDB с поддержкой протокола MongoDB, доступную в предварительной версии.
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2016
ms.author: anhoh

---
# <a name="how-to-create-a-documentdb-account-with-protocol-support-for-mongodb-using-the-azure-portal"></a>Создание учетной записи DocumentDB с поддержкой протокола MongoDB с помощью портала Azure
Чтобы создать учетную запись Azure DocumentDB с поддержкой протокола MongoDB, вам потребуется следующее.

* Учетная запись Azure. Если у вас ее нет, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/) .

## <a name="create-the-account"></a>Создание учетной записи
Чтобы создать учетную запись DocumentDB с поддержкой протокола MongoDB, выполните следующие действия.

1. В новом окне войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Создать**, щелкните **Данные+хранилище**, затем **Просмотреть все** и найдите в категории **Данные+хранилище** элемент "Протокол DocumentDB". Щелкните **DocumentDB с поддержкой протокола MongoDB**.
   
    ![Снимок экрана: колонки поиска "Marketplace" и "Данные+хранилище" с выделенным элементом "DocumentDB — поддержка протокола для MongoDB" (база данных Mongo)](./media/documentdb-create-mongodb-account/marketplacegallery2.png)
3. Можно также в категории **Данные+хранилище** найти и нажать в разделе **Хранение** кнопку **Дополнительно**, а затем нажать кнопку **Загрузить еще** один или несколько раз, пока не появится элемент **DocumentDB - Protocol Support for MongoDB** (DocumentDB — поддержка протоколов для MongoDB). Щелкните **DocumentDB с поддержкой протокола MongoDB**.
   
    ![Снимок экрана: колонки "Marketplace" и "Данные+хранилище" с выделенным элементом "DocumentDB — поддержка протокола для MongoDB" (база данных Mongo)](./media/documentdb-create-mongodb-account/marketplacegallery1.png)
4. В колонке **DocumentDB - Protocol Support for MongoDB (preview)** (DocumentDB — поддержка протоколов для MongoDB (предварительная версия)) щелкните **Создать**, чтобы запустить процесс подписки на предварительную версию.
   
    ![Колонка "DocumentDB — поддержка протокола для MongoDB" на портале Azure](./media/documentdb-create-mongodb-account/marketplacegallery3.png)
5. В колонке **Учетная запись DocumentDB** щелкните **Зарегистрироваться в предварительной версии**. Проверьте данные и нажмите кнопку **ОК**.
   
    ![Колонка "Зарегистрироваться сегодня" для "DocumentDB — поддержка протокола для MongoDB" на портале Azure](./media/documentdb-create-mongodb-account/registerforpreview.png)
6. Подтвердите согласие с условиями предварительной версии, и вы вернетесь обратно к колонке создания.  В колонке **Учетная запись DocumentDB** укажите желаемую конфигурацию учетной записи.
   
   ![Снимок экрана колонки создания учетной записи DocumentDB с поддержкой протокола MongoDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.png)

    - В поле **Идентификатор** введите имя для идентификации учетной записи.  После успешной проверки **идентификатора** в поле **Идентификатор** отображается зеленая галочка. Значение **идентификатора** становится именем узла в универсальном коде ресурса (URI). В **идентификаторе** могут использоваться только строчные буквы, цифры и знак "-". Его длина должна быть от 3 до 50 знаков. Обратите внимание, что к выбранному вами имени конечной точки добавляется *documents.azure.com*. Полученное значение и станет конечной точкой вашей учетной записи.

    - В поле **Подписка**выберите подписку Azure, которую вы хотите использовать для этой учетной записи. Если ваша учетная запись включает только одну подписку, эта учетная запись будет выбрана по умолчанию.

    - В поле **Группа ресурсов** выберите или создайте группу ресурсов для учетной записи.  По умолчанию будет выбрана существующая группа ресурсов в подписке Azure.  Однако вы можете создать новую группу ресурсов, чтобы добавить к ней эту учетную запись. Дополнительные сведения см. в статье [Управление ресурсами Azure с помощью портала Azure](resource-group-portal.md).

    - В поле **Расположение** укажите географическое расположение, в котором будет размещена учетная запись.

    - Установите флажок **Закрепить на панели мониторинга** 

1. Когда вы завершите настройку учетной записи, щелкните **Создать**.  Создание учетной записи может занять несколько минут.  Для проверки состояния можно отслеживать прогресс на начальной панели.  
   ![Снимок экрана: плитка "Создание" для уровня Startboard – создатель базы данных в Интернете](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-3.png)  
   
   Или вы можете наблюдать за ходом выполнения на портале "Уведомления".  
   
   ![Быстрое создание баз данных — снимок экрана: центр уведомлений, где показано, что создается учетная запись DocumentDB](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-4.png)  
   
   ![Снимок экрана: центр уведомлений, где показано, что учетная запись DocumentDB успешно создана и развернута в группе ресурсов — уведомление создателя базы данных в Интернете](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-5.png)
2. После создания учетной записи она будет готова для использования с параметрами по умолчанию. 
   
   ![Снимок экрана колонки учетной записи по умолчанию](./media/documentdb-create-mongodb-account/defaultaccountblades.png)

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [подключиться](documentdb-connect-mongodb-account.md) к учетной записи DocumentDB с поддержкой протокола MongoDB.

<!--HONumber=Oct16_HO2-->


