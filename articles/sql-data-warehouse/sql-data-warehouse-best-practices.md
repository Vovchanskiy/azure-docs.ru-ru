---
title: "Рекомендации по использованию хранилища данных SQL Azure | Документация Майкрософт"
description: "Рекомендации, которые следует учитывать при разработке решений для хранилища данных SQL Azure. Они помогут вам обеспечить эффективную работу."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f04cba06cfe6a9094268ea041e85753cbd99f8fd


---
# <a name="best-practices-for-azure-sql-data-warehouse"></a>Рекомендации по использованию хранилища данных SQL Azure
В этой статье содержится множество рекомендаций, которые помогут достичь оптимальной производительности хранилища данных SQL Azure.  Некоторые понятия, рассмотренные в этой статье, достаточно простые и понятные, но есть и понятия, предназначенные для более опытных пользователей, поэтому они рассмотрены лишь в общих чертах.  Здесь представлены основные рекомендации и сведения относительно важных моментов, которые следует учитывать при создании хранилища данных.  В каждом разделе содержится краткое описание определенных понятий и ссылки на статьи с более подробными сведениями.

Если вы еще мало знакомы с хранилищем данных SQL Azure, некоторые разделы могут показаться вам очень сложными.  Разделы здесь в основном расположены в порядке важности.  Если сперва вы уделите внимание нескольким первым основным понятиям, то будете подготовлены к дальнейшему материалу.  Набравшись опыта работы с хранилищем данных SQL, вы можете приступить к изучению следующих понятий.  Вам не понадобится много времени, чтобы разобраться с этими вопросами.

## <a name="reduce-cost-with-pause-and-scale"></a>Снижение расходов за счет приостановки и масштабирования ресурсов
В хранилище данных SQL предоставляется возможность приостановить ресурсы, если вы их не используете. Счет выставляется только за используемые вычислительные ресурсы.  Еще одной ключевой возможностью выступает масштабирование ресурсов.  Приостановить работу и выполнить масштабирование ресурсов можно с помощью портала Azure или команд PowerShell.  Изучите эти возможности, так как они позволяют существенно снизить расходы на хранилище данных, когда оно не используется.  Чтобы сохранять постоянную доступность хранилища данных, вместо приостановки работы можно уменьшить его размер (до размера DW100).

Дополнительные сведения см. в разделах [Приостановка работы вычислительных ресурсов][Приостановка работы вычислительных ресурсов], [Возобновление работы вычислительных ресурсов][Возобновление работы вычислительных ресурсов], [Масштабирование вычислительных ресурсов][Масштабирование вычислительных ресурсов].

## <a name="drain-transactions-before-pausing-or-scaling"></a>Очистка транзакций перед приостановкой использования и масштабированием вычислительных ресурсов
В случае приостановки или масштабирования хранилища данных SQL, когда вы отправите запрос на приостановку или масштабирование, остальные ваши запросы будут отменены.  Отмена типичного запроса SELECT выполняется очень быстро и почти не влияет на время, необходимое для приостановки работы или масштабирования экземпляра.  Однако остановка транзакционных запросов, с помощью которых изменяются данные или их структура, занимает длительное время.  **Транзакционные запросы по определению должны полностью завершиться или выполнить откат изменений.**   На откат изменений, выполненных с помощью транзакционных запросов, требуется столько же времени (или даже больше), как и на выполнение самих изменений.  Например, если отменить запрос на удаление строк, который уже выполняется в течение часа, системе понадобится столько же времени, чтобы вставить удаленные строки обратно.  Процесс приостановки работы и масштабирования ресурсов во время выполнения транзакционных запросов займет более длительное время, так как он не начнется, пока не будет выполнен откат изменений.

Дополнительные сведения см. в статьях [Транзакции в хранилище данных SQL][Транзакции в хранилище данных SQL], [Оптимизация транзакций для хранилища данных SQL][Оптимизация транзакций для хранилища транзакций SQL].

## <a name="maintain-statistics"></a>Обеспечение статистики
В отличие от SQL Server, который автоматически обнаруживает и создает или обновляет статистику в столбцах, в хранилище данных SQL этот процесс необходимо выполнять самостоятельно.  В будущем мы планируем решить эту проблему. Однако пока вам понадобится вести статистику, чтобы обеспечивать оптимизацию планов хранилища данных SQL.  Планы, созданные с помощью оптимизатора, зависят от доступной статистики.  **Проще всего приступить к работе со статистикой, создав статистику для каждого столбца.**  Не менее важно также обновлять статистику, так как данные могут быть существенно изменены.  Рекомендуется выполнять обновление статистики ежедневно или после каждой загрузки.  Однако всегда есть компромиссы между производительностью и затратами на создание и обновление статистики. Если ведение статистики занимает слишком много времени, нужно выбирать отдельные столбцы, для которых необходимо создавать статистику, или столбцы, требующие частого обновления статистики.  Например, можно ежедневно обновлять столбцы дат, в которые добавляются новые значения. **Статистику рекомендуется вести в столбцах, которые являются частью объединения, используются в предложении WHERE или GROUP BY**.

Дополнительные сведения см. в статьях [Managing statistics on tables in SQL Data Warehouse][Managing statistics on tables in SQL Data Warehouse] (Управление статистикой таблиц в хранилище данных SQL), [CREATE STATISTICS][CREATE STATISTICS] (Инструкция CREATE STATISTICS), [UPDATE STATISTICS][UPDATE STATISTICS] (Инструкция UPDATE STATISTICS).

## <a name="group-insert-statements-into-batches"></a>Объединение инструкций INSERT в группы
Одноразовую загрузку в небольшую таблицу или даже периодическую перезагрузку результатов поиска рекомендуется выполнять, используя такой синтаксис инструкции INSERT: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Однако для загрузки тысяч или миллионов строк на протяжении дня одноэлементных инструкций INSERT может быть недостаточно.  Вместо них можно создать процессы, которые будут записывать инструкции INSERT в файл и периодически загружать его.

Дополнительные сведения см. в статье [INSERT][INSERT] (Инструкция INSERT).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Быстрая загрузка и экспорт данных с помощью PolyBase
Хранилище данных SQL поддерживает загрузку и экспорт данных при помощи нескольких инструментов, включая фабрику данных Azure, PolyBase и BCP.  При работе с данными небольшого объема, что не требует высокой производительности, можно использовать любой инструмент.  Однако для загрузки или экспорта данных большого объема, что требует высокой производительности, лучше всего использовать PolyBase.  Этот инструмент использует архитектуру вычислений с массовым параллелизмом (MPP) хранилища данных SQL. Поэтому загрузка и экспорт данных с его помощью выполняется быстрее.  Загрузку данных с помощью PolyBase можно выполнить, используя команды CTAS или INSERT INTO.  **Команда CTAS позволяет свести к минимуму нагрузку ведения журнала транзакций и быстрее всего выполнить загрузку данных.**  Фабрика данных Azure также поддерживает загрузку данных с помощью PolyBase.  PolyBase поддерживает различные форматы файлов, включая формат GZIP.  **Чтобы максимально повысить пропускную способность при использовании текстовых файлов в формате GZIP, необходимо разбить файлы на 60 или больше частей. Это позволит достичь максимальной степени параллелизма загрузки.**  Кроме того, для повышения общей пропускной способности можно загружать данные одновременно.

Дополнительные сведения см. в статьях [Загрузка данных в хранилище данных Azure SQL][Загрузка данных в хранилище данных Azure SQL], [Руководство по использованию PolyBase в хранилище данных SQL][Руководство по использованию PolyBase в хранилище данных SQL], [Стратегии и шаблоны загрузки данных в хранилище данных SQL Azure][Стратегии и шаблоны загрузки данных в хранилище данных SQL Azure], [Загрузка данных с помощью фабрики данных Azure][Загрузка данных с помощью фабрики данных Azure], [Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure][Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure], [Инструкция CREATE EXTERNAL FILE FORMAT][Инструкция CREATE EXTERNAL FILE FORMAT], [Функция Create Table As Select (CTAS) в хранилище данных SQL][Функция Create Table As Select (CTAS) в хранилище данных SQL].

## <a name="load-then-query-external-tables"></a>Загрузка внешних таблиц и отправка запросов к ним
Технология Polybase (т. н. внешние таблицы), возможно, является самым быстрым способом загрузки данных, но она не оптимальна с точки зрения запросов. В настоящее время таблицы Polybase хранилища данных SQL поддерживают только файлы больших двоичных объектов Azure. Эти файлы не обслуживаются какими-либо вычислительными ресурсами.  Поэтому хранилище данных SQL не может разгрузить эту операцию, и ему требуется прочитать файл целиком, загрузив его в базу данных tempdb, чтобы считать данные.  Таким образом, если будет отправлено несколько запросов к этим данным, то лучше один раз загрузить их в локальную таблицу и предоставить ее для выполнения запросов.

Дополнительные сведения см. в [руководстве по использованию PolyBase][Guide for using PolyBase].

## <a name="hash-distribute-large-tables"></a>Хэш-распределение больших таблиц
По умолчанию таблицы распределяются по методу циклического перебора.  Это позволяет упростить процесс создания таблиц, так как пользователям не нужно принимать решение о типе распределения.  Таблицы с распределением по методу циклического перебора могут вполне годиться для некоторых рабочих нагрузок, но в большинстве случаев намного эффективнее использовать столбец распределения.  Наглядно это превосходство можно увидеть при объединении больших таблиц фактов.  Например, во время выполнения запроса на объединение таблицы Orders, распределенной по идентификатору order_id, с таблицей Transactions, распределенной по тому же идентификатору, этот запрос превращается в запрос к серверу, что исключает выполнение операций перемещения данных.  Чем меньше в запросе действий, тем быстрее он выполняется.  Скорость выполнения запроса также зависит от объема перемещаемых данных.  В этом разделе представлены только общие сведения. При загрузке распределенной таблицы входящие данные не должны быть отсортированы по ключу распределения, так как это замедлит процесс загрузки.  Ниже приведены ссылки на статьи, содержащие сведения о том, как с помощью столбца распределения можно улучшить производительность и как определить распределенную таблицу в предложение WITH инструкции CREATE TABLES.

Дополнительные сведения см. в статьях, посвященных [общим сведениям о таблицах в хранилище данных SQL][Общие сведения о таблицах в хранилище данных SQL], [распределению таблиц в хранилище данных SQL][Распределение таблиц в хранилище данных SQL], [выбору распределения таблицы][Выбор распределения таблицы], [инструкции CREATE TABLE][CREATE TABLE] и [инструкции CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="do-not-overpartition"></a>Недопущение избыточного секционирования
Не смотря на то, что секционирование данных — это эффективный способ управления данными, который реализуется благодаря переключению секций или оптимизации сканирования путем исключения секций, наличие большого количества секций может повлиять на производительность запросов.  Стратегия разделения данных на большое количество секций, как правило, эффективна в SQL Server, но она не всегда работает в хранилище данных SQL.  Слишком большое количество секций снижает эффективность кластеризованных индексов Columnstore, если в секции содержится менее миллиона строк.  Помните, что в хранилище данных SQL данные секционируются на 60 баз данных, поэтому при создании таблицы с 100 секциями фактически будет создана таблица с 6000 секций.  Все рабочие нагрузки отличаются, поэтому рекомендуется поэкспериментировать с секционированием, чтобы выбрать наиболее подходящее количество секций для вашей рабочей нагрузки.  В хранилище данных SQL можно использовать меньшее количество секций, чем в SQL Server.  Например, попробуйте использовать еженедельные или ежемесячные секции вместо ежедневных.

Дополнительные сведения см. в статье [Partitioning tables in SQL Data Warehouse][Partitioning tables in SQL Data Warehouse] (Секционирование таблиц в хранилище данных SQL).

## <a name="minimize-transaction-sizes"></a>Уменьшение размера транзакций
Инструкции INSERT, UPDATE и DELETE выполняются в транзакциях. В случае сбоя их необходимо откатить.  Чтобы сократить время выполнения отката, необходимо по возможности уменьшить размеры транзакций.  Это можно сделать, разделив инструкции INSERT, UPDATE и DELETE на части.  Например, если на выполнение инструкции INSERT требуется 1 час, рекомендуется разделить ее на 4 части. Таким образом, каждая часть будет выполняться 15 минут.  К пустым таблицам можно применять специальные операции, которые сопровождаются записью в журнал минимальных сведений, (такие как CTAS, TRUNCATE, DROP TABLE или INSERT), чтобы снизить риск отката.  Устранить откаты также можно, используя для управления данными только операции с метаданными (например, переключение секций).  Например, вместо выполнения инструкции DELETE для удаления всех строк в таблице, упорядоченной по идентификатору order_date (октябрь 2001 г.), данные можно секционировать ежемесячно, а потом переключить секцию с данными на пустую секцию из другой таблицы (см. примеры использования инструкции ALTER TABLE).  Используя инструкцию CTAS вместо DELETE, можно записать данные, которые необходимо сохранить в несекционированной таблице.  Если на выполнение этих двух инструкций требуется одинаковое количество времени, целесообразно использовать инструкцию CTAS, так как для ее выполнения необходима минимальная нагрузка ведения журнала транзакций, и ее можно быстро отменить.

Дополнительные сведения см. в статьях [Транзакции в хранилище данных SQL][Транзакции в хранилище данных SQL], [Оптимизация транзакций в хранилище данных SQL][Оптимизация транзакции в хранилище данных SQL], [Partitioning tables in SQL Data Warehouse][Partitioning tables in SQL Data Warehouse] (Секционирование таблиц в хранилище данных SQL), [TRUNCATE TABLE][TRUNCATE TABLE] (Инструкция TRUNCATE TABLE), [ALTER TABLE][ALTER TABLE] (Инструкция ALTER TABLE), [Функция Create Table As Select (CTAS) в хранилище данных SQL][Функция Create Table As Select (CTAS) в хранилище данных SQL].

## <a name="use-the-smallest-possible-column-size"></a>Использование минимального размера столбца
При определении DDL рекомендуется использовать поддерживаемый тип данных с наименьшим размером. Это позволит повысить производительность запросов.  Это особенно важно для столбцов CHAR и VARCHAR.  Если самое длинное значение в столбце состоит из 25 знаков, столбец необходимо определить как VARCHAR(25).  Не рекомендуется использовать по умолчанию длинные значения столбцов.  Кроме того, по возможности определяйте столбцы как VARCHAR, а не NVARCHAR.

Дополнительные сведения см. в статьях, посвященных [общим сведениям о таблицах в хранилище данных SQL][Overview of tables in SQL Data Warehouse], [типам данных для таблиц в хранилище данных SQL][Data types for tables in SQL Data Warehouse], [инструкции CREATE TABLE][CREATE TABLE].

## <a name="use-temporary-heap-tables-for-transient-data"></a>Использование временных таблиц без кластеризованных индексов для хранения временных данных
Если вам необходимо временно разместить данные в хранилище данных SQL, использование таблицы без кластеризованных индексов может существенно сократить время загрузки данных.  Загрузка таблицы в таблицу без кластеризованных индексов перед выполнением преобразования данных выполняется намного быстрее, чем загрузка данных в таблицу с кластеризованными индексами Columnstore.  Кроме того, загрузка данных во временную таблицу выполняется гораздо быстрее, чем загрузка таблицы в постоянное хранилище.  Временные таблицы начинаются с # и доступны только сеансам, в которых они были созданы, поэтому они могут работать в ограниченном числе сценариев.   Таблицы без кластеризованных индексов определены в предложении WITH инструкции CREATE TABLE.  При использовании временной таблицы рекомендуется создавать в ней статистику.

Дополнительные сведения см. в статьях, посвященных [временным таблицам в хранилище данных SQL][Временные таблицы в хранилище данных SQL], [инструкции CREATE TABLE][CREATE TABLE] и [инструкции CREATE TABLE AS SELECT][CREATE TABLE AS SELECT].

## <a name="optimize-clustered-columnstore-tables"></a>Оптимизация таблиц с кластеризованными индексами columnstore
Использование кластеризованных индексов Columnstore — это один из наиболее эффективных способов для хранения данных в хранилище данных SQL Azure.  По умолчанию в хранилище данных SQL используются таблицы с кластеризованными индексами Columnstore.  Качество кластеризованного сегмента Columnstore существенно влияет на эффективность выполнения запросов в таблицах с кластеризованными индексами Columnstore.  Если во время записи строк в таблицы Columnstore возникает нехватка памяти, качество сегмента Columnstore может ухудшиться.  Качество сегмента можно изменить по числу строк в сжатой группе строк.  Пошаговые инструкции по определению и улучшению качества сегмента для таблиц с кластеризованными индексами columnstore см. в разделе [Causes of poor columnstore index quality][Causes of poor columnstore index quality] (Причины низкого качества индекса Columnstore) статьи [Indexing tables in SQL Data Warehouse][Indexing tables in SQL Data Warehouse] (Индексирование таблиц в хранилище данных SQL).  Так как высококачественные сегменты columnstore важны, рекомендуется использовать идентификаторы пользователей, которым назначен класс ресурсов среднего или большого размера для загрузки данных.  Чем меньше DWU используется, тем больший размер ресурсов может понадобиться для пользователя, выполняющего загрузку. 

В основном таблицы Columnstore не передают данные в сжатый сегмент Columnstore, пока количество строк в каждой таблице не превысит миллион, а каждая таблица хранилища данных SQL не будет секционирована на 60 таблиц. Поэтому нецелесообразно применять к таблицам Columnstore запросы до тех пор, пока количество строк в таблице не превысит 60 миллионов.  Поэтому нецелесообразно применять к таблицам Columnstore запросы до тех пор, пока количество строк в таблице не превысит 60 миллионов.  Для таблицы с количеством строк менее 60 миллионов бессмысленно использовать индекс Columnstore, хотя он и не повлияет на производительность.  Более того, чтобы воспользоваться преимуществами кластеризованного индекса Columnstore при секционировании данных, каждая секция должна состоять из миллиона строк.  Если таблица состоит из 100 секций, то чтобы использовать кластеризованный индекс columnstore, она должна состоять как минимум из 6 миллиардов строк (60 распределений * 100 секций * 1 миллион строк).  Если таблица не содержит такого количества строк, рекомендуется уменьшить количество секций или использовать таблицу без кластеризованных индексов.  Чтобы получить более высокую производительность, возможно, вместо кластеризованной таблицы стоит использовать таблицу без кластеризованных индексов, содержащую вторичные индексы.  Таблицы ColumnStore еще не поддерживают вторичные индексы.

Если выбрать только необходимые столбцы, запросы к таблице ColumnStore будут выполняться быстрее.  

Дополнительные сведения см. в статье, посвященной [индексированию таблиц в хранилище данных SQL][Индексирование таблиц в хранилище данных SQL], [руководстве по индексам columnstore][Columnstore Indexes Guide], а также в разделе о [перестроении индексов для улучшения качества сегмента][Перестроение индексов для улучшения качества сегмента].

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Использование класса ресурсов большого размера для повышения производительности запросов
Чтобы выделить память для выполнения запросов, в хранилище данных SQL используются группы ресурсов.  По умолчанию для всех пользователей настроен класс ресурсов небольшого размера, предусматривающий 100 МБ памяти для каждого распределения.  В хранилище данных SQL содержится 60 распределений, на каждое из которых выделяется как минимум 100 МБ памяти. Поэтому в целом для системы выделяется 6000 МБ памяти (около 6 ГБ).  На выполнение некоторых запросов (например, на объединение таблиц с кластеризованными индексами Columnstore или добавления в них данных) требуется больше памяти.  На выполнение некоторых запросов, таких как запросы на обычное сканирование, память не требуется.  В тоже время перед перемещением всех пользователей в класс ресурсов большего размера следует учитывать, что использование такого класса влияет на параллелизм.

Дополнительные сведения см. в статье [Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL][Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL].

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Использование класса ресурсов небольшого размера для увеличения параллелизма
Если во время выполнения пользовательских запросов возникают длительные задержки, возможно, используются классы ресурсов большого размера или превышено количество одновременно используемых слотов выдачи. В таком случае запросы помещаются в очередь.  Чтобы посмотреть очередь выполнения пользовательских запросов, выполните команду `SELECT * FROM sys.dm_pdw_waits`, которая позволяет просмотреть возвращенные строки.

Дополнительные сведения см. в статьях [Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL][Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL], [sys.dm_pdw_waits][sys.dm_pdw_waits].

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Использование динамических административных представлений для отслеживания и оптимизации запросов
В хранилище данных SQL содержится несколько динамических административных представлений, которые можно использовать для отслеживания выполнения запроса.  В первой статье в списке ниже предоставлены пошаговые инструкции по отслеживанию выполнения запроса.  Чтобы быстро определить запросы в динамических административных представлениях, используйте в запросах параметр LABEL.

Дополнительные сведения см. в статьях [Мониторинг рабочей нагрузки с помощью динамических административных представлений][Мониторинг рабочей нагрузки с помощью динамических административных представлений], [Использование меток для инструментирования запросов в хранилище данных SQL][Использование меток для инструментирования запросов в хранилище данных SQL], [Предложение OPTION (Transact-SQL)][Предложение OPTION (Transact-SQL)], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits].

## <a name="other-resources"></a>Другие ресурсы:
Дополнительные сведения о распространенных проблемах и решениях см. в статье [Troubleshooting Azure SQL Data Warehouse][Troubleshooting Azure SQL Data Warehouse] (Устранение неполадок хранилища данных SQL Azure).

Если вы не нашли нужных сведений в этой статье, попробуйте использовать поиск по документации в левой части страницы, чтобы найти все документы, связанные с хранилищем данных SQL Azure.  На [форуме по хранилищу данных SQL Azure на портале MSDN][форум по хранилищу данных SQL Azure на портале MSDN] можно задать вопросы другим пользователям и разработчикам хранилища данных SQL.  Мы регулярно просматриваем этот форум и следим за тем, чтобы другие пользователи или наши специалисты ответили на интересующие вас вопросы.  Вопросы также можно задавать на [форуме по хранилищу данных SQL Azure][форум по хранилищу данных SQL Azure] на сайте Stack Overflow.

Наконец, используйте страницу [отзывов о хранилище данных SQL Azure][отзывы о хранилище данных SQL Azure], чтобы оставить запросы на функции.  Ваши отзывы и голоса за отзывы, оставленные другими пользователями, помогут нам определить, какие улучшения функций наиболее приоритетные.

<!--Image references-->

<!--Article references-->
[Создание запроса в службу поддержки]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Управление параллелизмом и рабочей нагрузкой]: ./sql-data-warehouse-develop-concurrency.md
[Инструкция Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Общие сведения о таблицах]: ./sql-data-warehouse-tables-overview.md
[Типы данных таблиц]: ./sql-data-warehouse-tables-data-types.md
[Распределение таблиц]: ./sql-data-warehouse-tables-distribute.md
[Индексы таблиц]: ./sql-data-warehouse-tables-index.md
[Причины низкого качества индекса columnstore]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[перестроением индексов columnstore]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Секционирование таблиц]: ./sql-data-warehouse-tables-partition.md
[Управление статистикой таблиц]: ./sql-data-warehouse-tables-statistics.md
[Временные таблицы]: ./sql-data-warehouse-tables-temporary.md
[руководством по использованию PolyBase]: ./sql-data-warehouse-load-polybase-guide.md
[Загрузка данных]: ./sql-data-warehouse-overview-load.md
[Перенос данных с помощью фабрики данных Azure]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[Загрузка данных с помощью фабрики данных Azure]: ./sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Загрузка данных с помощью bcp]: ./sql-data-warehouse-load-with-bcp.md
[Загрузка данных с помощью PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Мониторинг рабочей нагрузки с помощью динамических административных представлений]: ./sql-data-warehouse-manage-monitor.md
[Приостановка работы вычислительных ресурсов]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Возобновление работы вычислительных ресурсов]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Масштабирование вычислительных ресурсов]: ./sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Транзакции в хранилище данных SQL]: ./sql-data-warehouse-develop-transactions.md
[Оптимизация транзакций]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Устранение неполадок]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[Инструкция ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[Инструкция CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[Инструкция CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[Инструкция CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[Инструкция INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[Предложение OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[Инструкция TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[Инструкция UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Руководство по индексам columnstore]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Выбор распределения таблицы]: https://blogs.msdn.microsoft.com/sqlcat/2015/08/11/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[отзывов о хранилище данных SQL Azure]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[форуме по хранилищу данных SQL Azure на портале MSDN]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Форуме по хранилищу данных SQL Azure на сайте Stack Overflow]:  http://stackoverflow.com/questions/tagged/azure-sqldw
[Стратегии и шаблоны загрузки данных в хранилище данных SQL Azure]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/06/azure-sql-data-warehouse-loading-patterns-and-strategies



<!--HONumber=Nov16_HO2-->


