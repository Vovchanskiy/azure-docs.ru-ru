---
title: Мастер копирования фабрики данных | Microsoft Docs
description: Из этой статьи вы узнаете, как с помощью мастера копирования фабрики данных копировать данные из поддерживаемых источников данных в приемники.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2016
ms.author: spelluru

---
# Мастер копирования фабрики данных
Мастер копирования фабрики данных Azure позволяет создавать конвейеры для копирования данных из поддерживаемых источников в целевые расположения без написания определений JSON для связанных служб, наборов данных и конвейеров. Чтобы запустить мастер копирования, на домашней странице фабрики данных щелкните плитку **Копировать данные**.

![Мастер копирования](./media/data-factory-copy-wizard/copy-data-wizard.png)

## Функции
### Интуитивно понятный и простой мастер для копирования данных
Этот мастер позволяет легко перемещать данные из источника в место назначения за несколько минут с помощью следующих простых действий:

1. Укажите источник.
2. Укажите целевое расположение.
3. Настройте параметры.

![Выберите источник данных](./media/data-factory-copy-wizard/select-data-source-page.png)

### Широкие возможности просмотра данных и сопоставления схем
В мастере вы можете просматривать таблицы и папки, выполнять предварительный просмотр данных, сопоставлять схемы, проверять выражения и выполнять простые преобразования данных.

**Обзор таблиц и папок** ![Обзор таблиц и папок](./media/data-factory-copy-wizard/browse-tables-folders.png)

### Интерфейс масштабирования для разнообразных данных и типов объектов
Интерфейс изначально предполагает работу с большими объемами данных. Вы можете быстро и просто создавать конвейеры фабрики данных для перемещения сотен папок, файлов или таблиц.

**Предварительный просмотр данных** ![Параметры формата файла](./media/data-factory-copy-wizard/file-format-settings.png)

**Сопоставление схемы** ![Сопоставление схем](./media/data-factory-copy-wizard/schema-mapping.png)

**Выполнение простых преобразований** ![Проверка выражений](./media/data-factory-copy-wizard/validate-expressions.png)

### Интерфейс масштабирования для разнообразных данных и типов объектов
С помощью мастера копирования можно быстро и просто перемещать сотни папок, файлов или таблиц.

![Выбор таблиц, из которых нужно копировать данные](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

### Расширенные параметры планирования
Операцию копирования можно выполнить однократно или запускать по расписанию (ежечасно, ежедневно и т. д.). Оба варианта можно использовать с любыми соединителями в локальной сети или в облаке, а также с копиями на локальном настольном ПК.

Однократная операция копирования позволяет один раз переместить данные из источника в целевое расположение. Ее можно применять к данным любого размера и любого из поддерживаемых форматов. Запланированное копирование позволяет копировать данные с определенной периодичностью. Для запланированного копирования можно задать широкий набор настроек (повторение, время ожидания, предупреждения и т. д.).

![Планирование свойств](./media/data-factory-copy-wizard/scheduling-properties.png)

## Переменные в пути к папке BLOB-объекта Azure
Вы можете использовать переменные в пути к папке, чтобы определять источник копирования данных при выполнении операции на основе [системных переменных WindowStart](data-factory-functions-variables.md#data-factory-system-variables). Поддерживаемые переменные: **{year}**, **{month}**, **{day}**, **{hour}**, **{minute}** и **{custom}**. Пример: inputfolder/{year}/{month}/{day}.

Предположим, что у вас есть входные каталоги с именами в следующем формате:

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

Нажмите кнопку **Обзор** в разделе **Файл или папка**, перейдите к любой из этих папок, например 2016->03->01->02, и щелкните **Выбрать**. В текстовом поле появится такое значение: **2016/03/01/02**. Измените в нем **2016** на **{year}**, **03** на **{month}**, **01** на **{day}**, **02** на **{hour}** и нажмите клавишу TAB. Вы увидите раскрывающиеся списки, в которых можно выбрать формат для этих четырех переменных, как показано ниже.

![Использование системных переменных](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)

Можно также использовать переменную **custom**, как показано ниже, и любые [поддерживаемые строки формата](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Чтобы выбрать папку с такой структурой, сначала нажмите кнопку **Обзор**. Затем замените нужное значение на **{custom}** и нажмите клавишу TAB. Вы увидите текстовое поле для ввода строки формата.

![Использование пользовательской переменной](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## Дальнейшие действия
Краткое описание создания конвейера с действием копирования с помощью мастера копирования фабрики данных см. в [этой статье](data-factory-copy-data-wizard-tutorial.md).

<!---HONumber=AcomDC_0824_2016-->