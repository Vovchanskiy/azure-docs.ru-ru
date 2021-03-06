Действие копирования в фабрике данных копирует данные из хранилища-источника в хранилище-приемник. Фабрика данных поддерживает приведенные ниже хранилища данных. Данные из любого источника можно записывать в любой приемник. Щелкните название хранилища, чтобы узнать, как скопировать данные из него или в него.

| Категория | Хранилище данных | Поддерживается в качестве источника | Поддерживается в качестве приемника |
|:--- |:--- |:--- |:--- |
| Таблицы Azure |[хранилище BLOB-объектов Azure](../articles/data-factory/data-factory-azure-blob-connector.md) <br/> [Azure Data Lake Store](../articles/data-factory/data-factory-azure-datalake-connector.md) <br/> [База данных SQL Azure;](../articles/data-factory/data-factory-azure-sql-connector.md) <br/> [Хранилище данных Azure SQL](../articles/data-factory/data-factory-azure-sql-data-warehouse-connector.md) <br/> [Хранилище таблиц Azure](../articles/data-factory/data-factory-azure-table-connector.md) <br/> [Azure DocumentDB](../articles/data-factory/data-factory-azure-documentdb-connector.md) <br/> |✓ <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  |✓  <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  |
| Базы данных |[SQL Server](../articles/data-factory/data-factory-sqlserver-connector.md)\* <br/> [Oracle](../articles/data-factory/data-factory-onprem-oracle-connector.md)\* <br/> [MySQL](../articles/data-factory/data-factory-onprem-mysql-connector.md)\* <br/> [DB2](../articles/data-factory/data-factory-onprem-db2-connector.md)\* <br/> [Teradata](../articles/data-factory/data-factory-onprem-teradata-connector.md)\* <br/> [PostgreSQL](../articles/data-factory/data-factory-onprem-postgresql-connector.md)\* <br/> [Sybase](../articles/data-factory/data-factory-onprem-sybase-connector.md)\* <br/>[Cassandra](../articles/data-factory/data-factory-onprem-cassandra-connector.md)\* <br/>[MongoDB](../articles/data-factory/data-factory-on-premises-mongodb-connector.md)\*<br/>[Amazon Redshift](../articles/data-factory/data-factory-amazon-redshift-connector.md) |✓  <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓ <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  |✓  <br/> ✓  <br/> &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;<br/> &nbsp; <br/>&nbsp; |
| Файл |[Файловая система](../articles/data-factory/data-factory-onprem-file-system-connector.md)\* <br/> [HDFS](../articles/data-factory/data-factory-hdfs-connector.md)\* <br/> [Amazon S3](../articles/data-factory/data-factory-amazon-simple-storage-service-connector.md) <br/> [FTP](../articles/data-factory/data-factory-ftp-connector.md) |✓ <br/> ✓  <br/> ✓  <br/> ✓  |✓  <br/> &nbsp;<br/>&nbsp; |
| Прочее |[Salesforce](../articles/data-factory/data-factory-salesforce-connector.md)<br/> [Generic ODBC](../articles/data-factory/data-factory-odbc-connector.md)\* <br/> [Generic OData](../articles/data-factory/data-factory-odata-connector.md) <br/> [Веб-таблица (таблица на основе HTML)](../articles/data-factory/data-factory-web-table-connector.md) <br/> [GE Historian](../articles/data-factory/data-factory-odbc-connector.md#ge-historian-store)* |✓ <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  |&nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp; |

> [!NOTE]
> Хранилища данных, отмеченные звездочкой (*), могут находиться в локальном расположении или в IaaS Azure и требовать установки [шлюза управления данными](../articles/data-factory/data-factory-data-management-gateway.md) на локальном компьютере или компьютере IaaS Azure.
> 
> 



<!--HONumber=Nov16_HO2-->


