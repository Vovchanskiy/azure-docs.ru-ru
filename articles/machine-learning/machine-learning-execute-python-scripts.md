---
title: Выполнение сценариев машинного обучения Python | Microsoft Docs
description: Описываются принципы проектирования, лежащие в основе поддержки сценариев Python в Машинном обучении Azure, а также основные сценарии использования, возможности и ограничения.
keywords: машинное обучение Python, pandas, python pandas, сценарии python, выполнение сценариев python
services: machine-learning
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: bradsev

---
# Выполнение сценариев Python в Студии машинного обучения Azure
В этой статье описываются принципы проектирования, лежащие в основе текущей поддержки сценариев Python в Машинном обучении Azure. Здесь также описаны основные возможности, включая поддержку импорта существующего кода и экспорта визуализаций, а также рассматриваются некоторые ограничения и особенности текущей работы.

[Python](https://www.python.org/) — незаменимый инструмент в наборе инструментов многих специалистов по обработке и анализу данных. Он имеет:

* изящный и краткий синтаксис;
* кроссплатформенную поддержку;
* обширную коллекцию мощных библиотек;
* современные средства разработки.

Python используется на всех этапах рабочего процесса — от приема и обработки данных до создания компонентов, обучения, проверки и развертывания моделей — обычно используемых при моделировании в машинном обучении.

Студия машинного обучения Azure позволяет внедрять сценарии Python в различных частях эксперимента машинного обучения, а также легко опубликовывать их в качестве масштабируемых, рабочих веб-служб в Microsoft Azure.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Принципы проектирования сценариев Python в машинном обучении
Основным интерфейсом Python в Студии машинного обучения Azure является модуль [Выполнение сценария Python][execute-python-script], как показано на рис. 1.

![рисунок 1](./media/machine-learning-execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![рисунок 2](./media/machine-learning-execute-python-scripts/embedded-machine-learning-python-script.png)

Рисунок 1. Модуль **Выполнение сценария Python**

Модуль [Execute Python Script][execute-python-script] \(Выполнение сценария Python) может принимать три вида входных данных и выдавать два вида выходных данных (описанных ниже), так же, как и его аналог для языка R — модуль [Execute R Script][execute-r-script] \(Выполнение сценария R). Выполняемый код Python вводится в поле параметра в качестве специально именованной функции точки входа `azureml_main`. Ниже приведены ключевые принципы проектирования, используемые для реализации этого модуля.

1. *Привычность для пользователей Python.* Большинство пользователей Python видят свой код как функции внутри модулей. Поэтому они достаточно редко помещают множество исполняемых операторов в модуль верхнего уровня. В результате в поле сценария можно также ввести специально именованную функцию Python, а не только последовательность операторов. Объекты, представленные в функции, принадлежат к стандартным типам библиотек Python, таким как кадры данных [Pandas](http://pandas.pydata.org/) и массивы [NumPy](http://www.numpy.org/).
2. *Высокая точность при выполнении в локальной и облачной среде.* Серверная часть, используемая для выполнения кода Python, работает на базе широко используемого кроссплатформенного дистрибутива Python [Anaconda](https://store.continuum.io/cshop/anaconda/) 2.1 для обработки и анализа данных. Он поставляется с около 200 наиболее распространенными пакетами Python. Таким образом, специалисты по обработке и анализу данных могут отладить код и обеспечить его работу в своей локальной среде Anaconda, совместимой с Машинным обучением Azure. Затем с помощью существующих сред разработки, таких как [IPython](http://ipython.org/) Notebook или [инструменты Python для Visual Studio](http://aka.ms/ptvs), выполнить его с высокой степенью уверенности как часть эксперимента Машинного обучения Azure. Кроме того, точка входа `azureml_main` — стандартная функция Python, которую можно создать без использования кода для Машинного обучения Azure или установленного пакета SDK.
3. *Простота при совместном использовании с другими модулями Машинного обучения Azure.* Модуль [Выполнение сценария Python][execute-python-script] в качестве входных и выходных данных принимает стандартные наборы данных Машинного обучения Azure. Базовая платформа просто и эффективно соединяет среду выполнения Машинного обучения Azure и Python (с поддержкой таких компонентов, как отсутствующие значения). Поэтому Python можно использовать в сочетании с существующими рабочими процессами Машинного обучения Azure, включая те, которые вызывают R и SQLite. Таким образом, можно представить рабочие процессы:
   * использующие Python и Pandas для предварительной обработки и очистки данных;
   * передающие данные для преобразования SQL, объединяя несколько наборов данных для создания компонентов;
   * выполняющие обучение моделей с помощью обширного набора алгоритмов в Машинном обучении Azure;
   * оценивающие и обрабатывающие результаты впоследствии с помощью языка R.

## Основные сценарии использования в машинном обучении для сценариев Python
В этом разделе мы рассмотрим некоторые из основных вариантов использования модуля [Выполнение сценария Python][execute-python-script]. Как упоминалось ранее, любые входные данные, вводимые в модуль Python, представляются в качестве кадров данных Pandas. Дополнительную информацию о библиотеке Python Pandas и о том, как ее можно использовать для эффективного управления данными, см. в книге В. Маккини (W. McKinney) *Python для анализа данных* (O'Reilly, 2012 г.). Эта функция должна возвратить один кадр данных Pandas, упакованный в [последовательности](https://docs.python.org/2/c-api/sequence.html) Python, такой как кортеж, список или массив NumPy. Затем первый элемент из этой последовательности возвращается в первый выходной порт модуля. Схема этого процесса показана на рис. 2.

![image3](./media/machine-learning-execute-python-scripts/map-of-python-script-inputs-outputs.png)

Рис. 2. Сопоставление входных портов с параметрами и возвращаемого значения с входным портом

Более подробная семантика сопоставления входных портов с параметрами функции `azureml_main` показана в таблице 1:

![image1T](./media/machine-learning-execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Таблица 1. Сопоставление входных портов с параметрами функции

Сопоставление входных портов и параметров функции выполняется по позиционному принципу. Первый подключенный входной порт сопоставляется с первым параметром функции, а второй входной порт (если он подключен) — со вторым параметром функции.

## Преобразование типов входных и выходных данных
Как упоминалось ранее, входные наборы данных в Машинном обучении Azure преобразуются в кадры данных Pandas, а выходные кадры данных преобразуются обратно в наборы данных Машинного обучения Azure. Выполняются следующие преобразования:

1. Строковые и числовые столбцы преобразуются как есть, а отсутствующие значения в наборе данных преобразуются в значения NA в Pandas. То же самое происходит и при обратном преобразовании (значения NA в Pandas преобразуются в отсутствующие значения в Машинном обучении Azure).
2. Индексные векторы в Pandas не поддерживаются в Машинном обучении Azure. Все входные кадры данных в функции Python будут всегда содержать 64-разрядный числовой индекс от 0 до соответствующего количества строк минус 1.
3. Наборы данных Машинного обучения Azure не могут содержать повторяющиеся имена столбцов и имена столбцов, которые не являются строками. Если выходной кадр данных содержит столбцы с нечисловыми значениями, платформа вызывает функцию `str` для имен столбцов. Аналогичным образом все повторяющиеся имена столбцов автоматически корректируются, чтобы обеспечить их уникальность. Суффикс (2) добавляется к первой копии, (3) — ко второй копии и т. д.

## Выполнение сценариев Python
При публикации в качестве веб-службы вызываются любые модули [Выполнение сценария Python][execute-python-script] в оценивающем эксперименте. Например, на рис. 3 показан эксперимент по оценке, содержащий код для оценки одного выражения Python.

![image4](./media/machine-learning-execute-python-scripts/figure3a.png)

![рисунок 5](./media/machine-learning-execute-python-scripts/python-script-with-python-pandas.png)

Рис. 3. Веб-служба для оценки выражения Python

Веб-служба, созданная в этом эксперименте, принимает в качестве входных данных выражение Python (в виде строки), отправляет его в интерпретатор Python и возвращает таблицу, содержащую выражение и результат оценки.

## Импорт существующих модулей сценариев Python
Специалисты по обработке и анализу данных в качестве основного варианта используют включение существующих сценариев Python в эксперименты Машинного обучения Azure. Вместо объединения и вставки всего кода в одно поле сценария модуль [Выполнение сценария Python][execute-python-script] принимает третий входной порт, к которому можно подключить ZIP-файл с модулями Python. Затем этот файл распаковывается во время выполнения в среда выполнения, а его содержимое добавляется в путь к библиотеке интерпретатора Python. После этого функция точки входа `azureml_main` может импортировать эти модули напрямую.

Рассмотрим в качестве примера файл Hello.py, содержащий простую функцию Hello, World.

![рисунок 6](./media/machine-learning-execute-python-scripts/figure4.png)

Рис. 4 Определяемая пользователем функция

Далее мы создаем файл Hello.zip, содержащий файл Hello.py:

![рисунок 7](./media/machine-learning-execute-python-scripts/figure5.png)

Рис. 5. ZIP-файл, содержащий определяемый пользователем код Python

Теперь отправьте его в качестве набора данных в Студию машинного обучения Azure. Создайте и запустите простой эксперимент, использующий код Python в файле Hello.zip, подключив его к третьему входному порту модуля Execute Python Script (Выполнение сценария Python), как показано на этом рисунке.

![рисунок 8](./media/machine-learning-execute-python-scripts/figure6a.png)

![image9](./media/machine-learning-execute-python-scripts/figure6b.png)

Рис. 6. Пример эксперимента с определяемым пользователем кодом Python, загруженным в качестве ZIP-файла

В выходных данных модуля можно увидеть, что ZIP-файл был распакован, и функция `print_hello` была действительно выполнена. ![image10](./media/machine-learning-execute-python-scripts/figure7.png)

Рис. 7. Определяемая пользователем функция, используемая в модуле [Выполнение сценария Python][execute-python-script]

## Работа с визуализациями
Модуль [Выполнение сценария Python][execute-python-script] может возвратить созданные с помощью MatplotLib графики, которые можно визуализировать в браузере. Но эти графики не будут автоматически перенаправляться к изображениям, как это происходит при использовании языка R. Поэтому пользователь должен явным образом сохранить любые графики в PNG-файлы, если они должны быть возвращены в Машинное обучение Azure.

Чтобы создавать изображения с помощью MatplotLib, выполните следующую процедуру:

* Переключитесь с серверной части на AGG в стандартном обработчике на базе Qt.
* Создайте новый объект рисунка.
* Получите ось и постройте все графики по ней.
* Сохраните рисунок в PNG-файл.

Этот процесс показан на приведенном ниже рис. 8. Создается матрица точечной диаграммы с помощью функции scatter\_matrix в Pandas.

![image1v](./media/machine-learning-execute-python-scripts/figure-v1-8.png)

Рис. 8. Сохранение рисунков MatplotLib в изображения

На рис. 9 показан эксперимент, в котором используется сценарий, показанный ранее, для возвращения графиков через второй выходной порт.

![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9a.png)

![image2v](./media/machine-learning-execute-python-scripts/figure-v2-9b.png)

Рис. 9. Визуализация графиков, созданных из кода Python

Можно получить несколько рисунков, сохраняя их в разных изображениях. Среда выполнения Машинного обучение Azure собирает все изображения и объединяет их для визуализации.

## Дополнительные примеры
Среда Anaconda, установленная в Машинном обучении Azure, содержит общие пакеты, такие как NumPy, SciPy и Scikits-Learn, которые можно эффективно использовать для различных задач обработки данных в типичном конвейере машинного обучения. К примеру, в следующем эксперименте и сценарии продемонстрировано использование объединенных учеников в Scikits-Learn для вычисления оценки важности компонента для набора данных. Эти оценки затем можно использовать для контролируемого выбора компонентов перед отправкой в другую модель машинного обучения.

Ниже показана функция Python, используемая для вычисления оценки важности и упорядочивания компонентов на основе этой оценки:

![image11](./media/machine-learning-execute-python-scripts/figure8.png)

Рис. 10. Функция для ранжирования компонентов по оценкам   Затем следующий эксперимент вычисляет и возвращает оценки важности компонента в наборе данных «Диабет у индейцев пима» в Машинном обучении Azure:

![image12](./media/machine-learning-execute-python-scripts/figure9a.png) ![image13](./media/machine-learning-execute-python-scripts/figure9b.png)

Рис. 11. Эксперимент по ранжированию компонентов в наборе данных «Диабет у индейцев пима»

## Ограничения
Сейчас модуль [Выполнение сценария Python][execute-python-script] имеет следующие ограничения:

1. *Выполнение в изолированном режиме.* Сейчас среда выполнения Python выполняется в изолированном режиме, в результате чего в ней запрещен постоянный доступ к сети или к локальной файловой системе. Все файлы, сохраненные локально, изолированы и удаляются после завершения выполнения модуля. С помощью кода Python нельзя получить доступ к большинству каталогов на компьютере, на котором он выполняется, за исключением текущего каталога и его подкаталогов.
2. *Отсутствие расширенной поддержки разработки и отладки.* В настоящее время модуль Python не поддерживает возможности интегрированной среды разработки, такие как Intellisense и отладка. Кроме того, при сбое модуля во время выполнения доступна полная трассировка стека Python, но ее необходимо просматривать в журнале выходных данных модуля. Сейчас мы советуем вам разрабатывать и отлаживать сценарии Python в таких средах, как IPython, а затем импортировать код в модуль.
3. *Один кадр данных в качестве выходных данных.* Точке входа Python разрешено возвращать только один кадр данных в качестве выходных данных. Сейчас невозможно возвращать такие произвольные объекты Python, как обученные модели, непосредственно в среду выполнения Машинного обучения Azure. Как и в модуле [Выполнение сценария R][execute-r-script], который имеет те же ограничения, во многих случаях возможно поместить объект в массив байтов, а затем возвратить его в кадр данных.
4. *Невозможность настроить установку Python.* Сейчас единственный возможный способ добавить пользовательские модули Python — применение механизма с использованием ZIP-файла, описанного выше. Хотя этот механизм можно применять для небольших модулей, он не подходит для больших модулей (особенно с встроенными библиотеками DLL) или большого количества модулей.

## Заключение
Модуль [Выполнение сценария Python][execute-python-script] позволяет специалисту по обработке и анализу данных внедрять существующий код Python в рабочие процессы машинного обучения, размещенные в облаке, в Машинном обучении Azure и легко выполнять их как часть веб-службы. Модуль сценария Python легко взаимодействует с другими модулями в Машинном обучении Azure и может использоваться в ряде задач, начиная с просмотра данных и заканчивая предварительной обработкой, извлечением компонентов, оценкой и последующей обработкой результатов. Серверная среда выполнения, используемая для выполнения, работает на базе Anaconda — хорошо проверенного и широко используемого дистрибутива Python. Он упрощает для вас размещение существующих ресурсов кода в облако.

Мы планируем ввести в модуль [Execute Python Script][execute-python-script] \(Выполнение сценария Python) дополнительные функциональные возможности, такие как обучение и выполнение моделей в Python, а также добавить улучшенную поддержку разработки и отладки кода в Студию машинного обучения Azure.

## Дальнейшие действия
Дополнительные сведения см. в [Центре разработчика Python](/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

<!---HONumber=AcomDC_0914_2016-->