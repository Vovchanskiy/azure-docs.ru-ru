---
title: Анализ производительности узла Edge в CDN Azure | Microsoft Docs
description: Анализ производительности узла Edge в сети CDN Microsoft Azure Аналитика производительности узла Edge предоставляет подробные сведения о трафике и использовании пропускной способности в сети CDN.
services: cdn
documentationcenter: ''
author: camsoper
manager: erikre
editor: ''

ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper

---
# Анализ производительности узла Edge в сети CDN Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## Обзор
Аналитика производительности узла Edge предоставляет подробные сведения о трафике и использовании пропускной способности в сети CDN. Эти сведения можно использовать для создания статистики роста популярности, которая позволяет оценить порядок кэширования и доставки ресурсов вашим клиентам. В свою очередь, на основании этого вы можете формировать стратегии оптимизации доставки содержимого и определять проблемы, решение которых позволит использовать CDN более эффективно. В результате вы можете не просто повысить производительность доставки данных, но и снизить затраты, связанные с CDN.

> [!NOTE]
> Во всех отчетах при указании даты и времени используется формат UTC/GMT.
> 
> 

## Коллекция отчетов и журналов
Прежде чем средства аналитики производительности Edge смогут формировать отчеты, потребуется собрать данные об активности в сети CDN. Процесс сбора данных происходит один раз в день и охватывает действия, произошедшие за предыдущий день. Это означает, что приводимая в отчетах статистика отражает результаты за день с обработанными данными и не обязательно полностью отражает результаты текущего дня. Основная функция этих отчетов – оценка производительности. Их не следует использовать для выставления счетов или определения точных числовых показателей.

> [!NOTE]
> Необработанные данные, на основании которых формируются отчеты производительности Edge, доступны на период не менее 90 дней.
> 
> 

## Панель мониторинга
Панель мониторинга средств аналитики производительности Edge позволяет анализировать текущий и прошлый трафик в сети CDN в виде диаграмм и статистических данных. Вы можете использовать эту панель для определения текущих и долгосрочных тенденций в скорости передачи данных в сети CDN для своей учетной записи.

Составные элементы панели мониторинга.

* Интерактивная диаграмма с ключевыми показателями и тенденциями.
* Временная шкала с расшифровкой долгосрочных шаблонов для ключевых показателей и тенденций.
* Ключевые показатели и статистические сведения о повышении качества передачи данных в сети CDN с точки зрения общей производительности, использования ресурсов и эффективности.

### Доступ к панели мониторинга производительности Edge
1. В колонке профиля CDN нажмите кнопку **Управление**.
   
    ![Кнопка «Управление» в колонке профиля CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Откроется портал управления CDN.
2. Наведите указатель на вкладку **Аналитика**, а затем – на всплывающее окно **Аналитика производительности Edge**. Щелкните **Панель мониторинга**.
   
    Откроется панель мониторинга аналитики Edge.

### Диаграмма
Панель мониторинга содержит диаграмму отслеживания показателей за указанный период времени, выбранный на шкале времени непосредственно под диаграммой. Под диаграммой находится временная шкала с диапазоном, соответствующим двум последним годам работы сети CDN.

#### Использование диаграммы
* По умолчанию строится диаграмма коэффициента эффективности кэша за последние 30 дней.
* Эта диаграмма создается на основании ежедневных данных.
* При наведении курсора на отрезок вы увидите дату и значение показателя на эту дату.
* Выберите «Выделять выходные дни», чтобы затенить на диаграмме вертикальные отрезки, соответствующие выходным дням. Этот подход удобен для определения шаблонов трафика в выходные дни.
* Выберите «Данные за прошлый год», чтобы наложить данные за предыдущий год на аналогичный период текущего года. Этот тип сравнения позволяет определить долгосрочные закономерности использования сети CDN. В правом верхнем углу диаграммы находится блок условных обозначений с определением цветов для всех отрезков диаграммы.

#### Обновление диаграммы
* Диапазон времени. Выполните одно из следующих действий.
  * Выберите нужный диапазон на временной шкале. Диаграмма будет обновлена в соответствии с данными выбранного периода.
  * Дважды щелкните диаграмму, чтобы отобразить все данные (максимум два года).
* Показатели. Щелкните значок диаграммы рядом с нужным показателем. Диаграмма и временная шкала будут обновлены на основании данных для выбранной метрики.

### Ключевые метрики и статистика
#### Показатели эффективности
Эти показатели предназначены для определения возможностей повышения эффективности кэша. Повышение эффективности кэша дает следующие преимущества.

* Снижение нагрузки на сервере-источнике, что позволяет:
  * улучшить производительность веб-сервера;
  * снизить эксплуатационные расходы.
* Повышение скорости доставки данных за счет увеличения количества запросов, обрабатываемых непосредственно в сети CDN.

| Поле | Описание |
| --- | --- |
| Эффективность кэша |Указывает процент переданных данных, обработанных из кэша. Этот показатель определяет объем кэшированного содержимого, отправленного непосредственно из сети CDN (серверов Edge) источникам запросов (например, веб-браузерам). |
| Количество переходов |Указывает процент запросов, обработанных из кэша. Этот показатель определяет объем кэшированного содержимого, отправленного непосредственно из сети CDN (серверов Edge) источникам запросов (например, веб-браузерам). |
| % удаленных байтов – конфигурация без кэша |Указывает процент трафика, направленного с серверов-источников в сеть CDN (серверы Edge), который не будет кэшироваться из-за включенного параметра «Не использовать кэш» (обработчик правил HTTP). |
| % удаленных байтов – кэш с истекшим сроком |Указывает процент трафика, переданного с серверов-источников в сеть CDN (серверы Edge) в результате повторной проверки устаревшего содержимого. |

#### Показатели использования
Эти показатели позволяют оценить эффективность следующих методов сокращения затрат.

* Снижение эксплуатационных расходов в сети CDN.
* Сокращение расходов на CDN за счет эффективного использования кэша и алгоритмов сжатия.

> [!NOTE]
> Значения объема трафика показывают исходные данные, используемые для расчета коэффициентов и процентов. Для клиентов с большими объемами трафика эти значения могут отражать лишь часть всех объемов.
> 
> 

| Поле | Описание |
| --- | --- |
| Отправлено байт, в среднем |Указывает среднее число байтов, переданных для каждого запроса, обслуженного в сети CDN (серверов Edge), источникам запросов (например, веб-браузерам). |
| Байты при конфигурации без кэша |Указывает процент трафика, переданного из сети CDN (серверов Edge) источникам запросов (например, веб-браузерам) и не кэшированного из-за включения параметра «Не использовать кэш». |
| Сжатые байты |Указывает процент трафика, отправленного из сети CDN (серверов Edge) источникам запросов (например, веб-браузерам) в сжатом формате. |
| Отправлено байт |Указывает объем данных в байтах, которые были доставлены из сети CDN (серверов Edge) источникам запросов (например, веб-браузерам). |
| Получено байт |Указывает объем данных в байтах, отправленных источниками запросов (например, веб-браузерами) в сеть CDN (на серверы Edge). |
| Удаленные байты |Указывает объем данных в байтах, отправленных из сети CDN или серверов-источников клиента в сеть CDN (на серверы Edge). |

#### Метрики производительности
Эти показатели предназначены для отслеживания общей производительности сети CDN для всего трафика.

| Поле | Описание |
| --- | --- |
| Скорость передачи |Указывает среднюю скорость передачи содержимого из сети CDN источникам запросов. |
| Длительность |Указывает среднее время в миллисекундах, затраченное на доставку ресурсов источникам запросов (например, веб-браузерам). |
| Скорость сжатых запросов |Указывает процент переходов, переданных из сети CDN (серверов Edge) источникам запросов (например, веб-браузерам) в сжатом формате. |
| Частота ошибок 4xx |Указывает процент переходов, создавших код состояния 4xx. |
| Частота ошибок 5xx |Указывает процент переходов, создавших код состояния 5xx. |
| Попадания |Указывает количество запросов к содержимому сети CDN. |

#### Показатели безопасного трафика
Эти показатели предназначены для отслеживания производительности сети CDN в отношении HTTP-трафика.

| Поле | Описание |
| --- | --- |
| Эффективность безопасного кэша |Указывает процент данных, переданных для HTTPS-запросов и обработанных из кэша. Этот показатель определяет объем кэшированного содержимого, отправленного непосредственно из сети CDN (серверов Edge) источникам запросов (например, веб-браузерам) по протоколу HTTPS. |
| Скорость безопасной передачи |Указывает среднюю скорость передачи содержимого из сети CDN (серверов Edge) источникам запросов (например, веб-браузерам) по протоколу HTTPS. |
| Средняя безопасная длительность |Указывает среднее время в миллисекундах, затраченное на доставку ресурсов источникам запросов (например, веб-браузерам) по протоколу HTTPS. |
| Безопасные переходы |Указывает количество HTTPS-запросов к содержимому сети CDN. |
| Безопасно отправлено байт, в среднем |Указывает объем HTTPS-трафика в байтах, которые были доставлены из сети CDN (серверов Edge) источникам запросов (например, веб-браузерам). |

## Отчеты
Каждый отчет из этого модуля содержит диаграмму и статистику использования пропускной способности и трафика для различных показателей (например, кодов состояния HTTP, кодов состояния кэша, запрашиваемых URL-адресов и т.д.). Эта информация может использоваться для более подробного анализа предоставления содержимого клиентам и точной настройки поведения сети CDN с целью ускорения доставки данных.

### Доступ к отчетам по производительности Edge
1. В колонке профиля CDN нажмите кнопку **Управление**.
   
    ![Кнопка «Управление» в колонке профиля CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    Откроется портал управления CDN.
2. Наведите указатель на вкладку **Аналитика**, а затем – на всплывающее окно **Аналитика производительности Edge**. Щелкните **Большой HTTP-объект**.
   
    Откроется экран аналитических отчетов Edge.

| Отчет | Описание |
| --- | --- |
| Сводка по дням |Позволяет просматривать тенденции ежедневного трафика за определенный период времени. Каждый отрезок на диаграмме соответствует определенной дате. Размер отрезка указывает число переходов за соответствующий день. |
| Сводка по часам |Позволяет просматривать тенденции трафика за определенный период времени с разбивкой по часам. Каждый отрезок на диаграмме соответствует одному часу определенного дня. Размер отрезка указывает число переходов за соответствующий час. |
| Протоколы |Отображает распределение трафика между протоколами HTTP и HTTPS. Кольцевой график показывает процент переходов для каждого типа протокола. |
| Методы HTTP |Позволяет быстро ознакомиться с HTTP-методами для запроса ваших данных. Как правило, к самым распространенным HTTP-методам относятся GET, HEAD и POST. Кольцевой график показывает процент переходов для каждого типа HTTP-методов. |
| URL-адреса |Содержит диаграмму с 10 самыми популярными URL-адресами. Каждому URL-адресу соответствует один отрезок. Высота отрезка показывает число переходов для определенного URL-адреса за период отчета. Непосредственно под диаграммой отображается статистика для 100 основных URL-адресов. |
| Имена CNAME |Содержит диаграмму с 10 основными именами CNAME, используемыми для доступа к ресурсам за период отчета. Непосредственно под диаграммой отображается статистика для 100 основных имен CNAME. |
| Источники |Содержит диаграмму с 10 основными серверами-источниками сети CDN или клиента, из которых запрашивались ресурсы за определенный период времени. Непосредственно под диаграммой отображается статистика для 100 основных серверов-источников CDN или клиента. В качестве имени серверов-источников клиента указывается значение, определенное в параметре имени каталога. |
| Географические точки присутствия |Показывает, какая часть трафика направляется через определенную точку присутствия. Точке присутствия из сети CDN соответствует трехбуквенное обозначение. |
| Клиенты |Содержит график с 10 основными клиентами, которые запрашивали ресурсы за определенный период времени. В этом отчете все запросы, поступившие с одного IP-адреса, считаются запросами от одного клиента. Непосредственно под диаграммой отображается статистика для 100 основных клиентов. Этот отчет можно использовать для определения шаблонов загрузки для основных клиентов. |
| Состояния кэша |Предоставляет расшифровку поведения при кэшировании, которая поможет улучшить впечатления от использования. Поскольку наибольшая производительность возникает при наличии попаданий в кэш, вы можете оптимизировать скорость предоставления данных, сведя к минимуму непопадания и попадания, потерявшие актуальность. |
| Сведения о NONE |Содержит диаграмму с 10 основными URL-адресами ресурсов, для которых в течение определенного периода времени не выполнялись проверки свежести кэша. Непосредственно под диаграммой отображается статистика для 100 основных URL-адресов этих типов ресурсов. |
| Сведения о CONFIG\_NOCACHE |Содержит диаграмму с 10 основными URL-адресами ресурсов, которые не были кэшированы из-за конфигурации CDN клиента. Эти типы ресурсов обслуживаются непосредственно на сервере-источнике. Непосредственно под диаграммой отображается статистика для 100 основных URL-адресов этих типов ресурсов. |
| Сведения о UNCACHEABLE |Содержит диаграмму с 10 основными URL-адресами ресурсов, которые не были кэшированы из-за данных в заголовке запроса. Непосредственно под диаграммой отображается статистика для 100 основных URL-адресов этих типов ресурсов. |
| Сведения о TCP\_HIT |Содержит диаграмму с 10 основными URL-адресами ресурсов, которые были обработаны мгновенно при помощи кэша. Непосредственно под диаграммой отображается статистика для 100 основных URL-адресов этих типов ресурсов. |
| Сведения о TCP\_MISS |Содержит диаграмму с 10 основными URL-адресами ресурсов с состоянием кэша TCP\_MISS. Непосредственно под диаграммой отображается статистика для 100 основных URL-адресов этих типов ресурсов. |
| Сведения о TCP\_EXPIRED\_HIT |Содержит диаграмму с 10 основными URL-адресами ресурсов с устаревшим кэшем, которые были обработаны непосредственно из точки присутствия. Непосредственно под диаграммой отображается статистика для 100 основных URL-адресов этих типов ресурсов. |
| Сведения о TCP\_EXPIRED\_MISS |Содержит диаграмму с 10 основными URL-адресами ресурсов с устаревшим кэшем, для которых требуется получение новой версии с сервера-источника. Непосредственно под диаграммой отображается статистика для 100 основных URL-адресов этих типов ресурсов. |
| Сведения о TCP\_CLIENT\_REFRESH\_MISS |Содержит линейчатую диаграмму с 10 основными URL-адресами ресурсов, полученных с сервера-источника благодаря запросу от клиента, запрещающему кэширование. Непосредственно под диаграммой отображается статистика для 100 основных URL-адресов этих типов запросов. |
| Типы клиентских запросов |Указывает типы запросов, отправленных HTTP-клиентами (например, браузерами). Этот отчет включает кольцевую диаграмму со сведениями об обработке запросов. Под диаграммой отображаются сведения о пропускной способности и трафике для каждого типа запроса. |
| Агент пользователя |Содержит линейчатую диаграмму с 10 основными пользовательскими агентами, используемыми для доступа к содержимому через сеть CDN. Как правило, агент пользователя является веб-браузером, проигрывателем или браузером мобильного телефона. Непосредственно под диаграммой отображается статистика для 100 основных агентов. |
| Источники ссылок |Содержит линейчатую диаграмму с 10 основными источниками ссылок на содержимое, к которому осуществляется доступ через сеть CDN. Как правило, источник ссылок – это URL-адрес веб-страницы или ресурса со ссылкой на ваше содержимое. Под диаграммой приводятся подробные сведения об основных 100 источниках ссылок. |
| Типы сжатия |Содержит кольцевую диаграмму с разбивкой запрашиваемых ресурсов по признаку сжатия на серверах Edge. Процент сжатых ресурсов распределяется по типу сжатия. Под диаграммой приводятся подробные сведения обо всех типах и состояниях сжатия. |
| Типы файлов |Содержит линейчатую диаграмму с 10 основными файлами, запрашиваемыми через сеть CDN для вашей учетной записи. В этом отчете тип файла определяется расширением файла ресурса и MIME-типом (например, .html [text/html], .htm [text/html], .aspx [text/htm] и т.д.). Под диаграммой приводятся подробные сведения об основных 100 типах файлов. |
| Уникальные файлы |Содержит диаграмму, которая выводит общее количество уникальных ресурсов, запрошенных за каждый день определенного периода времени. |
| Сводка по маркерам проверки подлинности |Содержит круговую диаграмму с краткой информацией о доступе к запрошенным ресурсам с проверкой подлинности на основе маркеров. На диаграмме защищенные ресурсы отображаются в соответствии с результатами попытки проверки подлинности. |
| Сведения об отказе доступа с маркером проверки подлинности |Содержит линейчатую диаграмму с 10 основными запросами, которые были отклонены по результатам проверки подлинности на основе маркеров. |
| Коды ответов HTTP |Отчет о кодах состояния HTTP (например, «200 ОК», «403 Запрещено», «404 Не найдено» и т.д.), отправленных вашим HTTP-клиентам серверами Edge. Круговая диаграмма позволяет быстро оценить обработку ресурсов. Под диаграммой отображаются подробные статистические данные по каждому ответу. |
| Ошибки 404 |Содержит линейчатую диаграмму с 10 основными запросами, приводящими к коду ответа «404 Не найдено». |
| Ошибки 403 |Содержит линейчатую диаграмму с 10 основными запросами, приводящими к коду ответа «403 Запрещено». Код ответа «403 Запрещено» отображается, если сервер-источник клиента или наш сервер Edge в точке доступности отклоняет запрос. |
| Ошибки 4xx |Содержит линейчатую диаграмму с 10 основными запросами, приводящими к кодам ответа из диапазона 400. Этот отчет не включает ошибки «403 Не найдено» и «404 Запрещено». Обычно код ответа 4xx отображается, если запрос отклоняется из-за ошибки клиента. |
| Ошибки 504 |Содержит линейчатую диаграмму с 10 основными запросами, приводящими к коду ответа «504 Истекло время ожидания шлюза». Код ответа «504 Истекло время ожидания шлюза» отображается, если при попытке связи прокси-сервера HTTP с другим сервером истекает время ожидания. В сети CDN код «504 Истекло время ожидания шлюза» обычно отображается, когда серверу Edge не удается установить связь с сервером-источником клиента. |
| Ошибки 502 |Содержит линейчатую диаграмму с 10 основными запросами, приводящими к коду ответа «502 Недопустимый шлюз». Код ошибки «502 Недопустимый шлюз» обычно отображается при сбое протокола HTTP между сервером и прокси-сервером HTTP. В сети CDN код «502 Недопустимый шлюз» обычно отображается, когда сервер-источник клиента возвращает серверу Edge недопустимый ответ. Ответ является недопустимым, если он не полный или если из него не удается извлечь данные. |
| Ошибки 5xx |Содержит линейчатую диаграмму с 10 основными запросами, приводящими к кодам ответа из диапазона 500. Этот отчет не включает коды ответов «502 Недопустимый шлюз» и «504 Истекло время ожидания шлюза». |

## См. также
* [Обзор Azure CDN](cdn-overview.md)
* [Статистика сети CDN Microsoft Azure в режиме реального времени](cdn-real-time-stats.md)
* [Переопределение стандартного поведения через HTTP с помощью обработчика правил](cdn-rules-engine.md)
* [Расширенные HTTP-отчеты](cdn-advanced-http-reports.md)

<!---HONumber=AcomDC_0803_2016-->