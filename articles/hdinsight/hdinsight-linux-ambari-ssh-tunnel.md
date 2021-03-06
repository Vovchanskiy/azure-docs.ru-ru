---
title: Использование туннелирования SSH для доступа к веб-интерфейсу Ambari, ResourceManager, JobHistory, NameNode, Oozie и другим веб-интерфейсам
description: Узнайте, как безопасно просматривать веб-ресурсы, размещенные на узлах HDInsight под управлением Linux, с помощью туннеля SSH.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2016
ms.author: larryfr

---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui,-jobhistory,-namenode,-oozie,-and-other-web-ui's"></a>Использование туннелирования SSH для доступа к веб-интерфейсу Ambari, JobHistory, NameNode, Oozie и другим веб-интерфейсам
Кластеры HDInsight под управлением Linux предоставляют доступ к веб-интерфейсу Ambari через Интернет, однако при этом доступны не все функции веб-интерфейса. Это относится, например, к веб-интерфейсу других служб, которые доступны через Ambari. Чтобы получить доступ ко всем функциям веб-интерфейса Ambari, необходимо настроить туннель SSH к головному узлу кластера.

## <a name="what-requires-an-ssh-tunnel?"></a>Для доступа к каким элементам интерфейса необходим туннель SSH?
Без туннеля SSH некоторые меню в Ambari будут содержать не все пункты, так как для заполнения этих меню используются веб-сайты и службы, предоставляемые другими службами Hadoop, запущенными в кластере. Часто эти веб-сайты не защищены, поэтому небезопасно открывать прямой доступ к ним через Интернет. Иногда под управлением службы работает веб-сайт на другом узле кластера, например на узле Zookeeper.

Ниже перечислены службы веб-интерфейса Ambari, доступ к которым без использования туннеля SSH невозможен.

* JobHistory,
* NameNode,
* стеки потоков,
* веб-интерфейс Oozie
* веб-интерфейс главного узла и журналов HBase

При использовании действий сценариев для настройки кластера туннель SSH будет необходим для всех служб и программ, которые вы установили для доступа к веб-интерфейсу. Например, если вы установили Hue с помощью действия сценария, для доступа к веб-интерфейсу Hue потребуется туннель SSH.

## <a name="what-is-an-ssh-tunnel?"></a>Что такое туннель SSH?
[Туннель SSH](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) направляет трафик, приходящий на порт локальной рабочей станции, через подключение SSH на головной узел кластера HDInsight, на котором запрос затем разрешается так же, как если бы он был создан на головном узле. Ответ рабочей станции отправляется обратно через туннель.

## <a name="prerequisites"></a>Предварительные требования
При использовании туннеля SSH для веб-трафика необходимо иметь следующее:

* Клиент SSH. В дистрибутивах Linux и Unix и Macintosh OS X команда `ssh` входит в состав операционной системы. Для Windows рекомендуем воспользоваться [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
  
  > [!NOTE]
  > Если вы планируете использовать клиент SSH, отличный от `ssh` или PuTTY, обратитесь к документации своего клиента за информацией о настройке туннеля SSH.
  > 
  > 
* Веб-браузер, который можно настроить на использование прокси-сервера SOCKS
* **(Необязательно)**: подключаемый модуль, такой как [FoxyProxy](http://getfoxyproxy.org/,) , который может применить правила маршрутизации для пропускания через туннель только определенных запросов.
  
  > [!WARNING]
  > Без такого подключаемого модуля, как FoxyProxy, все запросы, сделанные через браузер, могут направляться через туннель. Это может привести к замедлению загрузки веб-страниц в браузере.
  > 
  > 

## <a name="<a-name="usessh"></a>create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Создание туннеля с помощью команды SSH
Используйте следующую команду для создания туннеля SSH с помощью команды `ssh` . Замените **USERNAME** именем пользователя SSH для кластера HDInsight, а **CLUSTERNAME** — именем кластера HDInsight.

    ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

В результате создается подключение, которое направляет трафик, поступающий на локальный порт 9876, в кластер по протоколу SSH. Доступны следующие параметры.

* **D 9876** — локальный порт, используемый для направления трафика через туннель;
* **C** — сжатие всех данных, так как веб-трафик преимущественно состоит из текста;
* **2** — принудительная попытка использовать только протокол SSH версии 2;
* **q** — тихий режим;
* **T** — отключение распределения псевдотелетайпа, так как выполняется только перенаправление порта;
* **n** — предотвращение считывания с STDIN, так как выполняется только перенаправление порта;
* **N** — запрет на выполнение удаленной команды, так как выполняется только перенаправление порта;
* **f** — выполнение в фоновом режиме.

При настройке кластера с ключом SSH может потребоваться использовать параметр `-i` и указать путь к закрытому ключу SSH.

После завершения команды трафик, отправленный на порт 9876 на локальном компьютере, будет направляться с использованием SSL на головной узел кластера и выглядеть так, будто его источником является головной узел.

## <a name="<a-name="useputty"></a>create-a-tunnel-using-putty"></a><a name="useputty"></a>Создание туннеля с помощью PuTTY
Для создания туннеля SSH с помощью PuTTY выполните следующие действия.

1. Откройте PuTTY и введите информацию о подключении. Если вы не знакомы с PuTTY, обратитесь к разделу [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md) , чтобы узнать, как пользоваться PuTTY с HDInsight.
2. В разделе **Категории** в левой части диалогового окна последовательно разверните **Подключение**, **SSH** и выберите **Туннели**.
3. Введите следующую информацию в форме **Параметры, управляющие перенаправлением портов SSH** :
   
   * **Порт источника** — порт на стороне клиента, трафик которого нужно перенаправлять. Например, **9876**.
   * **Назначение** — адрес SSH для кластера HDInsight под управлением Linux. Например, **mycluster-ssh.azurehdinsight.net**.
   * **Динамическая** — включает динамическую маршрутизацию прокси-сервера SOCKS.
     
     ![изображение параметров туннелирования](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)
4. Щелкните **Добавить**, чтобы добавить параметры, а затем щелкните **Открыть**, чтобы открыть подключение SSH.
5. При появлении запроса войдите на сервер. При этом будет установлен сеанс SSH и включен туннель.

## <a name="use-the-tunnel-from-your-browser"></a>Использование туннеля из браузера
> [!NOTE]
> Для действий, описанных в этом разделе, используется браузер FireFox, так как он свободно доступен для систем Linux, Unix, Macintosh OS X и Windows. Другие современные браузеры, такие как Google Chrome, Microsoft Edge или Apple Safari, также должны работать, однако подключаемый модуль FoxyProxy, используемый в некоторых действиях, может быть недоступен для некоторых браузеров.
> 
> 

1. Настройте браузер для использования **localhost:9876** в качестве прокси-сервера **SOCKS v5**. Вот как выглядят параметры Firefox. Если используется порт, отличный от 9876, измените номер порта соответствующим образом.
   
    ![изображение параметров Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)
   
   > [!NOTE]
   > Если выбрать параметр **Удаленная служба DNS** , то запросы DNS будут разрешаться с помощью кластера HDInsight. Если этот параметр не выбран, запросы DNS будут разрешаться локально.
   > 
   > 
2. Чтобы проверить, что трафик направляется через туннель, зайдите на сайт [http://www.whatismyip.com/](http://www.whatismyip.com/) при включенных и выключенных параметрах прокси-сервера в Firefox. Если настройки включены, IP-адрес будет относиться к компьютеру в центре обработки данных Microsoft Azure.

### <a name="browser-extensions"></a>Расширения браузера
Обычно при настройке браузера для использования туннеля вы не хотите направлять через туннель весь трафик. Расширения браузера, такие как [FoxyProxy](http://getfoxyproxy.org/), поддерживают сопоставление шаблонов для запросов URL-адресов (только FoxyProxy Standard или Plus), что позволяет отправлять через туннель только запросы определенных URL-адресов.

Если вы установили FoxyProxy Standard, выполните следующие действия, чтобы настроить его для перенаправления через туннель только трафика для HDInsight.

1. Откройте расширение FoxyProxy в браузере. Например, в Firefox щелкните значок FoxyProxy рядом с полем адреса.
   
    ![значок FoxyProxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxy.png)
2. Щелкните **Add New Proxy** (Добавить новый прокси-сервер), а затем выберите вкладку **Общие** и введите имя прокси-сервера **HDInsightProxy**.
   
    ![вкладка "Общие" FoxyProxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxygeneral.png)
3. Выберите вкладку **Сведения о прокси** и заполните следующие поля.
   
   * **Хост или IP-адрес** — необходимо указать значение localhost, так как мы используем туннель SSH на локальном компьютере.
   * **Порт** — порт, используемый для туннеля SSH.
   * **Прокси-сервер SOCKS** — выберите этот параметр, чтобы позволить браузеру использовать туннель в качестве прокси-сервера.
   * **SOCKS v5** — выберите этот параметр, чтобы задать требуемую версию прокси-сервера.
     
     ![прокси-сервер FoxyProxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxyproxy.png)
4. Выберите вкладку **URL Patterns** (Шаблоны URL-адреса), а затем щелкните **Add New Pattern** (Добавить новый шаблон). Используйте следующие значения параметров, чтобы определить шаблон, а затем нажмите кнопку **ОК**:
   
   * **Pattern Name** (Имя шаблона) - **clusternodes**. Это просто понятное имя для шаблона.
   * **URL pattern** (Шаблон URL-адреса) - **\*internal.cloudapp.net\***. Определяет шаблон, который соответствует внутреннему полному доменному имени узлов кластера.
     
     ![шаблон FoxyProxy](./media/hdinsight-linux-ambari-ssh-tunnel/foxypattern.png)
     
     Добавьте другой шаблон, используя следующие сведения в качестве параметров:
   * **Имя шаблона** — headnode
   * **URL pattern** (Шаблон URL-адреса) - \*headnodehost\*.
     
     Нажмите кнопку "ОК", чтобы сохранить этот шаблон.
5. Нажмите кнопку **ОК**, чтобы добавить прокси-сервер и закрыть окно **Параметры прокси-сервера**.
6. В верхней части диалогового окна FoxyProxy из раскрывающегося списка **Выбрать режим** выберите пункт **Use proxies based on their pre-defined patterns and priorities** (Использовать прокси-серверы на основе предопределенных шаблонов и приоритетов), а затем нажмите кнопку **Закрыть**.
   
    ![выбор режима FoxyProxy](./media/hdinsight-linux-ambari-ssh-tunnel/selectmode.png)

После выполнения этих действий только запросы URL-адресов, которые содержат строку **internal.cloudapp.net** , будут направляться через туннель SSL.

## <a name="verify-with-ambari-web-ui"></a>Проверка для веб-интерфейса Ambari
После настройки кластера выполните следующие действия, чтобы удостовериться, что веб-интерфейсы служб доступны из веб-интерфейса Ambari.

1. В браузере перейдите по адресу: http://headnodehost:8080. Адрес `headnodehost` будет отправлен через туннель в кластер и преобразован в головной узел, на котором выполняется Ambari. При появлении запроса введите имя пользователя (admin) и пароль учетной записи администратора кластера. Веб-интерфейс Ambari может потребовать повторного ввода имени пользователя и пароля. В этом случае повторно введите имя пользователя и пароль.
   
   > [!NOTE]
   > При использовании адреса http://headnodehost:8080 для подключения к кластеру вы подключаетесь через туннель непосредственно к головному узлу, на котором выполняется Ambari, с помощью протокола HTTP, и безопасность обмена данными обеспечивается туннелем SSH. При подключении через Интернет без использования туннеля обмен данными защищается с помощью протокола HTTPS. Для подключения через Интернет по протоколу HTTPS используйте https://<имя_кластера>.azurehdinsight.net, где **имя_кластера** — имя кластера.
   > 
   > 
2. В веб-интерфейсе Ambari выберите HDFS в списке в левой части страницы.
   
    ![Изображение с выбранным пунктом HDFS](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)
3. После появления информации о службе HDFS выберите **Быстрые ссылки**. Появится список головных узлов кластера. Выберите один из головных узлов, а затем выберите **NameNode UI**(Пользовательский интерфейс NameNode).
   
    ![Изображение с развернутым меню "Быстрые ссылки"](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)
   
   > [!NOTE]
   > Если подключение к Интернету медленное или головной узел занят, при выборе меню __Быстрые ссылки__вместо меню отображается индикатор ожидания. В этом случае подождите одну-две минуты для получения данных с сервера, а затем повторите попытку.
   > 
   > Если у вас монитор с низким разрешением или окно браузера не развернуто, некоторые записи в меню **Быстрые ссылки** могут быть обрезаны правым краем экрана. В этом случае разверните меню с помощью мыши, а затем используйте клавишу со стрелкой вправо, чтобы прокрутить экран вправо и увидеть остальную часть меню.
   > 
   > 
4. Откроется страница, аналогичная следующей:
   
    ![Изображение пользовательского интерфейса NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)
   
   > [!NOTE]
   > Обратите внимание на URL-адрес этой страницы. Он должен иметь вид **http://hn1-<имя_кластера>.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Этот адрес использует полное внутреннее доменное имя узла (FQDN), и он недоступен без использования туннеля SSH.
   > 
   > 

## <a name="next-steps"></a>Дальнейшие действия
Теперь когда вы узнали, как создать и использовать туннель SSH, обратитесь к следующим статьям, чтобы узнать об управлении и мониторинге кластера с помощью Ambari:

* [Управление кластерами HDInsight с помощью Ambari](hdinsight-hadoop-manage-ambari.md)

Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях:

* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

<!--HONumber=Oct16_HO2-->


