---
title: "Руководство для Linux. Начало работы с Hadoop и Hive | Документация Майкрософт"
description: "Следуйте инструкциям этого учебника для Linux, чтобы начать работу с Hadoop в HDInsight. Узнайте, как подготовить кластеры в Linux и запросить данные с помощью Hive."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/14/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 63a4eaf9fbb76480e0617b36d15aebae03ec3da4


---
# <a name="hadoop-tutorial-get-started-using-linuxbased-hadoop-in-hdinsight"></a>Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux
> [!div class="op_single_selector"]
> * [На основе Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
> * [На основе Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
> 
> 

Узнайте, как создавать кластеры [Hadoop](http://hadoop.apache.org/) в HDInsight под управлением Linux и запускать задания Hive в HDInsight. [Apache Hive](https://hive.apache.org/) — это самый популярный компонент экосистемы Hadoop. Сейчас служба HDInsight поставляется в комплекте с 4 разными типами кластеров: [Hadoop](hdinsight-hadoop-introduction.md), [Spark](hdinsight-apache-spark-overview.md), [HBase](hdinsight-hbase-overview.md) и [Storm](hdinsight-storm-overview.md).  Каждый тип кластера поддерживает свой набор компонентов. Все 4 типа кластеров поддерживают инфраструктуру Hive. Дополнительные сведения о поддерживаемых компонентах в HDInsight см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md)  

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством вам потребуется:

* **Подписка Azure**: чтобы создать бесплатную пробную учетную запись (на один месяц), перейдите по адресу [azure.microsoft.com/free](https://azure.microsoft.com/free).

### <a name="access-control-requirements"></a>Требования к контролю доступа
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-cluster"></a>Создание кластера
Большинство заданий Hadoop — пакетные. Вы создаете кластер, выполняете несколько заданий, а затем удаляете его. В этом разделе мы создадим в HDInsight кластер Hadoop под управлением Linux, используя [шаблон Azure Resource Manager](../resource-group-template-deploy.md). Полностью настраиваемый шаблон Resource Manager упрощает создание таких ресурсов Azure, как HDInsight. Знакомство с шаблонами Resource Manager не является обязательным для работы с этим руководством. Сведения о других способах создания кластеров, а также о свойствах, используемых в этом руководстве, см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Шаблон Resource Manager, используемый в этом руководстве, расположен в общедоступном контейнере больших двоичных объектов — [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json). 

1. Нажмите следующую кнопку, чтобы войти в Azure и открыть шаблон Resource Manager на портале Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. В колонке **Параметры** заполните следующие поля.
   
    ![Шаблон Resource Manager для начала работы с HDInsight под управлением Linux на портале](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png).
   
   * **Имя кластера**: введите имя создаваемого кластера Hadoop.
   * **Имя для входа и пароль кластера**: имя для входа по умолчанию — **admin**.
   * **Имя пользователя SSH и пароль**: по умолчанию используется имя **sshuser**.  Это имя можно изменить. 
     
     При работе с этим руководством другие параметры являются необязательными. Их можно не изменять. 
     
     У каждого кластера есть зависимость учетной записи хранения для хранилища BLOB-объектов Azure. Обычно она называется учетной записью хранения по умолчанию. Кластер HDInsight должен находиться в том же регионе Azure, что и его учетная запись хранения, используемая по умолчанию. Удаление кластеров не приведет к удалению учетной записи хранения. Имя учетной записи хранения в шаблоне — это имя кластера, к которому добавлено слово store. 
3. Нажмите кнопку **ОК** , чтобы сохранить параметры.
4. В колонке **Настраиваемое развертывание** введите **имя новой группы ресурсов**, чтобы создать группу ресурсов.  Группа ресурсов — это контейнер, в который входит кластер, зависимая учетная запись хранения и т. Расположение группы ресурсов может отличаться от расположения кластера.
5. Щелкните **Условия использования**, а затем — кнопку **Создать**.
6. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Создать**. Вы увидите новый элемент под названием **Развертывание для развертывания шаблона**. Процесс создания кластера занимает около 20 минут. 
7. После создания кластера заголовок элемента меняется на имя группы ресурсов, которое вы указали. Портал автоматически открывает две колонки с кластером и параметрами кластера. 
   
   ![Параметры кластера при начале работы с HDInsight под управлением Linux](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png).
   
   Отображаются два ресурса: кластер и учетная запись хранения по умолчанию.

## <a name="run-hive-queries"></a>Выполнение запросов Hive
[Apache Hive](hdinsight-use-hive.md) — это самый популярный компонент службы HDInsight. Существует множество способов выполнения заданий Hive в HDInsight. В этом руководстве для выполнения некоторых заданий Hive вы будете использовать представление Hive Ambari из портала. Другие способы отправки заданий Hive описаны в статье [Использование Hive в HDInsight](hdinsight-use-hive.md).

1. Чтобы открыть Ambari, перейдите по ссылке **https://&lt;ClusterName>.azurehdinsight.net**, где &lt;ClusterName> — это кластер, который вы создали в предыдущем разделе.
2. Введите имя пользователя Hadoop и пароль, указанные в предыдущем разделе. Имя пользователя по умолчанию — **admin**.
3. Откройте **представление Hive** , как показано на снимке экрана ниже:
   
    ![Выбор представлений Ambari](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. На странице в разделе **Редактор запросов** вставьте в рабочий лист следующие инструкции HiveQL:
   
        SHOW TABLES;
   
   > [!NOTE]
   > Для Hive требуется точка с запятой.       
   > 
   > 
5. Нажмите **Execute (Выполнить)**. Под окном редактора запросов появится раздел **Query Process Results** (Результаты обработки запроса), содержащий сведения о выполнении задания. 
   
    После выполнения запроса в разделе **Query Process Results** (Результаты обработки запроса) будут отображены результаты операции. Вы увидите одну таблицу с именем **hivesampletable**. Этот пример таблицы Hive входит в состав всех кластеров HDInsight.
   
    ![Представления Hive в HDInsight](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png).
6. Повторите шаги 4 и 5 и выполните следующий запрос.
   
        SELECT * FROM hivesampletable;
   
   > [!TIP]
   > Обратите внимание на раскрывающийся список **Сохранить результаты**, расположенный в разделе **Query Process Results** (Результаты обработки запроса) слева вверху. Этот список можно использовать для скачивания или сохранения результатов в хранилище HDInsight в формате CSV-файла.
   > 
   > 
7. Щелкните **журнал** для получения списка заданий.

Когда задание Hive будет завершено, вы сможете [экспортировать результаты в базу данных SQL Azure или базу данных SQL Server](hdinsight-use-sqoop-mac-linux.md) либо [визуализировать их с помощью Excel](hdinsight-connect-excel-power-query.md). Дополнительные сведения об использовании Hive в HDInsight см. в статье [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-the-tutorial"></a>Очистка учебника
После завершения работы с этим руководством кластер можно удалить. В случае с HDInsight ваши данные хранятся в службе хранилища Azure, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Поскольку стоимость кластера во много раз превышает стоимость хранилища, экономически целесообразно удалять неиспользуемые кластеры. 

> [!NOTE]
> С помощью [фабрики данных Azure](hdinsight-hadoop-create-linux-clusters-adf.md) можно создать кластеры HDInsight по требованию, а также настроить параметр TimeToLive, чтобы автоматически удалять кластеры. 
> 
> 

**Удаление кластера и/или учетной записи хранения по умолчанию**

1. Войдите на [портал Azure](https://portal.azure.com).
2. На панели мониторинга щелкните элемент с именем группы ресурсов, с помощью которой вы создали кластер.
3. Щелкните **Удалить** в колонке ресурсов, чтобы удалить группу ресурсов, которая содержит кластер и учетную запись хранения по умолчанию. Или щелкните имя кластера в элементе **Ресурсы** и щелкните **Удалить** в колонке кластера. Обратите внимание, что удаление группы ресурсов приведет к удалению учетной записи хранения. Если вы хотите сохранить учетную запись хранения, удалите только кластер.

## <a name="next-steps"></a>Дальнейшие действия
В этом руководстве вы узнали, как с помощью шаблона Resource Manager создать кластер HDInsight под управлением Linux и как выполнять базовые запросы Hive.

Дополнительные сведения об анализе данных с помощью HDInsight см. в следующих статьях.

* Дополнительные сведения об использовании Hive с HDInsight, включая выполнение запросов Hive из Visual Studio, см. в статье [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j][hdinsight-use-hive].
* Дополнительные сведения о Pig, языке, который используется для преобразования данных, см. в статье [Использование Pig с Hadoop в HDInsight][hdinsight-use-pig].
* Дополнительные сведения о MapReduce, способе записи программ, которые обрабатывают данные в Hadoop, см. в статье [Использование MapReduce в Hadoop в HDInsight][hdinsight-use-mapreduce].
* Дополнительные сведения об анализе данных в HDInsight с помощью средств HDInsight для Visual Studio см. в статье [Приступая к работе с инструментами Hadoop в Visual Studio для HDInsight для выполнения запроса Hive](hdinsight-hadoop-visual-studio-tools-get-started.md).

Если вы готовы приступить к работе с собственными данными и хотите узнать больше о том, как HDInsight сохраняет данные или как получать данные в HDInsight, см. следующие статьи:

* Информацию о том, как HDInsight использует хранилище BLOB-объектов Azure, см. в [этой статье](hdinsight-hadoop-use-blob-storage.md).
* Сведения об отправке данных в HDInsight см. в статье [Отправка данных для заданий Hadoop в HDInsight][hdinsight-upload-data].

Дополнительные сведения о создании кластера HDInsight и управлении этим кластером см. в следующих статьях:

* Сведения об управлении кластером HDInsight под управлением Linux см. в статье [Управление кластерами HDInsight с помощью веб-интерфейса Ambari](hdinsight-hadoop-manage-ambari.md).
* Дополнительные сведения о параметрах, которые можно выбрать при создании кластера HDInsight, см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Если вы уже знакомы с Linux и Hadoop, но хотите узнать об особенностях Hadoop в HDInsight, см. статью [Сведения об использовании HDInsight в Linux](hdinsight-hadoop-linux-information.md). Она содержит следующую информацию:
  
  * URL-адреса для служб, размещенных в кластере, например Ambari и WebHCat;
  * расположение файлов Hadoop и примеры в локальной файловой системе;
  * использование хранилища Azure (WASB) вместо HDFS в качестве хранилища данных по умолчанию.

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png



<!--HONumber=Nov16_HO2-->


