---
title: "Что такое HBase в HDInsight? | Документация Майкрософт"
description: "Введение в Apache HBase в HDInsight — базу данных NoSQL на основе Hadoop. Изучите варианты использования и сравните HBase с другими кластерами Hadoop."
keywords: "nosql, bigtable, что такое hbase"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: d2a76d53-133a-4849-a30c-88d9c794391c
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/14/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 64da5705f6b5978b53281bb7c6b6b295cc954dc6


---
# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtablelike-capabilities-for-hadoop"></a>Что такое HBase в HDInsight: база данных NoSQL, которая предоставляет возможности, схожие BigTable, для Hadoop
Apache HBase — это база данных NoSQL с открытым кодом, созданная на основе Hadoop и смоделированная после Google BigTable. HBase обеспечивает прямой доступ и строгую согласованность для больших объемов неструктурированных и слабоструктурированных данных, упорядоченных в семейства столбцов.

Данные хранятся в строках таблицы, данные в строке группируются по семейству столбцов. База данных HBase не имеет схемы в том смысле, что ни столбцы, ни типы хранимых в них данных не нужно определять до использования. Открытый код линейно масштабируется, чтобы обрабатывать петабайты данных на тысячах узлов. Он может полагаться на избыточность данных, пакетную обработку и другие особенности, которые предусмотрены распределенными приложениями в экосистеме Hadoop.

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Как HBase реализована в Azure HDInsight?
HDInsight HBase предлагается в форме управляемого кластера, интегрированного в среду Azure. Кластеры настроены на хранение данных непосредственно в хранилище больших двоичных объектов Azure, что обеспечивает небольшую задержку и повышенную гибкость в выборе соотношения производительности и стоимости. Это позволяет клиентам создавать интерактивные веб-сайты, работающие с большими наборами данных, создавать службы, которые хранят данные с датчиков и телеметрию миллионов конечных точек, и анализировать эти данные с помощью заданий Hadoop. HBase и Hadoop являются хорошими отправными точками для создания проекта Azure, работающего с большими данными. В частности, это позволит приложениям реального времени работать с большими наборами данных.

Реализация HDInsight использует масштабируемую архитектуру HBase для обеспечения автоматического сегментирования таблиц, строгой согласованности для чтения и записи, а также автоматического перехода на другой ресурс. Производительность повышается за счет кэширования операций чтения в памяти и потоковой записи с высокой пропускной способностью Также для HDInsight HBase доступна подготовка виртуальных сетей. Дополнительные сведения см. Дополнительные сведения см. в статье [Создание кластеров HBase в виртуальной сети Azure][hbase-provision-vnet].

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Как происходит управление данными в HDInsight HBase?
Управление данными в HBase может осуществляться с помощью команд `create`, `get`, `put` и `scan` из оболочки HBase. Данные записываются в базу данных с использованием команды `put` и считываются с помощью команды `get`. Команда `scan` используется для получения данных из нескольких строк таблицы. Данными также можно управлять с использованием интерфейса HBase API для C#, для которого имеется клиентская библиотека, работающая поверх HBase REST API. Запросы к базе данных HBase также могут осуществляться с помощью Hive. Начальные сведения об этих моделях программирования приведены в статье [Руководство по HBase. Приступая к работе с Apache HBase на Hadoop под управлением Windows в HDInsight][hbase-get-started]. Также доступны сопроцессоры, что позволяет обрабатывать данные в узлах, на которых размещена база данных.

## <a name="scenarios-use-cases-for-hbase"></a>Сценарии: варианты использования HBase
Классическим примером использования (для которого, собственно и была создана технология BigTable и HBase как ее расширение) является веб-поиск. Поисковые системы строят индексы, которые сопоставлены терминам, содержащимся на веб-страницах. Но есть много других вариантов использования, для которых подходит HBase; некоторые из них перечислены в этом разделе.

* Хранилище ключ-значение
  
    HBase можно использовать как хранилище пар «ключ-значение», оно подходит для управления системами обмена сообщениями. Facebook использует HBase для системы обмена сообщениями, она идеально подходит для хранения и управления интернет-коммуникациями. WebTable использует HBase для поиска и управления таблицами, извлеченными из веб-страниц.
* Данные от датчиков
  
    HBase удобно использовать для записи данных, накопленных при сборе из различных источников. К ним относятся социальные аналитики, временные ряды, обновления интерактивных информационных панели и счетчиков, а также управление системами ведения журналов аудита. Примеры включают в себя терминал для биржевой торговли Bloomberg и база данных Open Time Series Database (OpenTSDB), которая сохраняет и предоставляет доступ к показателям работоспособности серверных систем.
* Запросы в режиме реального времени
  
    [Phoenix](http://phoenix.apache.org/) система запросов SQL для Apache HBase. Доступ к системе осуществляется с помощью драйвера JDBC, она позволяет создавать запросы и управлять таблицами HBase с использованием SQL.
* HBase как платформа
  
    Поверх HBase могут выполняться приложения, используя ее как хранилище данных. Например, это используется в Phoenix, OpenTSDB, Kiji и Titan. Также возможна интеграция приложений с HBase. Примеры: Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia и Drill.

## <a name="a-namenextstepsanext-steps"></a><a name="next-steps"></a>Дальнейшие действия
* [Руководство по HBase. Приступая к работе с Apache HBase на Hadoop под управлением Windows в HDInsight][hbase-get-started]
* [Создание кластеров HBase в виртуальной сети Azure][hbase-provision-vnet]
* [Настройка репликации HBase в HDInsight](hdinsight-hbase-geo-replication.md)
* [Анализ мнений пользователей Twitter в режиме реального времени с использованием HBase в HDInsight][hbase-twitter-sentiment]
* [Использование Maven для выполнения сборки приложений Java, которые используют HBase с HDInsight (Hadoop) под управлением Windows][hbase-build-java-maven]

## <a name="a-nameseealsoasee-also"></a><a name="see-also"></a>Дополнительные материалы
* [Apache HBase](https://hbase.apache.org/)
* [Bigtable: распределенная система хранения структурированных данных](http://research.google.com/archive/bigtable.html)

[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/



<!--HONumber=Nov16_HO2-->


