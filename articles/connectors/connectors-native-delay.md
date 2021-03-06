---
title: Добавление задержки в приложения логики | Microsoft Docs
description: 'В этой статье приведены общие сведения о действиях '
;задержка";: ''
и: ''
"\";задержка": ''
до";,: ''
а: ''
также: ''
об: ''
их: ''
использовании: ''
с: ''
приложением: ''
логики: ''
azure.": ''
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan

---
# Начало работы с действиями "Задержка" и "Задержка до"
С помощью действий "Задержка" и "Задержка до" можно выполнять сценарии рабочих процессов.

Например, вы можете просматривать:

* ожидание буднего дня, чтобы отправить обновление состояния по почте;
* задержка рабочего процесса до завершения вызова HTTP, прежде чем возобновить работу и получить результат.

Сведения о начале работы с действием задержки в приложении логики см. в статье о [создании приложения логики](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Использование действий задержки
Действие — это операция, выполняемая рабочим процессом, определенным в приложении логики. Дополнительные сведения о действиях см. [здесь](connectors-overview.md).

Ниже приведен пример последовательности настройки действия задержки в приложении логики.

1. Чтобы добавить действие, после добавления триггера нажмите кнопку **Новый шаг**.
2. В текстовом поле введите **задержка**, чтобы открыть список действий задержки. В этом примере мы выберем действие **Задержка**.
   
    .![Действия задержки](./media/connectors-native-delay/using-action-1.png)
3. Чтобы настроить задержку, задайте свойства действия.
   
    .![Настройка задержки](./media/connectors-native-delay/using-action-2.png)
4. Щелкните **Сохранить**, чтобы опубликовать и активировать приложение логики.

## Сведения о действиях
Ниже приведены свойства, которые можно настроить для триггера повторения.

### Действие "Задержка"
Это действие задерживает запуск на определенный промежуток времени. Звездочка (*) означает, что это поле обязательное для заполнения.

| Отображаемое имя | Имя свойства | Описание |
| --- | --- | --- |
| Счетчик* |count |Число единиц времени для задержки |
| Единица измерения* |unit |Единица времени: `Second`, `Minute`, `Hour` или `Day` |

<br>

### Действие "Задержка до"
Это действие задерживает запуск до указанной даты или времени. Звездочка (*) означает, что это поле обязательное для заполнения.

| Отображаемое имя | Имя свойства | Описание |
| --- | --- | --- |
| Год* |Timestamp |Задержка до года (GMT) |
| Месяц* |Timestamp |Задержка до месяца (GMT) |
| День* |Timestamp |Задержка до дня (GMT) |

.<br>

## Дальнейшие действия
Теперь опробуйте платформу и [создайте приложение логики](../app-service-logic/app-service-logic-create-a-logic-app.md). Чтобы узнать, какие еще соединители доступны в приложениях логики, ознакомьтесь со [списком интерфейсов API](apis-list.md).

<!---HONumber=AcomDC_0810_2016-->