---
title: "Работа с Application Insights в Visual Studio"
description: "Анализ производительности и диагностика во время отладки и в производственной среде."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 2059802b-1131-477e-a7b4-5f70fb53f974
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8886107f96f31f668ce55ebb4c76e58ad1f21268


---
# <a name="working-with-application-insights-in-visual-studio"></a>Работа с Application Insights в Visual Studio
В Visual Studio 2015 и более поздних версиях можно анализировать производительность и диагностировать проблемы при отладке и в производственной среде с помощью телеметрии из [Visual Studio Application Insights](app-insights-overview.md).

Если вы еще не [установили Application Insights в своем приложении](app-insights-asp-net.md), сделайте это сейчас.

## <a name="a-nameruna-debug-your-project"></a><a name="run"></a> Отладка проекта
Запустите приложение, нажав клавишу F5, и попробуйте открывать разные страницы, чтобы создать некоторый объем данных телеметрии.

В Visual Studio вы увидите число записанных в журнал событий.

![В Visual Studio кнопка Application Insights доступна во время отладки.](./media/app-insights-visual-studio/appinsights-09eventcount.png)

Нажмите эту кнопку, чтобы начать поиск по журналу диагностики. 

## <a name="diagnostic-search"></a>Поиск по журналу диагностики
В окне поиска отображаются события, которые были записаны в журнал. (Если во время настройки Application Insights вы вошли в Azure, эти же события вы сможете найти на портале.)

![Щелкните проект правой кнопкой мыши и последовательно выберите пункты "Application Insights" и "Поиск".](./media/app-insights-visual-studio/34.png)

Бесплатный полнотекстовый поиск охватывает все поля в журнале событий. Например, можно выполнить поиск по фрагменту URL-адреса страницы, а также по значению свойства (например, город клиента) или ключевым словам в журнале трассировки.

Выберите любое событие, чтобы подробно просмотреть его свойства.

Кроме того, чтобы диагностировать невыполненные запросы или исключения, вы можете открыть вкладку "Связанные элементы".

![](./media/app-insights-visual-studio/41.png)

## <a name="diagnostics-hub"></a>Концентратор диагностики
Концентратор диагностики (в Visual Studio 2015 или более поздней версии) отображает данные телеметрии сервера Application Insights при их создании. Это средство работает, даже если вы установили пакет SDK без подключения к ресурсу на портале Azure.

![Откройте окно средств диагностики и проверьте события Application Insights.](./media/app-insights-visual-studio/31.png)

## <a name="exceptions"></a>Исключения
Если вы [настроили отслеживание исключений](app-insights-asp-net-exceptions.md), отчеты об исключениях будут отображаться в окне поиска. 

Щелкните исключение, чтобы просмотреть трассировку стека. Если код приложения открыт в среде Visual Studio, можно щелкнуть трассировку стека в соответствующей строке кода.

![Трассировка стека исключений](./media/app-insights-visual-studio/17.png)

Кроме того, в строке группы связанных элементов кода над каждым методом приведено количество исключений, зарегистрированных Application Insights за последние 24 часа.

![Трассировка стека исключений](./media/app-insights-visual-studio/21.png)

## <a name="local-monitoring"></a>Локальный мониторинг
(Относится к Visual Studio 2015 с обновлением 2) Если ваш пакет SDK не отправляет данные телеметрии на портал Application Insights (то есть в файле ApplicationInsights.config нет ключа инструментирования), окно диагностики отображает данные телеметрии, полученные в ходе последнего сеанса отладки. 

Это предпочтительно, если вы уже опубликовали предыдущую версию своего приложения. Кроме того, не рекомендуется, чтобы данные телеметрии, полученные на ваших сеансах отладки, смешивались с данными телеметрии на портале Application Insights, полученными от опубликованного приложения.

Это также полезно, если у вас есть [данные пользовательской телеметрии](app-insights-api-custom-events-metrics.md) , которые необходимо отладить перед отправкой на портал.

* *Сначала мы полностью настроили Application Insights для отправки данных телеметрии на портал. Но теперь нужно, чтобы данные телеметрии отображались только в Visual Studio.*
  
  * В параметрах окна поиска можно включить поиск локальной диагностики, который будет выполняться, даже если ваше приложение отправляет данные телеметрии на портал.
  * Чтобы остановить отправку данных телеметрии на портал, закомментируйте строку `<instrumentationkey>...` в файле ApplicationInsights.config. Когда данные телеметрии будут готовы к отправке на портал, раскомментируйте ее.

## <a name="trends"></a>Тренды
Тренды — это средство для визуализации того, как изменяется поведение приложения со временем. 

Нажмите кнопку **Обзор трендов телеметрии** на панели инструментов Application Insights или в окне поиска Application Insights. Выберите один из пяти стандартных запросов, чтобы приступить к работе. Анализировать разные наборы данных можно на основе типов данных телеметрии, диапазонов времени и других свойств. 

Чтобы найти аномалии в данных, выберите один из вариантов аномалий в раскрывающемся списке "Тип представления". Параметры фильтрации в нижней части окна позволяют легко находить конкретные подмножества данных телеметрии.

![Тренды](./media/app-insights-visual-studio/51.png)

[Дополнительные сведения о тенденциях](app-insights-visual-studio-trends.md)

## <a name="whats-next"></a>Что дальше?
|  |  |
| --- | --- |
| **[Добавление данных](app-insights-asp-net-more.md)**<br/>Мониторинг использования, доступности, зависимостей и исключений. Интеграция трассировок из платформ ведения журналов. Написание пользовательской телеметрии. |![Visual studio](./media/app-insights-visual-studio/64.png) |
| **[Работа с порталом Application Insights](app-insights-dashboards.md)**<br/>Панели мониторинга, эффективные средства диагностики и анализа, оповещения, карта динамических зависимостей приложения, а также экспорт данных телеметрии. |![Visual studio](./media/app-insights-visual-studio/62.png) |




<!--HONumber=Nov16_HO2-->


