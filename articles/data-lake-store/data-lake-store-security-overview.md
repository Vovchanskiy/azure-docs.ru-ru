---
title: Общие сведения об обеспечении безопасности в хранилище озера данных | Microsoft Docs
description: Узнайте, почему Azure Data Lake Store более безопасно для хранения больших данных
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2016
ms.author: nitinme

---
# Обеспечение безопасности в хранилище озера данных Azure
Многие предприятия используют аналитику больших данных, чтобы получать информацию, которая поможет принимать обоснованные решения. В организациях могут быть сложные управляемые среды с растущим числом разных пользователей. Поэтому для предприятий крайне важно, чтобы ценные бизнес-данные хранились в надежном месте и определенным пользователям можно было назначать надлежащий уровень доступа к этим данным. Azure Data Lake Store разработано с учетом этих требований к безопасности. Из этой статьи вы узнаете о том, как обеспечить безопасность Data Lake Store с помощью следующих функций:

* Аутентификация
* Авторизация
* Сетевая изоляция
* Защита данных
* Аудит

## Проверка подлинности и управление удостоверениями
Проверка подлинности — это процесс, в ходе которого подтверждается удостоверение пользователя при его взаимодействии с Data Lake Store или любыми службами, которые подключаются к Data Lake Store. Чтобы управлять удостоверениями и выполнять проверку подлинности, Data Lake Store использует [Azure Active Directory](../active-directory/active-directory-whatis.md), комплексное решение для управления удостоверениями и доступом в облаке, которое упрощает управление пользователями и группами.

Каждая подписка Azure связана с одним экземпляром каталога Azure Active Directory. Доступ к учетной записи Data Lake Store могут получить только те пользователи и службы, удостоверения которых определены в службе Azure Active Directory. Доступ реализуется с помощью портала Azure, средств командной строки или клиентских приложений, созданных с помощью пакета SDK для Azure Data Lake Store. Ниже перечислены основные преимущества использования Azure Active Directory в качестве централизованного механизма управления доступом.

* Упрощенное управление жизненным циклом удостоверений. Удостоверение пользователя или службы (удостоверение субъекта-службы) можно быстро создавать и отзывать. Для этого нужно просто удалить или отключить учетную запись в каталоге.
* Многофакторная проверка подлинности. Поддержка [многофакторной проверки подлинности](../multi-factor-authentication/multi-factor-authentication.md) обеспечивает дополнительный уровень безопасности при входе пользователей в систему и осуществлении транзакций.
* Проверка подлинности с любого клиента с помощью стандартного открытого протокола, например OAuth или OpenID.
* Федерация со службами каталога предприятия и поставщиками удостоверений в облаке.

## Проверка подлинности и управление доступом
После того как пользователь прошел проверку подлинности в Azure Active Directory для доступа к Azure Data Lake Store, проверка подлинности управляет разрешениями на доступ к Data Lake Store. Data Lake Store различает авторизацию для действий, связанных с учетной записью, и для действий, связанных с данными, следующим образом.

* [Контроль доступа на основе ролей](../active-directory/role-based-access-control-what-is.md) (RBAC), предоставляемый Azure для управления учетными записями
* ACL POSIX для доступа к данным в хранилище.

### Использование RBAC для управления учетными записями
По умолчанию для Data Lake Store определены четыре основные роли. С помощью этих ролей разрешается доступ на выполнение различных операций в учетной записи Data Lake Store с использованием портала Azure, командлетов PowerShell и REST API. Роли "Владелец" и "Участник" предоставляют доступ к различным функциям администрирования учетной записи. Пользователям, которые будут только взаимодействовать с данными, можно назначить роль "Читатель".

![Роли RBAC](./media/data-lake-store-security-overview/rbac-roles.png "Роли RBAC")

Обратите внимание, что хотя эти роли назначаются для управления учетными записями, некоторые из них влияют на доступ к данным. Чтобы управлять доступом к операциям, которые пользователь может выполнять в файловой системе, используйте списки управления доступом. В следующей таблице приведен краткий обзор прав на управление и прав на доступ к данным для ролей по умолчанию.

| Роли | Права управления | Права доступа к данным | Пояснение |
| --- | --- | --- | --- |
| Роль не назначена |None |Управление с помощью ACL |Пользователь не может использовать портал Azure или командлеты PowerShell Azure для просмотра Data Lake Store. Он может использовать только средства командной строки. |
| Владелец |Все |Все |Владелец — это привилегированный пользователь. Он может управлять всем и имеет полный доступ к данным. |
| читатель. |Только для чтения |Управление с помощью ACL |Читатель может просматривать всю информацию об управлении учетными записями. Например, пользователь с такой ролью может видеть, кому какая роль назначена, но не может вносить изменения в эти данные. |
| Участник |Все, кроме добавления и удаления ролей |Управление с помощью ACL |Участник может управлять другими функциями учетной записи, например развертываниями, созданием оповещений и управлением ими, но не может добавлять или удалять роли. |
| Администратор доступа пользователей |Добавление и удаление ролей |Управление с помощью ACL |Администратор доступа пользователей может управлять доступом пользователей к учетным записям. |

Инструкции см. в разделе [Назначение пользователей или групп безопасности учетным записям хранения Azure Data Lake](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### Использование списков управления доступом для операций в файловых системах
Data Lake Store — это иерархическая файловая система (как распределенная файловая система Hadoop (HDFS)), которая поддерживает [списки управления доступом POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Она предоставляет владельцам, группе владельцев и другим пользователям и группам права на чтение (r), запись (w) и выполнение (x) операций в ресурсах. В общедоступной предварительной версии Data Lake Store (текущий выпуск) списки управления доступом можно включить в корневой папке, вложенных папках, а также отдельных файлах. Списки управления доступом, настроенные для корневой папки, также применяются ко всем дочерним папкам и файлам.

Чтобы определять списки управления доступом для нескольких пользователей, рекомендуется использовать [группы безопасности](../active-directory/active-directory-accessmanagement-manage-groups.md). Добавьте пользователей в группу безопасности, а затем назначьте этой группе список управления доступом для файла или папки. Это удобно при предоставлении пользовательского доступа, так как для него существует ограничение — не более девяти записей. Дополнительные сведения о защите данных, содержащихся в Data Lake Store, с помощью групп безопасности Azure Active Directory см. в разделе [Назначение пользователей или группы безопасности в виде ACL в файловой системе Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions).

![Перечисление стандартных и пользовательских сценариев доступа](./media/data-lake-store-security-overview/adl.acl.2.png "Перечисление стандартных и пользовательских сценариев доступа")

## Сетевая изоляция
Data Lake Store позволяет управлять доступом к хранилищу данных на уровне сети. Вы можете настроить брандмауэры и определить диапазон IP-адресов для доверенных клиентов. После определения диапазона IP-адресов к Data Lake Store смогут подключаться только клиенты с IP-адресами в пределах этого диапазона.

![Параметры брандмауэра и доступ по IP-адресу](./media/data-lake-store-security-overview/firewall-ip-access.png "Параметры брандмауэра и IP-адрес")

## Защита данных
Для организаций важно обеспечивать безопасность критически важных данных на протяжении всего жизненного цикла. Data Lake Store использует стандартный протокол TLS для защиты передаваемых данных между клиентом и Data Lake Store.

В будущих выпусках будет доступна защита неактивных данных.

## Журналы аудита и диагностики
Действия, связанные с управлением учетными записями и данными, можно просматривать в журналах аудита или журналах диагностики.

* Действия, связанные с управлением учетными записями, используют API-интерфейсы Azure Resource Manager и отображаются на портале Azure в журналах аудита.
* Действия, связанные с данными, используют REST API WebHDFS и отображаются на портале Azure в журналах диагностики.

### Журналы аудита
Чтобы обеспечить соответствие нормативным требованиям, организациям могут потребоваться журналы аудита для расследования определенного инцидента. В Data Lake Store реализована встроенная функция мониторинга и аудита, которая регистрирует все действия, связанные с управлением учетными записями.

В журналах аудита для управления учетными записями можно просматривать и выбирать нужные столбцы для регистрации. Кроме того, эти журналы можно экспортировать в службу хранилища Azure.

![Журналы аудита](./media/data-lake-store-security-overview/audit-logs.png "Журналы аудита")

### Журналы диагностики
Журнал аудита доступа к данным можно включить на портале Azure (в разделе "Параметры диагностики"). Там же вы можете создать учетную запись хранилища BLOB-объектов Azure для хранения журналов.

![Журналы диагностики](./media/data-lake-store-security-overview/diagnostic-logs.png "Журналы диагностики")

Настроив параметры диагностики, вы сможете просматривать журналы на вкладке **Журналы диагностики**.

Дополнительные сведения о работе с журналами диагностики в Azure Data Lake Store см. в статье [Доступ к журналам диагностики Azure Data Lake Store](data-lake-store-diagnostic-logs.md).

## Сводка
Корпоративным клиентам нужна безопасная и простая в использовании облачная платформа для аналитики данных. Azure Data Lake Store полностью отвечает этим требованиям благодаря интеграции с Azure Active Directory. Это позволяет реализовать управление удостоверениями и проверку подлинности, авторизацию с помощью списков управления доступом, сетевую изоляцию, шифрование данных при передаче и хранении (ожидается в будущем), а также аудит.

Если вы хотите увидеть новые функции в Data Lake Store, оставьте свой отзыв на [форуме Data Lake Store UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## Дополнительные материалы
* [Обзор хранилища озера данных Azure](data-lake-store-overview.md)
* [Начало работы с Azure Data Lake Store с помощью портала Azure](data-lake-store-get-started-portal.md)
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0914_2016-->