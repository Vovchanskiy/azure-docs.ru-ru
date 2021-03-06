---
title: Перемещение данных в файловую систему и из нее | Microsoft Docs
description: Узнайте, как перемещать данные в локальную файловую систему и из нее с помощью фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: jingwang

---
# <a name="move-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Перемещение данных в локальную файловую систему или из нее с помощью фабрики данных Azure
В этой статье описано, как использовать действие копирования в фабрике данных Azure для перемещения данных в локальную файловую систему и из нее. Для получения списка хранилищ, которые можно использовать как источники и приемники для локальной файловой системы, см. раздел [Поддерживаемые хранилища данных](data-factory-data-movement-activities.md#supported-data-stores). Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md) , в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Фабрика данных поддерживает обмен данными с локальной файловой системой через шлюз управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке.

> [!NOTE]
> Для обмена данными с файловой системой устанавливать какие-либо двоичные файлы, кроме шлюза управления данными, не требуется.
> 
> Советы по устранению неполадок, связанных со шлюзом или подключением, см. в разделе [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshoot-gateway-issues).
> 
> 

## <a name="linux-file-share"></a>Файловый ресурс Linux
Чтобы использовать файловый ресурс Linux со связанной службой файлового сервера, сделайте следующее.

* Установите [Samba](https://www.samba.org/) на сервер Linux.
* Установите и настройте шлюз управления данными на сервере Windows. Установка шлюза управления данными на сервер Linux не поддерживается.

## <a name="copy-wizard"></a>Мастер копирования
Самый простой способ создать конвейер, копирующий данные в локальную файловую систему или из нее, — использовать мастер копирования. Краткое пошаговое руководство представлено в разделе [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md).

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). В них показано, как копировать данные в локальную файловую систему и хранилище BLOB-объектов Azure и из них. Тем не менее вы можете скопировать данные *непосредственно* из любых источников в любые из приемников, перечисленных в разделе [Поддерживаемые источники и приемники](data-factory-data-movement-activities.md#supported-data-stores), с помощью действия копирования в фабрике данных Azure.

## <a name="sample:-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Пример. Копирование данных из локальной файловой системы в хранилище BLOB-объектов Azure
В этом примере показано, как скопировать данные из локальной файловой системы в хранилище BLOB-объектов Azure.

Пример содержит следующие сущности фабрики данных.

* Связанная служба типа [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties).
* Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
* Входной [набор данных](data-factory-create-datasets.md) типа [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties).
* Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В следующем примере данные временных рядов каждый час копируются из локальной файловой системы в хранилище BLOB-объектов Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

В первую очередь настройте шлюз управления данными, как указано в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

**Связанная служба локального файлового сервера**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

Мы рекомендуем использовать свойство **encryptedCredential** вместо свойств **userid** и **password**. Подробные сведения о связанной службе файлового сервера см. в [соответствующем разделе](#onpremisesfileserver-linked-service-properties).

**Связанная служба хранилища Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Входной набор данных локальной файловой системы**

Данные берутся из нового файла каждый час. Свойства folderPath и fileName определяются с учетом времени начала среза.  

Если задано `"external": "true"`, то фабрика данных считает эту таблицу внешней, то есть не созданной в результате какого-либо действия в фабрике данных.

    {
      "name": "OnpremisesFileSystemInput",
      "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ]
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

**Выходной набор данных хранилища BLOB-объектов Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Действие копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **FileSystemSource**, а для типа **sink** — значение **BlobSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T19:00:00",
        "description":"Pipeline for copy activity",
        "activities":[  
          {
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "OnpremisesFileSystemInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "FileSystemSource"
              },
              "sink": {
                "type": "BlobSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## <a name="sample:-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Пример. Копирование данных из базы данных SQL Azure в локальную файловую систему
В примере ниже используется следующее:

* Связанная служба типа AzureSqlDatabase.
* Связанная служба типа OnPremisesFileServer.
* Входной набор данных типа AzureSqlTable.
* Выходной набор данных типа FileShare.
* Конвейер с действием копирования, в котором используются SqlSource и FileSystemSink.

В этом примере данные временного ряда каждый час копируются из таблицы SQL Azure в локальную файловую систему. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба SQL Azure**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Связанная служба локального файлового сервера**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

Мы рекомендуем использовать свойство **encryptedCredentia** вместо свойств **userid** и **password**. Подробные сведения о связанной службе файловой системы см. в [соответствующем разделе](#onpremisesfileserver-linked-service-properties).

**Входной набор данных SQL Azure**

В примере предполагается, что таблица MyTable уже создана в базе данных SQL Azure и содержит столбец timestampcolumn для данных временных рядов.

Если задано ``“external”: ”true”``, то фабрика данных считает эту таблицу внешней, то есть не созданной в результате какого-либо действия в фабрике данных.

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
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

**Выходной набор данных локальной файловой системы**

Данные копируются в новый файл каждый час. Свойства folderPath и fileName для большого двоичного объекта определяются с учетом времени начала среза.

    {
      "name": "OnpremisesFileSystemOutput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ]
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

**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **SqlSource**, а для типа **sink** — значение **FileSystemSink**. SQL-запрос, указанный для свойства **SqlReaderQuery**, выбирает для копирования данные за последний час.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T20:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "OnpremisesFileSystemOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "FileSystemSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 3,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## <a name="on-premises-file-server-linked-service-properties"></a>Свойства связанной службы локального файлового сервера
Вы можете связать локальную файловую систему с фабрикой данных Azure при помощи связанной службы локального файлового сервера. В таблице ниже приведены описания элементов JSON, которые относятся к связанной службе локального файлового сервера.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Убедитесь, что свойству type присвоено значение **OnPremisesFileServer**. |Да |
| host |Задает корневой путь к папке, которую необходимо скопировать. Чтобы указать специальные знаки в строке, используйте escape-символ "\". Примеры приведены в разделе [Примеры определений связанной службы и набора данных](#sample-linked-service-and-dataset-definitions). |Да |
| userid |Укажите идентификатор пользователя, имеющего доступ к серверу. |Нет (если выбрать encryptedcredential) |
| пароль |Укажите пароль для пользователя (userid). |Нет (если выбрать encryptedcredential) |
| encryptedCredential |Укажите зашифрованные учетные данные. Чтобы их получить, выполните командлет New-AzureRmDataFactoryEncryptValue. |Нет (если имя пользователя и пароль указываются в виде обычного текста) |
| gatewayName |Задает имя шлюза, который следует использовать фабрике данных для подключения к локальному файловому серверу. |Да |

Дополнительную информацию о настройке учетных данных для локальной файловой системы в качестве источника данных см. в разделе [Настройка учетных данных и безопасность](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security).

### <a name="sample-linked-service-and-dataset-definitions"></a>Примеры определений связанной службы и набора данных
| Сценарий | Размещение в определении связанной службы | Путь к файлу в определении набора данных |
| --- | --- | --- |
| Локальная папка на компьютере со шлюзом управления данными. <br/><br/>Примеры: "D:\\\\*" или "D:\папка\вложенная_папка\\\*" |"D:\\\\" (для шлюза управления данными 2.0 и более поздних версий) <br/><br/> localhost (для версий, предшествующих версии 2.0 шлюза управления данными) |.\\\\ или "папка\\\\вложенная_папка" (для шлюза управления данными 2.0 и более поздних версий) <br/><br/>"D:\\\\" или "D:\\\\папка\\\\вложенная_папка" (для шлюза версии ниже 2.0) |
| Удаленная общая папка: <br/><br/>Примеры: "\\\\сервер\\общая_папка\\\*" или "\\\\сервер\\общая_папка\\папка\\вложенная_папка\\*" |\\\\\\\\сервер\\\\общая_папка |.\\\\ или "папка\\\\вложенная_папка" |

**Как узнать версию шлюза**

1. На своем компьютере запустите [диспетчер конфигурации шлюза управления данными](data-factory-data-management-gateway.md#data-management-gateway-configuration-manager) .
2. Перейдите на вкладку **Справка** .

> [!NOTE]
> Мы рекомендуем вам [обновить свой шлюз управления данными до версии 2.0 и выше](data-factory-data-management-gateway.md#update-data-management-gateway), чтобы получить преимущества последних функций и исправлений.
> 
> 

**Пример указания имени пользователя и пароля в виде обычного текста**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

**Пример использования encryptedcredential**

    {
      "Name": " OnPremisesFileServerLinkedService ",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "D:\\",
          "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
          "gatewayName": "mygateway"
        }
      }
    }

## <a name="on-premises-file-system-dataset-type-properties"></a>Свойства типа "Набор данных локальной файловой системы"
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных в фабрике данных Azure](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных.

Разделы typeProperties для каждого типа набора данных отличаются. В этом разделе указываются такие сведения, как расположение и формат данных в хранилище данных. Раздел typeProperties набора данных типа **FileShare** содержит следующие свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| folderPath |Указывает вложенный путь к папке. Чтобы указать специальные знаки в строке, используйте escape-символ "\". Примеры приведены в разделе [Примеры определений связанной службы и набора данных](#sample-linked-service-and-dataset-definitions).<br/><br/>Вы можете использовать это свойство вместе с параметром **partitionBy**, чтобы в путях к папкам учитывались дата и время начала и окончания среза. |Да |
| fileName |Укажите имя файла в папке **folderPath** , если таблица должна ссылаться на определенный файл в папке. Если этому свойству не присвоить значение, таблица будет указывать на все файлы в папке.<br/><br/>Если свойство fileName не указано для выходного набора данных, то имя созданного файла будет иметь следующий формат: <br/><br/>`Data.<Guid>.txt` (пример: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Нет |
| partitionedBy |С помощью свойства partitionedBy можно указать динамические значения folderPath и fileName для данных временного ряда. Например, можно параметризовать значение folderPath для каждого часа получения данных. |Нет |
| Формат |Поддерживаются следующие типы формата: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat** и **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах [Определение TextFormat](#specifying-textformat), [Определение AvroFormat](#specifying-avroformat), [Указание JsonFormat](#specifying-jsonformat), [Указание OrcFormat](#specifying-orcformat) и [Указание ParquetFormatt](#specifying-parquetformat). Если требуется скопировать файлы между файловыми хранилищами "как есть" (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных. |Нет |
| fileFilter |Укажите фильтр для выбора подмножества файлов из folderPath. Фильтр дает возможность выбирать только некоторые файлы, а не все. <br/><br/>Допустимые значения: `*` (несколько знаков) и `?` (один знак).<br/><br/>Пример 1: "fileFilter": "*.log"<br/>Пример 2: "fileFilter": 2014-1-?.txt"<br/><br/>Обратите внимание, что свойство fileFilter применяется к входному набору данных FileShare. |Нет |
| compression |Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate** и **BZip2**. Поддерживаемые уровни: **Optimal** и **Fastest**. Сейчас для данных в формате **AvroFormat** или **OrcFormat** параметры сжатия не поддерживаются. Дополнительные сведения см. в разделе [Поддержка сжатия](#compression-support). |Нет |

> [!NOTE]
> Свойства filename и fileFilter невозможно использовать одновременно.
> 
> 

### <a name="using-partitionedby-property"></a>Использование свойства partitionedBy
Как сказано выше, для данных временных рядов путь к папке (folderPath) и имя файла (fileName) можно указывать динамически. Это делается с помощью свойства partitionedBy. Вы можете это сделать при помощи макроса фабрики данных и системных переменных SliceStart и SliceEnd, которые определяют логический период времени заданного среза данных.

Дополнительные сведения о наборах данных временных рядов, планировании и срезах см. в статьях [Наборы данных в фабрике данных Azure](data-factory-create-datasets.md), [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md) и [Конвейеры и действия в фабрике данных Azure: создание конвейеров, цепочки действий и расписаний для них](data-factory-create-pipelines.md).

#### <a name="sample-1:"></a>Пример 1
    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

В этом примере {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате ГГГГММДДЧЧ. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например: wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2:"></a>Пример 2
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

В этом примере год, месяц, день и время SliceStart извлекаются в отдельные переменные, используемые в свойствах folderPath и fileName.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="file-share-copy-activity-type-properties"></a>Свойства типа действия копирования для общей папки
**FileSystemSource** поддерживает следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| recursive |Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. |True, False (по умолчанию) |Нет |

**FileSystemSink** поддерживает следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| copyBehavior |Это свойство определяет поведение функции копирования, когда в качестве источника используется BlobSource или FileSystem. |/ **PreserveHierarchy:** сохраняет иерархию файлов в целевой папке. То есть относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><br/>**FlattenHierarchy**: все файлы из исходной папки создаются на первом уровне в целевой папке. Целевые файлы создаются с автоматически сформированными именами.<br/><br/>**MergeFiles**: объединяет все файлы из исходной папки в один файл. Если указано имя большого двоичного объекта или имя файла, то оно присваивается объединенному файлу. В противном случае присваивается автоматически созданное имя файла. |Нет |

### <a name="recursive-and-copybehavior-examples"></a>Примеры recursive и copyBehavior
В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений свойств recursive и copyBehavior.

| Значение recursive | Значение copyBehavior | Результаты выполнения операции |
| --- | --- | --- |
| Да |preserveHierarchy |Если у исходной папки "Папка1" следующая структура:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается с такой же структурой, как и исходная папка.<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 |
| Да |flattenHierarchy |Если у исходной папки "Папка1" следующая структура:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл3"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл4"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл5" |
| Да |mergeFiles |Если у исходной папки "Папка1" следующая структура:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1", "Файл2", "Файл3", "Файл4" и "Файл5" объединяется в один файл с автоматически созданным именем. |
| нет |preserveHierarchy |Если у исходной папки "Папка1" следующая структура:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| нет |flattenHierarchy |Если у исходной папки "Папка1" следующая структура:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| нет |mergeFiles |Если у исходной папки "Папка1" следующая структура:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1" и "Файл2" объединяется в один файл с автоматически созданным именем.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Производительность и настройка
 Ознакомьтесь с [руководством по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в котором описываются ключевые факторы, влияющие на производительность перемещения данных (действия копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

<!--HONumber=Oct16_HO2-->


