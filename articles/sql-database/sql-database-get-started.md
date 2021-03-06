---
title: "Руководство по базам данных SQL: создание базы данных SQL | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как настроить логический сервер базы данных SQL, правило брандмауэра для сервера, базу данных SQL и демонстрационные данные. Также вы научитесь выполнять подключение с помощью клиентских средств, настраивать пользователей и правила брандмауэра для базы данных."
keywords: "руководство по базам данных SQL, создание базы данных SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 914d5f7e6b8e9165fc8ba9021e1030b865ba5fb2


---
# <a name="sql-database-tutorial-create-a-sql-database-in-minutes-by-using-the-azure-portal"></a>Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure
> [!div class="op_single_selector"]
> * [Портал Azure](sql-database-get-started.md)
> * [C#](sql-database-get-started-csharp.md)
> * [PowerShell](sql-database-get-started-powershell.md)
> 
> 

Из этого руководства вы узнаете, как с помощью портала Azure выполнять следующие операции.

* Создание базы данных SQL Azure c помощью демонстрационных данных.
* Создание правила брандмауэра уровня сервера для одного IP-адреса или диапазона IP-адресов.

Эти же задачи можно выполнять с помощью [C#](sql-database-get-started-csharp.md) или [PowerShell](sql-database-get-started-powershell.md).

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

## <a name="create-your-first-azure-sql-database"></a>Создание своей первой базы данных SQL Azure
1. Если вы еще этого не сделали, подключитесь к [порталу Azure](http://portal.azure.com).
2. Щелкните **Создать**, **Данные+хранилище** и выберите **База данных SQL**.
   
    ![Новая база данных SQL 1](./media/sql-database-get-started/sql-database-new-database-1.png)
3. Щелкните **База данных SQL** , чтобы открыть колонку "База данных SQL". Содержимое этой колонки зависит от количества подписок и существующих объектов (например, существующих серверов).
   
    ![Новая база данных SQL 2](./media/sql-database-get-started/sql-database-new-database-2.png)
4. В текстовое поле **Имя базы данных** введите имя для своей первой базы данных, например "моя-база-данных". Зеленый флажок указывает, что выбрано допустимое имя.
   
    ![Новая база данных SQL 3](./media/sql-database-get-started/sql-database-new-database-3.png)
5. Выберите подписку (если у вас их несколько).
6. В разделе **Группа ресурсов** щелкните **Создать** и укажите имя для своей первой группы ресурсов, например "моя-группа-ресурсов". Зеленый флажок указывает, что выбрано допустимое имя.
   
    ![Новая база данных SQL 4](./media/sql-database-get-started/sql-database-new-database-4.png)
7. В списке **Выбор источника** щелкните **Пример**, а затем в списке **Выберите пример** щелкните **AdventureWorksLT [V12]**.
   
    ![Новая база данных SQL 5](./media/sql-database-get-started/sql-database-new-database-5.png)
8. В разделе **Сервер** щелкните **Настроить обязательные параметры**.
   
    ![Новая база данных SQL 6](./media/sql-database-get-started/sql-database-new-database-6.png)
9. В колонке "Сервер" щелкните **Создать сервер**. В объекте сервера (это может быть новый или существующий сервер) будет создана база данных SQL Azure.
   
    ![Новая база данных SQL 7](./media/sql-database-get-started/sql-database-new-database-7.png)
10. Просмотрите колонку **Создание сервера** , чтобы узнать, какие сведения следует указать для нового сервера.
    
    ![Новая база данных SQL 8](./media/sql-database-get-started/sql-database-new-database-8.png)
11. В текстовое поле **Имя сервера** введите имя для своего первого сервера, например "мой-новый-серверный-объект". Зеленый флажок указывает, что выбрано допустимое имя.
    
    ![Новая база данных SQL 9](./media/sql-database-get-started/sql-database-new-database-9.png)
12. В поле **Имя для входа администратора сервера**укажите имя пользователя, которое будет использоваться для входа администратора на этот сервер, например my-admin-account. Это имя называется именем субъекта сервер. Зеленый флажок указывает, что выбрано допустимое имя.
    
    ![Новая база данных SQL 10](./media/sql-database-get-started/sql-database-new-database-10.png)
13. В полях **Пароль** и **Подтверждение пароля** укажите пароль учетной записи субъекта-сервера, например "p@ssw0rd1".. Зеленый флажок указывает, что выбран допустимый пароль.
    
    ![Новая база данных SQL 11](./media/sql-database-get-started/sql-database-new-database-11.png)
14. В списке **Расположение**выберите центр обработки данных, соответствующий вашему расположению, например "Восточная Австралия".
    
    ![Новая база данных SQL 12](./media/sql-database-get-started/sql-database-new-database-12.png)
15. В разделе **"Создать сервер V12 (последнее обновление)" обратите внимание, что доступен только вариант создания текущей версии сервера SQL Azure.
    
    ![Новая база данных SQL 13](./media/sql-database-get-started/sql-database-new-database-13.png)
16. Обратите внимание, что по умолчанию флажок **Разрешить службам Azure доступ к серверу** установлен и его нельзя изменить здесь. Это дополнительный параметр. Его можно изменить в параметрах брандмауэра сервера для этого объекта сервера, хотя в большинстве случаев это не требуется.
    
    ![Новая база данных SQL 14](./media/sql-database-get-started/sql-database-new-database-14.png)
17. В колонке "Создание сервера" просмотрите выбранные параметры и нажмите кнопку **Выбрать** , чтобы выбрать этот сервер для новой базы данных.
    
    ![Новая база данных SQL 15](./media/sql-database-get-started/sql-database-new-database-15.png)
18. В колонке "База данных SQL" в разделе **Ценовая категория** щелкните **S2 Standard**, а затем **Базовый**, чтобы выбрать самую низкую ценовую категорию для своей первой базы данных. Ценовую категорию всегда можно изменить позднее.
    
    ![Новая база данных SQL 16](./media/sql-database-get-started/sql-database-new-database-16.png)
19. В колонке "База данных SQL" просмотрите выбранные параметры и нажмите кнопку **Создать** , чтобы создать свой первый сервер и базу данных. Указанные вами значения пройдут проверку, и начнется развертывание.
    
    ![Новая база данных SQL 17](./media/sql-database-get-started/sql-database-new-database-17.png)
20. Чтобы проверить состояние развертывания, на портале на панели инструментов щелкните **Уведомления** .
    
    ![Новая база данных SQL 18](./media/sql-database-get-started/sql-database-new-database-18.png)

> [!IMPORTANT]
> Когда развертывание завершится, в Azure будут созданы новый сервер и база данных SQL Azure. Вы не сможете подключиться к новому серверу или базе данных с помощью средств SQL Server, пока не создадите правило брандмауэра для сервера, которое открывает брандмауэр базы данных SQL для подключений извне Azure.
> 
> 

[!INCLUDE [Create server firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Дальнейшие действия
После выполнения инструкций из этого руководства по базам данных SQL у вас будет готовая база данных с демонстрационными данными, которую можно использовать с помощью привычных средств.

* Если вы знакомы с языком Transact-SQL и средой SQL Server Management Studio (SSMS), узнайте, как [подключиться и отправлять запросы к базе данных SQL с помощью SSMS](sql-database-connect-query-ssms.md).
* Если вы знаете Excel, узнайте, как [подключиться к базе данных SQL в Azure с помощью Excel](sql-database-connect-excel.md).
* Если вы готовы написать свой собственный код, выберите язык программирования в [библиотеках подключений для базы данных SQL и SQL Server](sql-database-libraries.md).
* Дополнительные сведения о переносе локальных баз данных SQL Server в Azure см. в статье [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-cloud-migrate.md).
* Чтобы загрузить данные в новую таблицу из CSV-файла с помощью программы командной строки BCP, см. сведения в статье [Загрузка данных из CSV-файла в хранилище данных SQL Azure (неструктурированные файлы)](sql-database-load-from-csv-with-bcp.md).
* Дополнительные сведения об обеспечении безопасности базы данных SQL Azure см. в статье [Руководство по базам данных SQL: создание учетных записей пользователей базы данных SQL для доступа к базе данных и управления ею с помощью портала Azure](sql-database-get-started-security.md).

## <a name="additional-resources"></a>Дополнительные ресурсы
[Что такое база данных SQL?](sql-database-technical-overview.md)




<!--HONumber=Nov16_HO2-->


