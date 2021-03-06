---
title: Анализ производительности запросов базы данных Azure SQL
description: Мониторинг производительности запросов определяет запросы, максимально использующие ресурсы процессора, в базе данных SQL Azure.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: monicar

ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/09/2016
ms.author: sstein

---
# Анализ производительности запросов базы данных Azure SQL
Управление производительностью реляционных баз данных и ее настройка являются сложной задачей, требующей значительного опыта и времени. Анализ производительности запросов позволяет тратить меньше времени на устранение неполадок с производительностью базы данных, предоставляя следующие возможности:

* Более глубокое понимание потребления ресурсов базы данных (DTU).
* Запросы, максимально использующие ресурсы процессора, имеющие максимальную длительность или число выполнений, потенциально можно настроить, чтобы повысить производительность.
* Возможность перейти к подробным сведениям о запросе, просмотреть его текст и журнал использования ресурсов.
* Заметки к настройке производительности, отображающие действия [помощника по базам данных SQL Azure](sql-database-advisor.md).

​

## Предварительные требования
* Анализ производительности запросов доступен только с базой данных SQL Azure версии 12.
* Для анализа производительности запросов в базе данных должно быть активно [хранилище запросов](https://msdn.microsoft.com/library/dn817826.aspx). Если хранилище запросов не запущено, портал подаст запрос на его включение.

## Разрешения
Для использования анализа производительности запросов необходимо [управление доступом на основе ролей](../active-directory/role-based-access-control-configure.md) с такими разрешениями:

* разрешения **читатель**, **владелец**, **участник**, **участник базы данных SQL** или **участник сервера SQL Server** для просмотра основных ресурсов, использующих запросы и диаграммы;
* разрешения **владелец**, **участник**, **участник базы данных SQL** или **участник сервера SQL Server** для просмотра текста запросов.

## Использование анализа производительности запросов
Анализ производительности запросов легко использовать:

* Откройте [портал Azure](https://portal.azure.com/) и найдите базу данных, которую необходимо проверить.
  * В меню слева, в разделе поддержки и устранения неполадок, выберите "Анализ производительности запросов".
* На первой вкладке просмотрите список самых ресурсоемких запросов.
* Выберите отдельный запрос, чтобы просмотреть сведения о нем.
* Откройте [помощник по базам данных SQL Azure](sql-database-advisor.md) и проверьте наличие рекомендаций.
* С помощью ползунков или значков масштабирования можно изменить наблюдаемый интервал.
  
    ![панель мониторинга производительности](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Хранилище запросов должно сохранить несколько часов данных, чтобы база данных SQL могла предоставить анализ производительности запросов. Если в базе данных нет активности или хранилище запросов было неактивно в течение определенного времени, диаграммы не будут содержать данные этого периода. Можно включить хранилище запросов в любой момент, если оно не запущено.
> 
> 

## Просмотр запросов, максимально использующих ресурсы процессора
На [портале](http://portal.azure.com) выполните следующие действия.

1. Перейдите к базе данных SQL и щелкните **Все параметры** > **Поддержка и устранение неполадок** > **Анализ производительности запросов**.
   
    ![Анализ производительности запросов][1]
   
    Откроется представление популярных запросов и список запросов, наиболее активно использующих ресурсы процессора.
2. Щелкните диаграмму, чтобы просмотреть подробные сведения.<br>Верхняя строка показывает общую долю DTU в процентах для базы данных, а полосы — долю ресурсов ЦП в процентах, использованных выбранными запросами за нужный период времени (например, если выбрана **прошлая неделя**, каждая полоса соответствует одному дню).
   
    ![наиболее ресурсоемкие запросы][2]
   
    В приведенной ниже таблице представлены сводные данные по видимым запросам.
   
   * Идентификатор запроса — уникальный идентификатор запроса внутри базы данных.
   * Загрузка ЦП на запрос за период наблюдения (зависит от статистической функции).
   * Длительность запроса (зависит от статистической функции).
   * Общее число выполнений определенного запроса.
     
     С помощью флажков выделите отдельные запросы или отмените их выделение, чтобы включить их в диаграмму или исключить из нее.
3. Если данные устарели, нажмите кнопку **Обновить**.
4. Можно использовать ползунки и кнопки масштабирования, чтобы изменить интервал наблюдения и изучить пики. ![Параметры](./media/sql-database-query-performance/zoom.png)
5. При необходимости, если требуется другое представление, можно выбрать вкладку **Пользовательские** и задать:
   
   * метрику (ЦП, длительность, число выполнений);
   * интервал времени (последние 24 часа, за прошлую неделю, за прошлый месяц);
   * количество запросов;
   * статистическую функцию.
     
     ![Параметры](./media/sql-database-query-performance/custom-tab.png)

## Просмотр подробных сведений для отдельного запроса
Чтобы просмотреть сведения о запросе, выполните следующие действия.

1. Щелкните любой запрос в списке популярных запросов.
   
    ![сведения](./media/sql-database-query-performance/details.png)
2. Откроется представление сведений, в котором использование ресурсов ЦП, длительность и число выполнений для запросов разбито по времени.
3. Щелкните возле диаграммы, чтобы получить дополнительные сведения.
   
   * На верхней диаграмме показана строка с общей долей DTU базы данных в процентах, а в столбцах отображается доля ресурсов ЦП в процентах, затраченных на выполнение выбранного запроса.
   * Вторая диаграмма отображает общую длительность выполнения выбранного запроса.
   * На нижней диаграмме отображено общее число выполнений выбранного запроса.
     
     ![сведения о запросе][3]
4. Вы можете использовать ползунки и кнопки масштабирования или щелкнуть **Параметры**, чтобы настроить отображение данных запроса или выбрать другой период времени.

## Просмотр запросов с наибольшей длительностью
В последнем обновлении анализа производительности запросов были представлены две новых метрики, которые помогут выявить потенциальные узкие места: "Длительность" и "Число выполнений".<br>

Длительные запросы с большой вероятностью могут блокировать ресурсы на долгий период, блокировать работу других пользователей и ограничивать масштабируемость. Они также являются лучшими кандидатами для оптимизации.<br>

Определение длительных запросов:

1. Откройте вкладку **Пользовательские** в колонке "Анализ производительности запросов" для выбранной базы данных.
2. Выберите метрики **Длительность**.
3. Выберите количество запросов и интервал наблюдения.
4. Выберите статистическую функцию.
   
   * **Сумма** суммирует время выполнения запроса на протяжении всего интервала наблюдения.
   * **Максимум** находит запросы, время выполнения которых было максимальным на всем интервале наблюдения.
   * **Среднее** находит среднее время выполнения для всех выполненных запросов и показывает наибольшее из этих средних значений.
     
     ![Длительность запроса][4]

## Просмотр запросов с наибольшим числом выполнений
Большое число выполнений может не влиять на саму базу данных, и использование ресурсов может быть небольшим, но общая производительность приложения может понизиться.

В некоторых случаях очень большое число выполнений может привести к увеличению круговых путей в сети. Круговые пути существенно влияют на производительность. Они обуславливают задержки в сети и задержки подчиненного сервера.

Например, многие управляемые данными веб-сайты интенсивно обращаются к базе данных при каждом запросе пользователя. Хотя организация пулов подключений помогает решить проблему, увеличение сетевого трафика и рабочей нагрузки на сервер базы данных может отрицательно сказаться на производительности. Общий совет — свести круговые пути к абсолютному минимуму.

Определение часто выполняемых ("активных") запросов:

1. Откройте вкладку **Пользовательские** в колонке "Анализ производительности запросов" для выбранной базы данных.
2. Выберите метрики **Число выполнений**.
3. Выберите количество запросов и интервал наблюдения.
   
    ![Число выполнений запроса][5]

## Основные сведения о заметках к настройке производительности
При изучении рабочей нагрузки в колонке "Анализ производительности запросов" в верхней части диаграммы можно заметить значки с вертикальной линией.<br>

Эти значки являются заметками. Они представляют действия, влияющие на производительность, которые выполняет [помощник по базам данных SQL Azure](sql-database-advisor.md). Наведя указатель мыши на заметку, можно отобразить основные сведения о действии.

![Заметка к запросу][6]

Если вы хотите узнать больше или применить рекомендации помощника, щелкните соответствующий значок. Отобразятся подробные сведения о действии. При наличии активной рекомендации ее можно немедленно применить с помощью команды.

![Сведения заметки к запросу][7]

### Несколько заметок
Вполне возможно, что из-за уровня масштаба заметки, находящиеся близко друг к другу, сгруппируются в одну. В этом случае отображается специальный значок. Если щелкнуть его, откроется новая колонка со списком сгруппированных заметок. Корреляция запросов и действия по настройке производительности помогут лучше понять характер рабочей нагрузки.

## Оптимизация конфигурации хранилища запросов для анализа производительности запросов
При работы с анализом производительности запросов хранилище запросов может выдавать следующие сообщения:

* "Хранилище запросов настроено неоптимальным образом для этой базы данных. Щелкните здесь, чтобы получить дополнительные сведения."
* "Хранилище запросов настроено неоптимальным образом для этой базы данных. Щелкните здесь, чтобы изменить настройки."

Обычно эти сообщения отображаются, когда хранилище запросов не может собирать новые данные.

Первый случай происходит, когда хранилище запросов находится в состоянии "только чтение" и заданы оптимальные параметры. Это можно исправить, увеличив размер хранилища запросов или очистив его.

![Кнопка для qds][8]

Второй случай происходит, когда хранилище запросов отключено или не заданы оптимальные параметры. <br>Можно изменить политику хранения и записи и включить хранилище запросов, выполнив приведенные ниже команды или воспользовавшись порталом.

![Кнопка для qds][9]

### Рекомендуемая политика хранения и отслеживания
Политики хранения бывают двух видов.

* На основе размера: если имеет значение AUTO, по достижении максимального размера хранилища данные автоматически удаляются.
* На основе времени: по умолчанию имеет значение 30 дней, означающее, что если в хранилище запросов закончится место, данные запросов старше 30 дней будут удалены.

Для политики отслеживания доступны следующие параметры.

* **All**: фиксируются все запросы.
* **Auto**: редкие запросы и запросы с незначительным временем компиляции и выполнения игнорируются. Пороговые значения счетчика, а также времени компиляции и выполнения определяются внутренним образом. Это вариант по умолчанию.
* **Нет**: хранилище запросов прекращает запись новых запросов, однако по-прежнему собирает статистику выполнения уже записанных запросов.

Рекомендуем выбрать для всех политик параметр AUTO и настроить удаление через 30 дней:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Увеличение размера хранилища запросов. Для этого нужно подключиться к базе данных и отправить следующий запрос:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Применение этих параметров в конечном счете приведет к сбору новых запросов хранилищем запросов, но если вы не хотите ждать, то хранилище запросов можно очистить.

> [!NOTE]
> Выполнение приведенного ниже запроса удалит всю текущую информацию из хранилища запросов.
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## Резюме
Анализ производительности запросов помогает понять влияние рабочей нагрузки запросов и его связь с потреблением ресурсов базы данных. Благодаря этой функции можно узнать о наиболее ресурсоемких запросах и легко определить запросы, которые нужно исправить, прежде чем они станут проблемными.

## Дальнейшие действия
Для получения дополнительных рекомендаций по повышению производительности базы данных SQL щелкните [Рекомендации](sql-database-advisor.md) в колонке **Анализ производительности запросов**.

![Помощник по производительности](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

<!---HONumber=AcomDC_0907_2016-->