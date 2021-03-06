---
title: "Прогнозное решение для оценки кредитных рисков с помощью машинного обучения | Документация Майкрософт"
description: "Подробное пошаговое руководство по созданию решения для прогнозной аналитики в Студии машинного обучения Azure для оценки кредитных рисков."
keywords: "кредитный риск, решение прогнозной аналитики, оценка рисков"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aa64dc7f5bb3e928aac30987b0904435c603829c


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Пошаговое руководство по разработке решения для прогнозной аналитики в службе машинного обучения Azure для оценки кредитных рисков
Предположим, требуется спрогнозировать кредитный риск претендентов на кредит на основе сведений, которые они предоставляют в заявке на кредит.  

Оценка кредитных рисков — несомненно, сложная задача, но давайте немного упростим ее вводные. Затем мы сможем использовать ее как пример того, как с помощью машинного обучения Microsoft Azure, Студии машинного обучения и веб-службы машинного обучения создать решение для прогнозной аналитики.  

В этой статье мы пошагово пройдем процесс разработки модели прогнозной аналитики в Студии машинного обучения, а затем развернем эту модель в веб-службе машинного обучения Azure. Для начала мы воспользуемся общедоступными данными по кредитным рискам, разработаем и обучим на основе этих данных прогнозную модель, а затем развернем ее как веб-службу, которую смогут использовать другие для оценки рисков по кредитам.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

> [!TIP]
> Чтобы скачать и распечатать схему, на которой представлены общие возможности Студии машинного обучения, см. [обзорную схему возможностей Студии машинного обучения Azure](machine-learning-studio-overview-diagram.md).
> 
> 

Создание нашего решения для оценки кредитных рисков предполагает такие действия:  

1. [Создание рабочей области машинного обучения](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Отправка существующих данных](machine-learning-walkthrough-2-upload-data.md)
3. [Создание нового эксперимента](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Обучение и анализ моделей](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Развертывание веб-службы](machine-learning-walkthrough-5-publish-web-service.md)
6. [Доступ к веб-службе](machine-learning-walkthrough-6-access-web-service.md)

В этом пошаговом руководстве используется упрощенная версия примера эксперимента [Двоичная классификация: прогнозирование кредитных рисков](http://go.microsoft.com/fwlink/?LinkID=525270) из [коллекции Cortana Intelligence](http://gallery.cortanaintelligence.com/).




<!--HONumber=Nov16_HO2-->


