---
title: Загрузка данных из хранилища BLOB-объектов Azure в хранилище данных SQL Azure (фабрика данных Azure) | Microsoft Docs
description: Сведения о том, как загружать данные с помощью фабрики данных Azure.
services: sql-data-warehouse
documentationcenter: NA
author: lodipalm
manager: barbkess
editor: ''
tags: azure-sql-data-warehouse

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/16/2016
ms.author: lodipalm;barbkess;sonyama

---
# Загрузка данных из хранилища больших двоичных объектов Azure в хранилище данных SQL Azure (фабрика данных Azure).
> [!div class="op_single_selector"]
> * [Фабрика данных](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
> * [PolyBase;](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
> 
> 

 В этом руководстве показано, как создать конвейер в фабрике данных Azure для перемещения данных из большого двоичного объекта в хранилище Azure в хранилище данных SQL. С помощью описанных ниже шагов вы сможете:

* настроить образец данных в большом двоичном объекте службы хранилища Azure;
* подключить ресурсы к фабрике данных Azure;
* создать конвейер для перемещения данных из больших двоичных объектов службы хранилища в хранилище данных SQL.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Loading-Azure-SQL-Data-Warehouse-with-Azure-Data-Factory/player]
> 
> 

## Перед началом работы
Чтобы получить основные сведения о фабрике данных Azure, см. статью [Введение в службу фабрики данных Azure][Введение в службу фабрики данных Azure].

### Создание или определение ресурсов
Для работы с этим руководством вам потребуются следующие ресурсы.

* **Большой двоичный объект в службе хранилища Azure**. Мы будем использовать этот большой двоичный объект как источник демонстрационных данных для конвейера фабрики данных Azure, поэтому такой источник нужно создать заранее. Если у вас еще нет учетной записи хранения, [создайте ее][создайте ее].
* **Хранилище данных SQL**. Мы будем перемещать данные из большого двоичного объекта в службе хранилища Azure в хранилище данных SQL. Для этого в Интернете нам потребуется хранилище данных с демонстрационными данными AdventureWorksDW. Если у вас еще нет хранилища данных, [подготовьте его][Create a SQL Data Warehouse]. Если у вас уже есть хранилище данных, но в нем нет демонстрационных данных, [загрузите их вручную][Load sample data into SQL Data Warehouse].
* **Фабрика данных Azure**. Загрузку данных фактически будет выполнять фабрика данных Azure, поэтому она потребуется нам для создания конвейера перемещения данных. Если у вас еще нет фабрики, сведения о том, как ее создать, см. в шаге 1 статьи [Создание первой фабрики данных Azure с помощью портала Azure и редактора фабрики данных][Создание первой фабрики данных Azure с помощью портала Azure и редактора фабрики данных].
* **AZCopy**. Служебная программа AZCopy нужна, чтобы скопировать демонстрационные данные из локального клиента в большой двоичный объект в службе хранилища Azure. Инструкции по установке см. в [документации по AZCopy][документации по AZCopy].

## Шаг 1. Копирование демонстрационных данных в BLOB-объект хранилища Azure
Когда все ресурсы будут готовы, скопируйте демонстрационные данные в BLOB-объект хранилища Azure.

1. [Загрузите демонстрационные данные][Загрузите демонстрационные данные]. Они содержат пример данных о продажах, которые мы добавим наши демонстрационные данные AdventureWorksDW.
2. Чтобы скопировать данные о продажах в BLOB-объект хранилища Azure, выполните такую команду AZCopy:

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## Шаг 2. Подключение ресурсов к фабрике данных Azure.
Теперь, когда BLOB-объект содержит нужные нам данные, мы можем создать конвейер фабрики данных Azure, чтобы переместить их из хранилища BLOB-объектов Azure в хранилище данных SQL.

Для начала откройте [портал Azure][портал Azure] и в меню слева выберите свою фабрику данных.

### Шаг 2.1. Создание связанной службы
Свяжите свою учетную запись хранения Azure и хранилище данных SQL с фабрикой данных.

1. Сначала начните процесс регистрации, щелкнув в колонке фабрики данных раздел "Связанные службы" и выбрав пункт "Новое хранилище данных". Выберите имя, под которым будет зарегистрирована ваша служба хранилища Azure, выберите тип "Служба хранилища Azure", а затем введите имя и ключ своей учетной записи.
2. Чтобы зарегистрировать хранилище данных SQL, перейдите в раздел "Создать и развернуть" и последовательно щелкните "Новое хранилище данных" и "Хранилище данных SQL Azure". Скопируйте этот шаблон и добавьте в него свои данные.

```JSON
{
    "name": "<Linked Service Name>",
    "properties": {
        "description": "",
        "type": "AzureSqlDW",
        "typeProperties": {
             "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
         }
    }
}
```

### Шаг 2.2. Определение набора данных
После создания связанных служб следует определить наборы данных. Это означает, что нужно задать структуру данных, перемещаемых из вашего хранилища в хранилище данных. Вы можете прочитать об этом подробнее.

1. Сначала перейдите к разделу фабрики данных "Разработка и развертывание".
2. Чтобы привязать хранилище к фабрике данных, щелкните элемент "Создать набор данных", а затем — "Хранилище BLOB-объектов Azure". Для определения данных в хранилище BLOB-объектов Azure вы можете использовать приведенный ниже сценарий.

```JSON
{
    "name": "<Dataset Name>",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "<linked storage name>",
        "typeProperties": {
            "folderPath": "<containter name>",
            "fileName": "FactInternetSales.csv",
            "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```


1. Теперь мы настроим также набор данных, предназначенный для хранилища данных SQL. Начнем так же, то есть щелкнем элемент "Создать набор данных", а затем — "Хранилище данных SQL Azure".

```JSON
{
    "name": "DWDataset",
    "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "FactInternetSales"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

## Шаг 3. Создание и запуск конвейера
Наконец, настроим и запустим конвейер в фабрике данных Azure. Эта операция будет выполнять фактическое перемещение данных. Полный список операций, которые можно выполнять в хранилище данных SQL и фабрике данных Azure, см. [здесь][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

Теперь в разделе "Разработка и развертывание" щелкните элемент "Дополнительные команды", а затем — "Создать конвейер". Создав конвейер, вы сможете передавать данные в хранилище данных с помощью приведенного ниже кода.

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
              }
            ],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## Дальнейшие действия
Дополнительные сведения можно найти в следующих статьях.

* [Схема обучения по фабрике данных Azure][Схема обучения по фабрике данных Azure].
* [Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure][Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure]. Это основная справочная статья, посвященная использованию фабрики данных Azure с хранилищем данных SQL Azure.

В приведенных ниже статьях содержатся подробные сведения о фабрике данных Azure. В них речь идет о базах данных SQL Azure и HDinsight, но эти сведения также относятся к хранилищу данных SQL Azure.

* [Начало работы с фабрикой данных Azure][Начало работы с фабрикой данных Azure]. Это основное руководство по обработке данных с помощью фабрики данных Azure. Из него вы узнаете, как построить конвейер, в котором веб-журналы ежемесячно преобразовываются и анализируются с помощью HDInsight. Обратите внимание, что в этом руководстве копирование не используется.
* [Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных][Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных]. В этом руководстве вы создадите конвейер в фабрике данных Azure для копирования данных из BLOB-объекта хранилища Azure в базу данных SQL Azure.

<!--Image references-->

.<!--Article references-->

[документации по AZCopy]: ../storage/storage-use-azcopy.md
[Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[создайте ее]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Создание первой фабрики данных Azure с помощью портала Azure и редактора фабрики данных]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Введение в службу фабрики данных Azure]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL с помощью фабрики данных]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Начало работы с фабрикой данных Azure]: ../data-factory/data-factory-build-your-first-pipeline.md

.<!--MSDN references-->

<!--Other Web references-->
[Схема обучения по фабрике данных Azure]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[портал Azure]: https://portal.azure.com
[Загрузите демонстрационные данные]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv

<!---HONumber=AcomDC_0817_2016-->