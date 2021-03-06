---
title: Хранение кода проекта серверной части .NET в системе управления версиями | Microsoft Docs
description: Узнайте, как сохранить серверный проект .NET в локальном репозитории Git на компьютере и опубликовать его оттуда.
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Хранение кода проекта мобильной службы в системе управления версиями
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

> [!div class="op_single_selector"]
> * [Сервер .NET.](mobile-services-dotnet-backend-store-code-source-control.md)
> * [Серверная служба JavaScript](mobile-services-store-scripts-source-control.md)
> 
> 

В этой статье показано, как использовать систему управления версиями, предоставленную мобильными службами Azure, для хранения проекта серверной службы для .NET. Чтобы опубликовать проект, просто отправьте его из локального репозитория Git в рабочую мобильную службу.

Мобильную службу для работы с этим учебником вы должны были создать при изучении учебника [Приступая к работе с мобильными службами].

## <a name="enable-source-control"></a>Включение системы управления версиями в вашей мобильной службе
[!INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

## <a name="clone-repo"></a>Установка Git и создание локального репозитория
1. Установите Git на локальном компьютере.
   
    Действия, необходимые для установки Git, отличаются в разных операционных системах. Сведения о дистрибутивах для разных операционных систем и руководство по установке см. в разделе [Установка Git].
   
   > [!NOTE]
   > В некоторых операционных системах доступны обе версии Git — для командной строки и с графическим интерфейсом. В инструкциях, приведенных в данной статье, используется версия для командной строки.
   > 
   > 
2. Откройте окно командной строки, например **GitBash** (Windows) или **Bash** (оболочка Unix). На компьютерах с OS X доступ к командной строке можно получить через приложение **Terminal**.
3. В командной строке перейдите в каталог, где будут храниться сценарии. Пример: `cd SourceControl`.
4. Используйте следующую команду для создания локальной копии нового репозитория Git, заменив `<your_git_URL>` на URL-адрес репозитория Git для своей мобильной службы:
   
        git clone <your_git_URL>
5. При отображении запроса введите имя пользователя и пароль, заданные при включении системы управления версиями в вашей мобильной службе. После успешной проверки подлинности должна отобразиться последовательность ответов:
   
        remote: Counting objects: 8, done.
        remote: Compressing objects: 100% (4/4), done.
        remote: Total 8 (delta 1), reused 0 (delta 0)
        Unpacking objects: 100% (8/8), done.
6. Перейдите в каталог, из которого была выполнена команда `git clone`, и убедитесь, что новому каталогу присвоено имя мобильной службы. Для серверной мобильной службы для .NET репозиторий Git изначально пуст.

После создания локального репозитория можно опубликовать проект серверной службы для .NET из этого репозитория.

## <a name="deploy-scripts"></a>Публикация проекта с использованием Git
1. Создайте новый проект серверной службы для .NET в Visual Studio 2013 или перенесите существующий проект в новый локальный репозиторий.
   
    Для быстрой проверки загрузите и сохраните в эту папку проект быстрого запуска мобильных служб.
2. Удалите все папки пакета NuGet, оставив файл packages.config.
   
    Мобильные службы автоматически восстановят пакеты NuGet на основе файла packages.config. А с помощью файла .gitignore можно задать каталоги, которые не войдут в пакет.
3. В командной строке Git введите следующую команду, чтобы запустить отслеживание нового файла сценария:
   
        $ git add .
4. Введите следующую команду, чтобы сохранить изменения:
   
        $ git commit -m "adding the .NET backend service project"
5. Введите такую команду, чтобы передать изменения в удаленный репозиторий, и введите свои учетные данные:
   
        $ git push origin master
   
    Вы увидите несколько команд, подтверждающих, что проект развернут в мобильных службах, пакеты добавлены, а служба перезапущена.
6. Перейдя по URL-адресу серверной мобильной службы для .NET, вы увидите такое сообщение:
   
    ![Стартовая страница мобильных служб](./media/mobile-services-dotnet-backend-store-code-source-control/mobile-service-startup.png)

Теперь ваш проект мобильной службы сохраняется в системе управления версиями, а вы можете публиковать обновления службы, просто перенося обновления из своего локального репозитория. Сведения о том, как изменять модели данных в серверных мобильных службах для .NET, использующих базы данных SQL, можно найти в статье [Изменение модели данных в серверной мобильной службе для .NET].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Git website]: http://git-scm.com
[Source control]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Установка Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-ios-get-started.md
[Изменение модели данных в серверной мобильной службе для .NET]: mobile-services-dotnet-backend-how-to-use-code-first-migrations.md

<!---HONumber=AcomDC_0727_2016-->