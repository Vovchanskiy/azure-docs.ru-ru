---
title: Восстановление удаленной базы данных SQL Azure (портал Azure) | Microsoft Docs
description: Восстановление удаленной базы данных SQL Azure (портал Azure).
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/09/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# Восстановление удаленной базы данных SQL Azure на портале Azure
> [!div class="op_single_selector"]
> * [Обзор](sql-database-recovery-using-backups.md)
> * [Восстановление удаленной базы данных: PowerShell](sql-database-restore-deleted-database-powershell.md)
> 
> 

## Выбор базы данных для восстановления
Чтобы восстановить базу данных на портале Azure, выполните следующие действия.

1. Откройте [портал Azure](https://portal.azure.com).
2. В левой части экрана выберите **ОБЗОР** > **Серверы SQL**.
3. Перейдите к серверу с базой данных, которую требуется восстановить после удаления, и выберите сервер.
4. Прокрутите вниз до раздела **Операции** колонки сервера и выберите **Удаленные базы данных**. ![Восстановление базы данных SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5. Выберите удаленную базу данных, которую хотите восстановить.
6. Укажите имя базы данных и нажмите кнопку "ОК".
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## Дальнейшие действия
* Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md).
* Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь с разделом [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).
* Чтобы узнать об использовании автоматически создаваемых резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md).
* Чтобы узнать о более быстрых вариантах восстановления, ознакомьтесь с [активной георепликацией](sql-database-geo-replication-overview.md).
* Чтобы узнать об использовании автоматически создаваемых резервных копий для архивации, ознакомьтесь с [копированием базы данных](sql-database-copy.md).

<!---HONumber=AcomDC_0727_2016-->