---
title: Примеры запросов Lucene для службы поиска Azure | Microsoft Docs
description: Синтаксис запросов Lucene для поиска нечетких соответствий, поиска с учетом расположения, повышения приоритета слов, поиска по регулярным выражениям и поиска с использованием подстановочных знаков.
services: search
documentationcenter: ''
author: LiamCa
manager: pablocas
editor: ''
tags: Lucene query analyzer syntax

ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: liamca

---
# Примеры синтаксиса запросов Lucene для создания запросов в службе поиска Azure
При создании запросов для поиска Azure можно использовать [простой синтаксис запросов](https://msdn.microsoft.com/library/azure/dn798920.aspx) по умолчанию или альтернативное [средство синтаксического анализа запросов Lucene в службе поиска Azure](https://msdn.microsoft.com/library/azure/mt589323.aspx). Средство синтаксического анализа запросов Lucene поддерживает более сложные конструкции запросов, а именно: запросы, относящиеся к полям, поиск нечетких соответствий, поиск с учетом расположения, повышение приоритета слов и поиск по регулярным выражениям.

В этой статье можно пошагово выполнить примеры синтаксиса запросов Lucene и просмотреть результаты, которые отображаются напротив них. Примеры выполняются с предварительно загруженным индексом поиска в [JSFiddle](https://jsfiddle.net/), интерактивном редакторе кода для тестирования скриптов и HTML.

Щелкните правой кнопкой мыши URL-адреса примеров запросов, чтобы открыть JSFiddle в отдельном окне браузера.

> [!NOTE]
> В следующих примерах используется индекс поиска, состоящий из должностей, доступных на основе набора данных, полученных в рамках проекта [по открытым данным г. Нью-Йорк](https://nycopendata.socrata.com/). Эти данные не являются текущими или завершенными. Индекс включен в службу "песочницы", предоставляемую корпорацией Майкрософт. Для использования этих запросов подписка Azure или служба поиска Azure не требуется.
> 
> 

## Просмотр примеров в этой статье
Во всех примерах в этой статье средство синтаксического анализа запросов Lucene указывается с помощью параметра поиска **queryType**. При использовании средства синтаксического анализа запросов Lucene в коде параметр **queryType** необходимо указывать в каждом запросе. Допустимые значения: **simple**|**full**, где **simple** является значением по умолчанию, а **full** соответствует средству синтаксического анализа запросов Lucene. Дополнительные сведения о задании параметров запроса см. в статье [Поиск документов (API REST службы поиска Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

**Пример 1.** Щелкните правой кнопкой мыши приведенный ниже фрагмент запроса, чтобы открыть его в новой странице браузера, где загружается JSFiddle и выполняется запрос.

* [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Этот запрос возвращает документы из нашего индекса должностей (загруженного в службу "песочницы").

В новом окне браузера вы увидите исходный код JavaScript и рядом с ним выходные данные HTML. Сценарий ссылается на запрос, который доступен по URL-адресам примеров в этой статье. Так, в **примере 1** базовый запрос выглядит так:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Обратите внимание на то, что в запросе используется предварительно настроенный индекс поиска Azure с именем nycjobs. Параметр **searchFields** ограничивает поиск полем должности. Параметр **queryType** имеет значение **full**, предписывающее службе поиска Azure использовать средство синтаксического анализа запросов Lucene для этого запроса.

### Операция запроса, относящегося к полю
Вы можете изменить приведенные в этой статье примеры, задав конструкцию **fieldname:searchterm**, чтобы определить операции запроса, относящегося к полю, где поле представляет собой одно слово, а условие поиска — одно слово или фразу, иногда с логическими операторами. Некоторые примеры:

* business\_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

Добавьте несколько строк в кавычках, если необходимо, чтобы обе строки считались одной сущностью, как в приведенном случае поиска двух разных городов в поле расположения. Кроме того, оператор должен быть указан в верхнем регистре, как в случае с NOT и AND.

Поле, указанное в **fieldname:searchterm**, должно поддерживать возможность поиска. Дополнительные сведения об использовании атрибутов индекса в определениях полей см. в статье [Создание индекса (Azure поиска службы REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

## Поиск нечетких соответствий
Операция поиска нечетких соответствий позволяет найти совпадения в словах с аналогичной конструкцией. В [документации Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) поиск нечетких соответствий основан на [расстоянии Дамерау — Левенштейна](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Чтобы выполнить поиск нечетких соответствий, необходимо поставить символ тильды "~" в конце слова. Дополнительно можно поставить цифру от 0 до 2, указывающую расстояние редактирования. Например, "blue~" или "blue~1" вернет результаты с "blue", "blues" и "glue".

**Пример 2.** Щелкните правой кнопкой мыши приведенный ниже фрагмент запроса, чтобы выполнить его. Этот запрос осуществляет поиск должностей, содержащих слово "senior", а не "junior":

* [&queryType=full&search= business\_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## Поиск с учетом расположения
Операция поиска с учетом расположения позволяет найти слова, расположенные рядом в документе. Вставьте символ тильды "~" в конце фразы, а затем — цифру, обозначающую количество слов, определяющее границу близости. Например, если ввести "hotel airport"~5, будут найдены слова "hotel" и "airport", расположенные в пределах 5 слов друг от друга в документе.

**Пример 3.** Щелкните правой кнопкой мыши приведенный ниже фрагмент запроса. Этот запрос осуществляет поиск должностей со словом "associate" (написанного с ошибкой):

* [&queryType=full&search= business\_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Пример 4**. Щелкните запрос правой кнопкой мыши. Осуществляется поиск должностей, содержащих словосочетание "senior analyst", разделенное не более чем одним словом:

* [&queryType=full&search=business\_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Пример 5.** Выполните эту операцию поиска снова, при этом не допуская разделения словосочетания "senior analyst" другими словами.

* [&queryType=full&search=business\_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## Повышение приоритета слов
При повышении приоритета слов документы сортируются по приоритету, т. е. документы, в которых содержится условие поиска, имеют высший приоритет по отношению к документам, в которых его нет. Этот тип запроса отличается от профилей повышения, так как они повышают приоритет определенных полей, а не определенных слов. В следующем примере показаны эти различия.

Рассмотрим профиль повышения, который повышает приоритет совпадений в определенном поле, таком как **genre** в примере musicstoreindex. Повышение значимости слов может использоваться для дальнейшего повышения приоритета определенных условий поиска относительно других. Например, при вводе запроса "rock^2 electronic" документы, содержащие это условие поиска в поле **genre**, становятся приоритетнее документов, содержащих это условие поиска в других полях, поддерживающих поиск, в индексе. Кроме того, документы, содержащие слово "rock", будут иметь более высокий приоритет, чем документы, содержащие слово "electronic", так как введен коэффициент повышения приоритета (2).

Чтобы повысить приоритет условия поиска, используйте символ крышки "^" и коэффициент повышения приоритета (число) в конце этого условия. Чем выше коэффициент повышения приоритета, тем приоритетнее условие поиска относительно других. Коэффициент повышения приоритета по умолчанию — 1. Несмотря на то, что коэффициент повышения приоритета должен быть положительным числом, он может быть меньше 1 (например, 0,2).

**Пример 6**. Щелкните запрос правой кнопкой мыши. Выполняется поиск должностей, содержащих словосочетание "computer analyst". В результате этого поиска должности, содержащие оба слова "computer" и "analyst", не найдены, а должности со словом "analyst" выведены в верхней части результатов.

* [&queryType=full&search=business\_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Пример 7.** Выполните этот поисковой запрос снова, при этом повышая приоритет результатов, содержащих слово "computer", относительно результатов, содержащих слово "analyst", если оба слова не встречаются одновременно.

* [&queryType=full&search=business\_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## Регулярное выражение
Операция поиска по регулярным выражениям позволяет найти совпадение в зависимости от содержимого между косыми чертами "/", как указано в документации [класса RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Пример 8**. Щелкните запрос правой кнопкой мыши. Выполняется поиск должностей, содержащих слово "Senior" или "Junior".

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

URL-адрес для этого примера неправильно отображается на странице. Для решения этой проблемы скопируйте URL-адрес ниже и вставьте его в строку URL-адреса в браузере: `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## Поиск с использованием подстановочных знаков
Вы можете использовать распознаваемый синтаксис для поиска с использованием одного (?) или нескольких (*) подстановочных знаков. Обратите внимание, что средство синтаксического анализа запросов Lucene поддерживает использование этих символов для поиска одного слова, а не фразы.

**Пример 9**. Щелкните запрос правой кнопкой мыши. Выполняется поиск должностей, содержащих в начале"prog". В результате будут найдены должности со словами "programming" и "programmer".

* [&queryType=full&$select=business\_title&search=business\_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

Символ "*" или "?" не может находиться в начале поискового запроса.

## Дальнейшие действия
Попробуйте указать средство синтаксического анализа запросов Lucene в коде. Чтобы узнать о том, как настроить запросы поиска для .NET и REST API, воспользуйтесь приведенными ссылками. В этих ссылках используется простой синтаксис по умолчанию, поэтому потребуется применить полученные из этой статьи знания, чтобы задать **queryType**.

* [Отправка запросов в индекс службы поиска Azure с помощью пакета SDK для .NET](search-query-dotnet.md)
* [Отправка запросов в индекс службы поиска Azure с помощью REST API](search-query-rest-api.md)

<!---HONumber=AcomDC_0907_2016-->