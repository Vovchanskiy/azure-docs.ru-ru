---
title: Назначение переменных в хранилище данных SQL | Microsoft Docs
description: Советы по присваиванию значений переменных Transact-SQL в хранилище данных SQL Azure для разработки решений.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/14/2016
ms.author: jrj;barbkess;sonyama

---
# Назначение переменных в хранилище данных SQL
Переменные в хранилище данных SQL задаются с помощью инструкции `DECLARE` или инструкции `SET`.

Ниже перечислены допустимые способы задания значения переменной:

## Задание переменных с помощью DECLARE
Инициализация переменных с помощью DECLARE — один из наиболее гибких способов задать значение переменной в хранилище данных SQL.

```sql
DECLARE @v  int = 0
;
```

С помощью DECLARE можно задать одновременно несколько переменных. Использовать `SELECT` или `UPDATE` для этого нельзя:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Нельзя инициализировать и использовать переменную в одной и той же инструкции DECLARE. Чтобы проиллюстрировать это, ниже приведен **недопустимый** пример, так как @p1 инициализируется и используется в одной и той же инструкции DECLARE. Это приведет к ошибке.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## Задание значений с помощью SET
SET — это очень распространенный метод задания одной переменной.

Ниже приведены примеры допустимого задания переменной с помощью SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

С помощью SET можно одновременно задать только одну переменную. Тем не менее, как видно выше, допускаются составные операторы.

## Ограничения
Нельзя использовать SELECT или UPDATE, чтобы присвоить значение переменной.

## Дальнейшие действия
Дополнительные советы по разработке см. в статье [Общие сведения о разработке][Общие сведения о разработке].

<!--Image references-->

<!--Article references-->
[Общие сведения о разработке]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0629_2016-->