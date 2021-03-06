---
title: Изучение Hadoop в HDInsight с помощью коллекции примеров | Microsoft Docs
description: С помощью коллекции для начала работы с HDInsight вы можете быстро изучить Hadoop, запуская примеры приложений. Можно использовать как предоставленный пример данных или как собственный.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jgao

---
# Изучение Hadoop с помощью коллекции для начала работы с Azure HDInsight
Коллекция "Приступая к работе" доступна только для кластеров HDInsight под управлением Windows. С ее помощью можно легко и быстро изучить Hadoop, запуская примеры приложений в HDInsight. Некоторые примеры поставляются с примером данных. Для остальных примеров можно указать собственные данные. Сейчас доступно шесть следующих примеров (в будущем их будет больше):

* Решения с данными Azure
  * Анализ журналов веб-сайта Microsoft Azure
  * Аналитика службы хранилища Microsoft Azure
* Решения с примером данных
  * Анализ данных датчика
  * Анализ тенденций Twitter
  * Анализ журнала веб-сайта
  * Рекомендации по фильмам с использованием Mahout

![Решения из коллекции для начала работы с HDInsight Hadoop, Storm и HBase, включая примеры данных.][hdinsight.sample.gallery]

В следующем видео показано, как запустить пример анализа тенденций Twitter:

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.youtube.com/embed/7ePbHot1SN4></a></center>

Доступ к панели мониторинга можно получить на странице http://<YourHDInsightClusterName>.azurehdinsight.net/ или на портале Azure.

**Запуск примера из коллекции для начала работы**

1. Войдите на [портал Azure][azure.portal].
2. Щелкните **Обзор** в меню слева, щелкните **Кластеры HDInsight** и выберите имя кластера.
3. В меню вверху щелкните **Панель мониторинга**.
4. Введите имя и пароль пользователя HTTP (его также называют пользователем кластера).
5. В верхней части страницы щелкните **Коллекция для начала работы**.
6. Выберите один из примеров. К каждому примеру прилагаются подробные указания по его запуску. На следующем рисунке приведен пример анализа тенденций Twitter:
   
    ![Пример анализа тенденций Twitter в HDInsight][hdinsight.twitter.sample]

## Дальнейшие действия
HDInsight можно также изучить другими способами:

* [Карта обучения HDInsight][hdinsight.learn.map]
* [Инфографика по HDInsight][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]: https://portal.azure.com

<!---HONumber=AcomDC_0914_2016-->