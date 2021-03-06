---
title: Восстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью портала Azure | Microsoft Docs
description: Геовосстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью портала Azure.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/17/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# Геовосстановление базы данных SQL Azure из геоизбыточной резервной копии с помощью портала Azure
> [!div class="op_single_selector"]
> * [Обзор](sql-database-recovery-using-backups.md)
> * [Геовосстановление: PowerShell](sql-database-geo-restore-powershell.md)
> 
> 

В этой статье показано, как восстановить базу данных на новый сервер с помощью геовосстановления и портала Azure.

## Выбор базы данных для восстановления
Чтобы восстановить базу данных на портале Azure, выполните следующие действия.

1. Откройте [портал Azure](https://portal.azure.com).
2. В левой части экрана последовательно выберите **Создать** > **Данные и хранилище** > **База данных SQL**.
3. Выберите **Резервная копия** в качестве источника, затем выберите геоизбыточную резервную копию, из которой вы хотите восстановить базу данных.
   
   ![Восстановление базы данных SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)
4. Укажите имя базы данных, сервер, на котором вы хотите восстановить базу данных, и нажмите кнопку "Создать".

## Дальнейшие действия
* Сведения об обеспечении непрерывности бизнес-процессов и возможные сценарии описаны в [обзоре непрерывности бизнес-процессов](sql-database-business-continuity.md).
* Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь с разделом [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).
* Чтобы узнать об использовании создаваемых автоматически резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md).
* Чтобы узнать о более быстрых вариантах восстановления, ознакомьтесь с [активной георепликацией](sql-database-geo-replication-overview.md).
* Чтобы узнать об использовании создаваемых автоматически резервных копий для архивации, ознакомьтесь с [копированием базы данных](sql-database-copy.md).

<!---HONumber=AcomDC_0727_2016-->