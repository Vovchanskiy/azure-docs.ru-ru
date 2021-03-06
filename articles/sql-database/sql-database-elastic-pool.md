---
title: Что такое пул эластичных БД Azure? | Microsoft Docs
description: Управление сотнями и тысячами баз данных с помощью пула. Одна цена за набор единиц производительности может распространяться на весь пул. Включение баз данных в пул и их исключение оттуда по желанию.
keywords: эластичная база данных; базы данных SQL
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: cgronlun

ms.service: sql-database
ms.devlang: NA
ms.date: 07/12/2016
ms.author: CarlRabeler
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# <a name="what-is-an-azure-elastic-pool?"></a>Что такое пул эластичных БД Azure?
Пулы эластичных БД SQL обеспечивают простое и экономически выгодное решение для управления целевыми показателями производительности для нескольких баз данных с совершенно разными и непредсказуемыми моделями функционирования.

> [!NOTE]
> Пулы эластичных БД общедоступны во всех регионах Azure, кроме западной Индии, где сейчас доступна только предварительная версия.  Общедоступные пулы эластичных БД появятся в этом регионе в самое ближайшее время.
> 
> 

## <a name="how-it-works"></a>Принцип работы
В рамках распространенной модели SaaS каждому отдельному клиенту предоставляется собственная база данных. И каждый такой клиент (база данных) характеризуется непредсказуемым потреблением ресурсов памяти, ввода-вывода и ЦП. Так как же эффективно и экономно распределить ресурсы, зная о колебаниях потребности в них? Обычно используется два подхода: 1) подготовка избыточного количества ресурсов, соответствующего пиковой нагрузке (ведет к повышению затрат); 2) подготовка недостаточного количества ресурсов для экономии (ведет к снижению производительности и удовлетворенности клиентов во время пиковой нагрузки). Пулы эластичных БД решают эту проблему, обеспечивая базы данных необходимыми ресурсами производительности, когда они нужны. Они предоставляют простой механизм распределения ресурсов в рамках прогнозируемого бюджета. Дополнительные сведения о шаблонах разработки для приложений SaaS, использующих пулы эластичных БД, см. в статье [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
> 
> 

В Базе данных SQL относительная мера способности базы данных удовлетворять потребности в ресурсах выражается в единицах транзакций базы данных (DTU) для отдельных баз данных и эластичных DTU (eDTU) для эластичных баз данных в пулах эластичных БД. Дополнительные сведения о DTU и eDTU см. в статье [Введение в базы данных SQL](sql-database-technical-overview.md#understand-dtus).

Пулу предоставляется заданное количество единиц eDTU по фиксированной цене. Отдельным базам данных в пуле предоставляется возможность гибкого автоматического масштабирования в рамках заданных параметров. В условиях интенсивной нагрузки база данных может использовать дополнительные единицы eDTU для удовлетворения потребности в ресурсах. При небольших нагрузках базы данных используют меньше ресурсов (eDTU), а при отсутствии нагрузок не используют их вовсе. Подготовка ресурсов для всего пула, а не для отдельной базы данных упрощает задачи управления. Кроме того, используя пул, вы можете спрогнозировать расходы.

Добавить дополнительные единицы eDTU в существующий пул можно без простоя баз данных в пуле эластичных БД или снижения их производительности. Точно так же, если eDTU больше не нужны, они могут быть удалены из существующего пула в любой момент времени.

Вы также можете добавлять базы данных в пул или удалять их из него. Если у базы данных прогнозируемо низкие показатели использования ресурсов, ее можно удалить.

## <a name="which-databases-go-in-a-pool?"></a>Какие базы данных можно включить в пул?
![Базы данных SQL, совместно использующие eDTU в пуле эластичных баз данных.][1]

Для пула эластичных БД лучше всего подходят базы данных, у которых чередуются периоды активности и простоя. В приведенном выше примере показана активность отдельной базы данных, четырех баз данных и пула эластичных БД с 20 базами данных. Базы данных, у которых периоды активности разнесены во времени, отлично подходят для включения в пулы эластичных баз данных. Так как в одно и то же время активны не все базы данных пула, они могут совместно использовать eDTU. Не все базы данных укладываются в эту схему. Для баз данных с более стабильной потребностью в ресурсах, лучше подходят уровни обслуживания "Базовый", "Стандартный" и "Премиум", в рамках которых ресурсы назначаются по отдельности.

[Вопросы цены и производительности для пула эластичных БД](sql-database-elastic-pool-guidance.md).

## <a name="edtu-and-storage-limits-for-elastic-pools-and-elastic-databases."></a>Ограничения eDTU и хранилища для пулов эластичных БД и эластичных баз данных
[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Если использованы все DTU пула эластичных БД, то каждая база данных в пуле получает равное количество ресурсов для обработки запросов.  Служба базы данных SQL обеспечивает равное разделение ресурсов между базами данных за счет равных срезов времени вычислений. Если задано минимальное значение DTU на базу данных, отличное от нуля, то такое равное разделение ресурсов в пуле эластичных БД также позволяет гарантировать любое дополнительное количество ресурсов каждой базе данных.

## <a name="elastic-pool-and-elastic-database-properties"></a>Свойства пула эластичных БД и эластичной базы данных
### <a name="limits-for-elastic-pools"></a>Пределы пулов эластичных БД
| Свойство | Описание |
|:--- |:--- |
| Уровень служб |Basic, Standard или Premium. Уровень служб определяет настраиваемые границы ограничений производительности и хранения, а также возможности управления непрерывностью бизнес-процессов. Каждая база данных в пуле имеет тот же уровень служб, что и пул. Уровень служб также называется "выпуском". |
| Число eDTU на пул |Максимальное количество eDTU, которое может совместно использоваться базами данных в пуле. Общее количество eDTU, используемое базами данных в пуле, не может превышать это ограничение в один момент времени. |
| Максимальный размер хранилища на пул (ГБ) |Максимальный размер хранилища в ГБ, который может совместно использоваться базами данных в пуле. Общий размер хранилища, используемый базами данных в пуле, не может превышать это ограничение. Это ограничение определяется количеством eDTU на каждый пул. Если это ограничение превышено, все базы данных становятся доступными только для чтения. |
| Максимальное количество баз данных на пул |Максимально допустимое число баз данных в пуле. |
| Максимальное количество одновременных рабочих ролей на пул |Максимальное количество одновременных рабочих ролей (запросов) для всех баз данных в пуле. |
| Максимальное количество одновременных входов на пул |Максимальное количество одновременных входов для всех баз данных в пуле. |
| Максимальное количество одновременных сеансов на пул |Максимальное количество одновременных сеансов для всех баз данных в пуле. |

### <a name="limits-for-elastic-databases"></a>Пределы эластичных баз данных
| Свойство | Описание |
|:--- |:--- |
| Максимальное количество eDTU на базу данных |Максимальное количество eDTU, которое может использовать любая база данных в пуле, если оно доступно и не используется другими базами данных в пуле.  Это значение не гарантирует предоставление ресурсов базе данных.  Это глобальный параметр, который применяется ко всем базам данных в пуле. Задайте большое максимальное количество eDTU на базу данных, достаточное для обработки пиков использования базы данных. Значение параметра может быть несколько преувеличенным, так как использование пула предполагает обращение к более и менее активным базам данных в условиях, когда не все базы одновременно обрабатывают пиковое количество запросов. Предположим, например, что пиковая нагрузка одной базы данных равна 20 eDTU и только 20 % из 100 баз данных в пуле одновременно работают с такой нагрузкой.  Если в качестве максимального количества eDTU для каждой базы данных задано значение 20, для пула целесообразно увеличить количество eDTU в 5 раз и задать 400 eDTU на пул. |
| Минимальное количество eDTU на базу данных |Минимальное количество eDTU, которое гарантируется любой базе данных в пуле.  Это глобальный параметр, который применяется ко всем базам данных в пуле. Можно задать минимальное количество eDTU на базу данных, равное 0, что также является значением по умолчанию. Для этого свойства задается значение в диапазоне от 0 до среднего количества eDTU, используемых на одну базу данных. Произведение количества баз данных в пуле и минимального количества eDTU на базу данных не может превышать количество eDTU на пул.  Например, если в пуле 20 баз данных и минимальное количество eDTU на базу данных равно 10, то количество eDTU на пул должно быть не меньше 200. |
| Максимальный размер хранилища для базы данных (ГБ) |Максимальный размер хранилища для базы данных в пуле. Эластичные базы данных совместно используют хранилище пула, поэтому размер хранилища базы данных находится в пределах от наименьшего оставшегося объема хранилища пула до максимального размера хранилища для базы данных. |

## <a name="elastic-database-jobs"></a>Задания обработки эластичных баз данных
Пул упрощает выполнение задач управления, так как позволяет выполнять сценарии в **[заданиях обработки эластичных БД](sql-database-elastic-jobs-overview.md)**. Задания обработки эластичных баз позволяют сократить объем трудоемких операций, связанных с управлением большим количеством баз данных. Сведения о начале работы см. в статье [Начало работы с заданиями обработки эластичных баз данных](sql-database-elastic-jobs-getting-started.md).

Дополнительные сведения о других средствах см. в статье [Программа изучения средств работы с эластичными базами данных](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/).

## <a name="business-continuity-features-for-databases-in-a-pool"></a>Возможности обеспечения непрерывности бизнеса для баз данных в пуле
Эластичные базы данных поддерживают те же [возможности по обеспечению непрерывности бизнес-процессов](sql-database-business-continuity.md) , что и отдельные базы данных на серверах версии 12.

### <a name="point-in-time-restore"></a>Восстановление до точки во времени
При восстановлении до точки во времени используются создаваемые автоматически резервные копии баз данных, чтобы восстановить базу данных в пуле на определенный момент времени. Ознакомьтесь с разделом [Восстановление до точки во времени](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>Геовосстановление
Геовосстановление используется по умолчанию, когда база данных недоступна из-за происшествия в регионе, где она размещена. Ознакомьтесь с разделом [Восстановление базы данных SQL Azure или переход на базу данных-получатель при отказе](sql-database-disaster-recovery.md) 

### <a name="active-geo-replication"></a>Активная георепликация
Для приложений c более высокими требованиями к восстановлению по сравнению с возможностями геовосстановления настройте активную георепликацию. Это можно сделать на [портале Azure](sql-database-geo-replication-portal.md), а также с помощью [PowerShell](sql-database-geo-replication-powershell.md) или [Transact-SQL](sql-database-geo-replication-transact-sql.md).

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Видеокурс о возможностях эластичных баз данных на сайте виртуальной академии Майкрософт](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554) 

<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png



<!--HONumber=Oct16_HO2-->


