---
title: Переход от мониторинга конечных точек Azure к тестам доступности Application Insights
description: Переход от классических тестов мониторинга конечных точек Azure к тестам доступности Application Insights до 31 октября 2016 г.
services: application-insights
documentationcenter: ''
author: soubhagyadash
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: awills

---
# <a name="moving-from-azure-endpoint-monitoring-to-application-insights-availability-tests"></a>Переход от мониторинга конечных точек Azure к тестам доступности Application Insights
Вы используете [мониторинг конечных точек](https://blogs.msdn.microsoft.com/mast/2013/03/03/windows-azure-portal-update-configure-web-endpoint-status-monitoring-preview/) для приложений Azure? Не позднее *31 октября 2016 г.* он будет заменен новыми и более эффективными [тестами доступности](app-insights-monitor-web-app-availability.md). Хотя некоторые тесты уже созданы, они будут включены 31 октября 2016 г. 

Если требуется, вы можете изменять новые тесты и самостоятельно переключаться между ними. Они находятся на [портале Azure](https://portal.azure.com) в группе ресурсов Default-ApplicationInsights-CentralUS.

## <a name="what-are-availability-tests?"></a>Что такое тесты доступности?
Тестирование доступности — это функция Azure, которая постоянно проверяет работоспособность веб-сайта или службы, отправляя к ним HTTP-запросы (отдельные тесты проверки связи или веб-тесты Visual Studio) из 16 расположений по всему миру. 

На [классическом портале Azure](https://manage.windowsazure.com)эти тесты назывались мониторингом конечных точек. Их область действия была более ограниченной. Новые тесты доступности характеризуются существенными усовершенствованиями, которые приведены ниже.

* До 10 веб-тестов Visual Studio и проверок связи для каждого ресурса Application Insights. 
* До 16 расположений по всему миру для отправки тестовых запросов в ваше веб-приложение. Больший контроль критериев успешного завершения теста. 
* Тестирование любых веб-сайтов и служб, а не только веб-приложений Azure.
* Повторные тестирования: сокращение числа ложноположительных предупреждений из-за временных неполадок сети. 
* Веб-перехватчики могут получать уведомления HTTP POST для оповещений.

![](./media/app-insights-migrate-azure-endpoint-tests/16-1test.png)

Узнайте больше о [тестах доступности здесь](app-insights-monitor-web-app-availability.md).

Тесты доступности являются частью [Visual Studio Application Insights](app-insights-overview.md), расширяемой службы аналитики для любого веб-приложения.

## <a name="so-what's-happening-to-my-endpoint-tests?"></a>Что происходит с тестами конечных точек?
* Мы скопировали тесты мониторинга конечных точек в новые тесты доступности Application Insights.
* В настоящее время новые тесты доступности отключены, а старые тесты конечных точек все еще работают.
* Правила оповещений *не* были перенесены. Изначально новые тесты были настроены с правилом по умолчанию:
  * срабатывание в случае, если 1 расположение сообщает о сбоях в течение 5 минут;
  * отправка сообщения электронной почты администраторам подписок.

На [портале Azure](https://portal.azure.com)перенесенные тесты находятся в группе ресурсов "Default-ApplicationInsights-CentralUS". Имена тестов имеют префикс "migrated-". 

## <a name="what-do-i-need-to-do?"></a>Что нужно сделать?
* Если мы по каким-либо причинам не перенесли ваши тесты, вы можете [легко настроить](app-insights-monitor-web-app-availability.md)новые тесты доступности.

### <a name="option-a:-do-nothing.-leave-it-to-us."></a>Вариант А. Ничего не делайте. Предоставьте все нам.
**31 октября 2016 г.** мы:

* отключим старые тесты конечных точек;
* включим перенесенные тесты доступности.

### <a name="option-b:-you-manage-and/or-enable-the-new-tests."></a>Вариант Б. Вы управляете новыми тестами и (или) включаете их.
* Проверка и редактирование новых тестов доступности на новом [портале Azure](https://portal.azure.com). 
  * Проверка условий триггера
  * Проверка получателей электронной почты
* Включение новых тестов
* Устаревшие тесты мониторинга конечных точек будут отключены 31 октября 2016 г. 

### <a name="option-c:-opt-out"></a>Параметр В. Отказ от использования.
Если вы не хотите использовать тесты доступности, удалите их на [портале Azure](https://portal.azure.com). Кроме того, в нижней части электронных уведомлений есть ссылка для отказа от подписки.

31 октября 2016 г. мы удалим старые тесты конечных точек. 

## <a name="how-do-i-edit-the-new-tests?"></a>Как изменить новые тесты?
Войдите на [портал Azure](https://portal.azure.com) и найдите веб-тесты с префиксом "migrated-". 

![Выберите "Группы ресурсов", Default-ApplicationInsights-CentralUS и откройте тесты c префиксом "migrated".](./media/app-insights-migrate-azure-endpoint-tests/20.png)

Измените или включите тест:

![](./media/app-insights-migrate-azure-endpoint-tests/21.png)

## <a name="why-is-this-happening?"></a>Почему это происходит?
Более эффективная служба. Область действия старой службы конечных точек была гораздо уже. Можно было указать только два URL-адреса для простых тестов проверки связи из 3 геоположений на виртуальной машине Azure или в веб-приложении. Новые тесты могут выполнять многошаговые веб-тесты из максимум 16 расположений. Для одного приложения можно указать до 10 тестов. Можно проверить любой URL-адрес — это не обязательно должен быть сайт Azure.

Новые тесты настраиваются отдельно от тестируемого веб-приложения или виртуальной машины. 

Мы переносим эти тесты, чтобы вы могли сохранить над ними контроль даже при использовании нового портала. 

## <a name="what-is-application-insights?"></a>Что такое Azure Application Insights?
Новые тесты доступности являются частью [Visual Studio Application Insights](app-insights-overview.md). Просмотрите [2-минутный видеоролик](http://go.microsoft.com/fwlink/?LinkID=733921).

## <a name="am-i-paying-for-the-new-tests?"></a>Нужно ли платить за новые тесты?
Перенесенные тесты размещаются в ресурсе Application Insights в плане "Бесплатный" по умолчанию. Это позволяет собирать до 5 миллионов точек данных. Вы легко вписываетесь в объем данных, используемый тестами в настоящее время. 

Конечно, если вам нравится Application Insights и вы создаете дополнительные тесты доступности или реализуете большую часть возможностей диагностики и наблюдения за производительностью, вы можете создать дополнительные точки данных.  Однако в результате вы только можете достичь квоты, выделенной для плана "Бесплатный". Вы получите счет только после перехода на план "Стандартный" или "Премиум". 

[Узнайте больше о ценах на Application Insights и отслеживании квот](app-insights-pricing.md). 

## <a name="what-is-and-isn't-migrated?"></a>Что переносится и что не переносится?
Из старых тестов конечных точек сохраняются следующие компоненты:

* URL-адрес тестируемой конечной точки;
* георасположения, из которых отправляются запросы;
* прежняя периодичность проверки — 5 минут;
* прежнее время ожидания тестов — 30 секунд. 

Не переносятся:

* Правило триггера оповещения. Правило, которое мы настроили, срабатывает в случае, если 1 расположение сообщает об сбоях в течение 5 минут.
* Получатели оповещений. Электронные уведомления будут отправляться владельцам и совладельцам подписок. 

## <a name="how-do-i-find-the-new-tests?"></a>Как найти новые тесты?
Если необходимо, вы можете изменить любой новый тест прямо сейчас. Войдите на [портал Azure](https://portal.azure.com), откройте **Группы ресурсов** и выберите **Default-ApplicationInsights-CentralUS**. В этой группе вы найдете новые веб-тесты. [Узнайте о новых тестах доступности](app-insights-monitor-web-app-availability.md).

Обратите внимание, что новые оповещения будут отправляться с этого адреса: оповещения App Insights (ai-noreply@microsoft.com).

## <a name="what-happens-if-i-do-nothing?"></a>Что произойдет, если ничего не делать?
Будет применен вариант А. Мы включим перенесенные тесты и настроим правила оповещений по умолчанию, как было сказано выше. Вам будет необходимо добавить настраиваемые правила оповещений, получателей, как было сказано выше. Устаревшие тесты мониторинга конечных точек будут отключены. 

## <a name="where-can-i-provide-feedback-on-this?"></a>Где можно оставить отзыв?
Ваши отзывы важны для нас! Отправьте отзыв по [электронной почте](mailto:vsai@microsoft.com). 

<!--HONumber=Oct16_HO2-->


