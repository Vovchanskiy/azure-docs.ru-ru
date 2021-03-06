---
title: Ресурсы для рабочих нагрузок пакетной службы HPC в облаке | Microsoft Docs
description: В этой статье приведены технические ресурсы, которые помогут вам выполнять крупномасштабные параллельные, пакетные и высокопроизводительные вычисления (HPC) в Azure.
services: batch, cloud-services, virtual-machines
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''

ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 09/22/2016
ms.author: danlep

---
# Большие вычисления в Azure: технические ресурсы для пакетных и высокопроизводительных вычислений
Это руководство по техническим ресурсам, которые помогут вам выполнять крупномасштабные параллельные, пакетные и высокопроизводительные вычисления (HPC) в Azure. Перенесите свои текущие пакетные рабочие нагрузки или рабочие нагрузки HPC в облако Azure или создайте новые решения для больших вычислений с помощью набора служб Azure.

## Варианты решений
Узнайте, какие решения для больших вычислений существуют в Azure, и выберите вариант, который подходит для рабочих нагрузок и потребностей вашей организации.

* [Пакетные и высокопроизводительные вычислительные решения](batch-hpc-solutions.md)
* [Большие вычисления в облаке с помощью Azure и HPC (видео)](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)

## Пакетная служба Azure
[Пакетная служба](https://azure.microsoft.com/services/batch/) — это платформа как услуга, которая позволяет легко переводить приложения Linux и Windows в облако и выполнять задания без настройки кластера или планировщика и управления ими. С помощью пакета SDK вы можете интегрировать клиентские приложения в Пакетную службу Azure, используя различные языки, размещать данные в Azure и создавать конвейеры выполнения заданий.

* [Документация](https://azure.microsoft.com/documentation/services/batch/)
* Справочник по API [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx), [Python](http://azure-sdk-for-python.readthedocs.io/latest/), [Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/), [Java](http://azure.github.io/azure-sdk-for-java/) и [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)
* Справочник по [библиотеке .NET для управления пакетной службой](https://msdn.microsoft.com/library/mt463120.aspx)
* Учебники: начало работы с [библиотекой пакетной службы Azure для .NET](batch-dotnet-get-started.md) и [клиентом Python пакетной службы](batch-python-tutorial.md)
* [Форум по Пакетной службе](https://social.msdn.microsoft.com/Forums/ru-RU/home?forum=azurebatch)
* [Видеоролики о Пакетной службе](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Кластерные решения HPC
Разверните или расширьте свой существующий кластер HPC под управлением Windows или Linux в службу Azure и выполняйте ресурсоемкие вычислительные рабочие нагрузки в облаке.

### Пакет Microsoft HPC
HPC Pack — это бесплатное решение HPC корпорации Майкрософт, созданное на основе технологий Microsoft Azure и Windows Server и поддерживающее рабочие нагрузки HPC как в Windows, так и в Linux.

* [Загрузить пакет HPC 2012 R2 с обновлением 3](https://www.microsoft.com/download/details.aspx?id=49922)
* [Документация](https://technet.microsoft.com/library/jj899572.aspx)
* Варианты кластера пакета HPC под управлением [Linux](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md) и [Windows](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md) в Azure
* [Ускорение в рабочие экземпляры Azure с помощью пакета HPC](https://technet.microsoft.com/library/gg481749.aspx)
* [Ускорение в пакетную службу Azure с помощью пакета HPC](https://technet.microsoft.com/library/mt612877.aspx)
* [Форумы по Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### Кластерные решения Linux и OSS
Используйте эти шаблоны Azure для развертывания кластеров HPC под управлением Linux.

* Шаблон [Spin up a SLURM cluster](https://azure.microsoft.com/documentation/templates/slurm/) и [запись блога](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
* [Шаблон Spin up a Torque cluster](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [Intel Cloud Edition для Lustre Software — пробная версия](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)

## Microsoft MPI
[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) — это реализация стандарта MPI (Message Passing Interface — интерфейс передачи сообщений) от Майкрософт, предназначенная для разработки и запуска параллельных приложений на платформе Windows. Последней версией является MS-MPI версии 7.

* [Скачать MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)
* [Справочник по MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)
* [Форум по MPI](https://social.microsoft.com/Forums/ru-RU/home?forum=windowshpcmpi)

## Экземпляры для ресурсоемких вычислений
В Azure доступны [разные размеры виртуальных машин](../virtual-machines/virtual-machines-windows-sizes.md), включая экземпляры [для ресурсоемких вычислений](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md), поддерживающие подключение к внутренней сети RDMA для выполнения рабочих нагрузок HPC в Linux и Windows.

* [Настройка кластера Linux RDMA для выполнения приложений MPI](../virtual-machines/virtual-machines-linux-classic-rdma-cluster.md)
* [Настройка кластера RDMA в Windows с помощью Microsoft HPC Pack для выполнения приложений MPI](../virtual-machines/virtual-machines-windows-classic-hpcpack-rdma-cluster.md)

## Примеры и образцы
* [Примеры кода для пакетной службы Azure на C# и Python](https://github.com/Azure/azure-batch-samples)
* Набор средств [Batch Shipyard](https://azure.github.io/batch-shipyard/) для удобного развертывания пакетных рабочих нагрузок Docker
* [Пробная установка сервера SUSE Linux Enterprise Server для HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## Связанные службы Azure
* [Фабрика данных](https://azure.microsoft.com/documentation/services/data-factory/)
* [Машинное обучение](https://azure.microsoft.com/documentation/services/machine-learning/)
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Виртуальные машины](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Наборы для масштабирования виртуальных машин](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)
* [Облачные службы](https://azure.microsoft.com/documentation/services/cloud-services/)
* [Служба приложений](https://azure.microsoft.com/documentation/services/app-service/)
* [Службы мультимедиа](https://azure.microsoft.com/documentation/services/media-services/)
* [Функции](https://azure.microsoft.com/documentation/services/functions/)

## Проекты архитектуры
* [Управление данными и высокопроизводительными вычислениями с помощью пакетной службы и фабрики данных Azure](http://go.microsoft.com/fwlink/?linkid=717686) (PDF-файл) и [статья](../data-factory/data-factory-data-processing-using-batch.md)

## Отраслевые решения
* [Банки и рынки ценных бумаг](https://finance.azure.com/)
* [Инженерное моделирование](https://simulation.azure.com/)

## Истории клиентов
* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168)
* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)
* [Институт исследования рака Людвига](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)
* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)
* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)
* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

## Дальнейшие действия
* Последние объявления см. в [блоге группы Microsoft HPC и Пакетной службы](http://blogs.technet.com/b/windowshpc/), а также в [блоге Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Следите также за лентой [новостей о Пакетной службе](https://azure.microsoft.com/updates/?service=batch) или подпишитесь на [RSS-канал](https://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=AcomDC_0928_2016-->